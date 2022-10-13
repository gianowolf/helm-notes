## Docker & microk8s

```sh
sudo snap install microk8s --classic

sudo usermod -a -G microk8s gfl
sudo chown -f -R gfl ~/.kube

microk8s status --wait-ready

microk8s enable dashboard dns registry istio
microk8s enable dashboard dns registry istio # turn on services you want

microk8s kubectl get all --all-namespaces

microk8s dashboard-proxy

microk8s start
microk8s stop
```

```sh
sudo addgroup --system docker
sudo adduser $USER docker 
newgrp docker 
sudo snap disable docker
sudo snap enable docker 
```

