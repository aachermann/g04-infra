# g04-argocd

## Installation of microk8s (Micro-Kubernetes - SingleNode-Kubernetes)
https://microk8s.io/
```bash
sudo snap install microk8s --classic
echo $USER
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
su - $USER
microk8s status --wait-ready
microk8s kubectl get nodes
microk8s kubectl get services
echo 'alias kubectl="microk8s kubectl"' >> ~/.bashrc
source ~/.bashrc
microk8s enable ingress
```


## Installation of ArgoCD
Ressources from https://github.com/argoproj/argo-cd/

1. CustomRessourceDefinitions
There are some Manifests, we have to apply. 
In the [install folder](./install/) there is the whole manifest for the ArgoCD installation.
```bash
kubectl apply -f manifests/install.yaml
kubectl patch svc argocd-server -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc 
kubectl port-forward svc/argocd-server 8080:443
kubectl get secrets argocd-initial-admin-secret -o yaml
```

2. SealesSecret Controller
https://github.com/bitnami-labs/sealed-secrets
We must have some Credentials on Kubernetes (like GitLab SSH-Key, and so on). These secrets we are not allowed to push to gitlab. So we need an mechanismn encrypt data.
SealedSecret works with an encrypten key on kubernetes cluster. each cluster has it's own key with keyrotation. for every new cluster the sealedsecret has to be encrypted again. 
```bash
microk8s kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.1/controller.yaml
microk8s kubectl get pods -n kube-system -l name=sealed-secrets-controller

# Installing kubeseal-CLI Tool
KUBESEAL_VERSION='0.24.1'
wget "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESEAL_VERSION:?}/kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz"
tar -xvzf kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz kubeseal
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
rm -r kubeseal
rm -r kubeseal-${KUBESEAL_VERSION:?}-linux-amd64.tar.gz

microk8s config > ~/.kube/config/config
export KUBECONFIG=~/.kube/config
kubectl config get-contexts

# Installing kubectl-neat Tool to make the work easyier
wget https://github.com/itaysk/kubectl-neat/releases/download/v2.0.3/kubectl-neat_linux_amd64.tar.gz
tar -xvzf kubectl-neat_linux_amd64.tar.gz
sudo install -m 755 kubectl-neat /usr/local/bin/kubectl-neat
rm -r kubectl-neat_linux_amd64.tar.gz
rm -r kubectl-neat
rm LICENSE



# testing to encrypt a Secret
kubectl create secret generic gitlab-access-token -n default \
--from-literal=token=[YOUR_GITLAB_ACCESS_TOKEN]

kubectl get secret gitlab-access-token -o yaml |kubectl-neat > secret_gitlab-access-token.yaml

kubeseal --format yaml < secret_gitlab-access-token.yaml > base/secrets/sealedsecret_gitlab-access-token.yaml

```

## Probleme ArgoCD <-> Gitlab Connection
```bash
kubectl patch configmap argocd-cm --type merge -p '{"data":{"sshKnownHosts.data": "ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMOoW91G9wYGGHS1A3jKX9S3tgLifxGreSjmAxnV2zUc1wDEcsnyexJ83GgGPlLfhHkf9UhEUL3v89CxgKiZB0M= \n"}}'
```