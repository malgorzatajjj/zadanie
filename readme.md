#instalacja rancher desktop

curl -s https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/Release.key | gpg --dearmor | sudo dd status=none of=/usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg] https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/ ./' | sudo dd status=none of=/etc/apt/sources.list.d/isv-rancher-stable.list
sudo apt update
sudo apt install rancher-desktop


#instalacja kubectl
sudo snap install kubectl --classic

#instalacja helm chart
sudo snap install helm --classic

#instalacja k3d
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

#utworzenie klastra 
k3d cluster create cluster --agents 2
kubectl config use-context k3d-cluster


#utworzenie namespaces ingress-ngnix, minio, nginx
kubectl create namespace ingress-nginx
kubectl create namespace minio
kubectl create namespace nginx

#instalacja ingress-nginx, minio, nginx bitnami

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx -f ingress-values.yaml

helm repo add minio https://charts.min.io/
helm repo update
helm install -n minio -f minio-values.yaml --generate-name minio/minio 

helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
kubectl apply -f cm-nginx.yaml -n nginx 
helm install nginx bitnami/nginx -f nginx-values.yaml -n nginx


#skopiowanie obrazów

mc alias set minio http://minio.local:8080 login haslo

mc cp 1.jpg minio/galeria

#dodanie hostów
sudo nano /etc/hosts
127.0.0.1 frontend.local minio.local console.minio.local

#przekierowanie ingress loadbalancer

kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80





