# Argo CD

[Argo CD](https://argoproj.github.io/cd/) is a declarative, GitOps continous delivery tool for Kubernetes.

In our workshop, we will use Argo CD to deploy our microservices and resources. 

## Install Argo CD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Expose the Argo CD API Server
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": 
"LoadBalancer"}}'
```

## Expose the Argo CD Server API/UI
Save the following manifest to argocd-nodeport.yml
```yml
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: server
    app.kubernetes.io/name: argocd-server
    app.kubernetes.io/part-of: argocd
  name: argocd-server-nodeport
  namespace: argocd
spec:
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 8080
      nodePort: 30007
    - name: https
      port: 443
      protocol: TCP
      targetPort: 8080
      nodePort: 30008
  selector:
    app.kubernetes.io/name: argocd-server
  sessionAffinity: None
  type: NodePort
```
Now, apply the manifest:
```bash
kubectl apply -f argocd-nodeport.yml
```

## Obtain the Argo CD password 

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

## Login
Use the Argo CD UDF Access Method to access the Argo CD UI and login with the `admin` user and the password you obtain in the previous step.