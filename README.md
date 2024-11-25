# sd2660_k8s

# Connect to EKS in terminal for kubectl
aws configure
aws eks --region ap-southeast-1 update-kubeconfig --name sd2660-devops-eks

# Install ArgoCD

kubectl create namespace argocd

kubectl apply -n argocd -f  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath='{.data.password}' | base64 --decode; echo

kubectl port-forward svc/argocd-server -n argocd 8080:443

# Launch application

kubectl create namespace app-argocd

kubectl apply -f argocd-application.yaml

kubectl port-forward -n app-argocd svc/frontend 3000:3000

argocd login localhost:8080 --username admin --password 12345678 --insecure

Argo CD Image Updater
#install
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/stable/manifests/install.yaml

kubectl get pods -n app-argocd -o jsonpath="{.items[*].spec.containers[?(@.name=='backend')].image}"


#apply config

kubectl apply -f manifests/argocd-image-updater-cm.yaml

kubectl rollout restart deployment/argocd-image-updater -n argocd

kubectl logs -f deployment/argocd-image-updater -n argocd