# Demo Notes

Instructions and commands for the demo.

## Init Argo-CD

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Disable auth/tls

```sh
kubectl --namespace=argocd edit deployments.apps argocd-server
```

Add flags to the server:

```yaml
        - --insecure=true
        - --disable-auth=true
```

## Port-Forward

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:80
```

## Demo References

### Repos to add

- `https://github.com/argoproj/argocd-example-apps.git`
- `https://github.com/carsonoid/talk-k8s-and-argocd.git`

### Bootstrap App

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: bootstrap
spec:
  project: default
  source:
    repoURL: 'https://github.com/carsonoid/talk-k8s-and-argocd.git'
    path: argo-examples/bootstrap
    targetRevision: HEAD
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: argocd
```

### Ingress Demo

Use curl to check that the hostname routing works and that the site is served via https.

```sh
curl -k -H 'Host: argocd-nginx.local' https://$(kubectl get services --namespace=kube-system traefik --output jsonpath='{.status.loadBalancer.ingress[0].ip}')
```

Add an entry to `/etc/hosts` to make browsers work

```sh
echo "$(kubectl get services --namespace=kube-system traefik --output jsonpath='{.status.loadBalancer.ingress[0].ip}') argocd-nginx.local" |sudo tee --append /etc/hosts
```

Then go to https://argocd-nginx.local/
