# Here are some Problems and Learnings - Infrastructure Based

## Probleme ArgoCD <-> Gitlab Connection
the connection to the gitlab on switch could not be configured successfully. since testing was first done on local microk8s instances, the quick workaround was to move the infrastructure repo to github. No further troubleshooting was carried out.

## SSH-Tunneling
Since not all ports are open on the VM, other mechanisms had to be used. to call ArgoCD locally, we set up the SSH tunnel. great learning - 

Thanks go to the lecturers! 

so useful - why not earlier!?

```bash
# ~/.ssh/config
Host devops-10
  HostName 86.119.42.170
  User student
  IdentityFile ~\.ssh\id_rsa
```

```bash
 ssh -L 8080:localhost:31318 student@devops-10
```

## Ingress
