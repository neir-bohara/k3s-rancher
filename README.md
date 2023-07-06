# Rancher's k3s - First steps

Lightweight, easy, fast Kubernetes distribution with a very small footprint

https://k3s.io or https://k3d.io for the dockerized version of k3s.

## Demo

I'll use Linux Instance For that Demo.

### Install Master
By default K3s using the traefik so we gonna disable the traefik and using the Nginx-ingress.

`curl -sfL https://get.k3s.io | sh -s - --disable traefik` 

### Install Worker

Grab token from the master node to be able to add worked nodes to it: 

`cat /var/lib/rancher/k3s/server/node-token`

Install k3s on the worker node and add it to our cluster:

`curl -sfL https://get.k3s.io | K3S_NODE_NAME=<worker-node name> K3S_URL=https://<masterserver-IP>:6443 K3S_TOKEN=<TOKEN> sh - `

### Install nginx ingress controller


Website: https://kubernetes.github.io/ingress-nginx/

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml`

#### Important note for AWS, Azure, GCP

In the above cloud provider 'nginx-ingress' take the private IP as external IP. So you need to manually edit and change the public IP address available for nginx, so it can be listening on your public IP address.
`kubectl -n ingress-nginx edit svc ingress-nginx-controller`

Add your IP as list to `spec.externalIPs` like so:

```
  spec:
    externalIPs:
    - <YOUR_IP>
```

### Additional information

You can change the settings of k3s  with `vim /etc/systemd/system/k3s.service`.

Make sure to restart the service: `systemctl restart k3s`

### Deploy demo nginx application

`kubectl apply -f deployment.yaml`



