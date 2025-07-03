# ZADANIE

## 1. Instalacja rancher desktop

`curl -s https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/Release.key | gpg --dearmor | sudo dd status=none of=/usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg`<br><br>
`echo 'deb [signed-by=/usr/share/keyrings/isv-rancher-stable-archive-keyring.gpg] https://download.opensuse.org/repositories/isv:/Rancher:/stable/deb/ ./' | sudo dd status=none of=/etc/apt/sources.list.d/isv-rancher-stable.list`<br><br>
`sudo apt update`<br><br>
`sudo apt install rancher-desktop`

## 2. Instalacja kubectl
`sudo snap install kubectl --classic`

## 3. Instalacja helm chart
`sudo snap install helm --classic`

## 4.Instalacja k3d
`curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash`

## 5. Utworzenie klastra 
`k3d cluster create cluster --agents 2`<br><br>
`kubectl config use-context k3d-cluster`

## 6. Utworzenie namespaces ingress-ngnix, minio, nginx
`kubectl create namespace ingress-nginx`<br><br>
`kubectl create namespace minio`<br><br>
`kubectl create namespace nginx`

## 7. Instalacja ingress-nginx, minio, nginx bitnami
`helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`<br><br>
`helm repo update`

`helm install ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx -f ingress-values.yaml`

`helm repo add minio https://charts.min.io/`<br><br>
`helm repo update`<br><br>
`helm install -n minio -f minio-values.yaml --generate-name minio/minio` 

`helm repo add bitnami https://charts.bitnami.com/bitnami`<br><br>
`helm repo update`<br><br>
`kubectl apply -f cm-nginx.yaml -n nginx` <br><br>
`helm install nginx bitnami/nginx -f nginx-values.yaml -n nginx`

## 8. Skopiowanie obrazów
`mc alias set minio http://minio.local:8080 login haslo`<br><br>
`mc cp 1.jpg minio/galeria`

## 9. Dodanie hostów
`sudo nano /etc/hosts`<br><br>
`127.0.0.1 frontend.local minio.local console.minio.local`

## 10. Przekierowanie ingress loadbalancer
`kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80`





