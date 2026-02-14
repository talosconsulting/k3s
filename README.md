# install 
```bash
sudo dnf install -y https://github.com/k3s-io/k3s-selinux/releases/download/v1.6.latest.1/k3s-selinux-1.6-1.el9.noarch.rpm

sudo curl -Lo /etc/systemd/system/k3s.service https://raw.githubusercontent.com/k3s-io/k3s/refs/heads/main/k3s.service
sudo systemctl daemon-reload
sudo systemctl enable k3s.service

sudo curl -Lo /usr/bin/k3s https://github.com/k3s-io/k3s/releases/download/v1.35.0%2Bk3s3/k3s
sudo chmod +x /usr/bin/k3s

mkdir -p /etc/rancher/k3s/
echo 'default-local-storage-path: "/mnt/spare/k3s/"' | sudo tee /etc/rancher/k3s/config.yaml

sudo /usr/bin/k3s server \
  --bind-address 192.168.1.2 \
  --tls-san '*.lan,192.168.1.2' \
  --write-kubeconfig /mnt/raid/dev/ansible/roles/fedora-developer/files/config \
  --write-kubeconfig-mode 0644 \
  --default-local-storage-path /mnt/spare/k3s/
```

## smoketest
```bash
k apply -k .
# namespace/smoketest created
# service/smoketest-service created
# persistentvolumeclaim/smoketest-pvc created
# deployment.apps/smoketest-deployment created
# ingress.networking.k8s.io/smoketest-ingress created

curl http://smoketest.lan
# <!DOCTYPE html>
# <html>
# <head>
# <title>Welcome to nginx!</title>
# ...
```
## uninstall
```bash
sudo systemctl disable --now k3s.service
sudo /usr/bin/k3s server --cluster-reset
sudo rm -rf /var/lib/rancher/k3s/
```
