# sd2660_k8s

kubectl create namespace argocd
kubectl apply -n argocd -f  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl  -n argocd get secret argocd-initial-admin-secret -o jsonpath=”{.data.password}” 
kubectl port-forward svc/argocd-server -n argocd 8080:443

kubectl create namespace app-argocd

Create new project

argocd login localhost:8080

argocd app create sd2660-devops \
    --repo https://github.com/nhatbao47/sd2660_k8s.git \
    --path charts/my-chart \
    --helm-chart my-chart \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace my-namespace \
    --sync-policy automated

argocd app create demo \
  --repo https://github.com/nhatbao47/sd2660_k8s.git \
  --path "." \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace app-argocd

kubectl port-forward -n app-argocd svc/frontend 3000:3000

kubectl delete namespace argocd
kubectl delete namespace app-argocd