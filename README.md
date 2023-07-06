# Rancher's k3s - First steps

Lightweight, easy, fast Kubernetes distribution with a very small footprint

https://k3s.io or https://k3d.io for the dockerized version of k3s.

# K3s - Lightweight Kubernetes

Lightweight Kubernetes. Easy to install, half the memory, all in a binary of less than 100 MB.

Great for:

Edge
IoT
CI
Development
ARM
Embedding K8s
Situations where a PhD in K8s clusterology is infeasible

# What is K3s?

K3s is a fully compliant Kubernetes distribution with the following enhancements:

Packaged as a single binary.
Lightweight storage backend based on sqlite3 as the default storage mechanism. etcd3, MySQL, Postgres are also available.
Wrapped in simple launcher that handles a lot of the complexity of TLS and options.
Secure by default with reasonable defaults for lightweight environments.
Simple but powerful "batteries-included" features have been added, such as:
local storage provider
service load balancer
Helm controller
Traefik ingress controller.
Operation of all Kubernetes control plane components is encapsulated in a single binary and process. This allows K3s to automate and manage complex cluster operations like distributing certificates.
External dependencies have been minimized (just a modern kernel and cgroup mounts needed). K3s packages the required dependencies, including:
containerd
Flannel (CNI)
CoreDNS
Traefik (Ingress)
Klipper-lb (Service LB)
Embedded network policy controller
Embedded local-path-provisioner
Host utilities (iptables, socat, etc)

# What's with the name?

We wanted an installation of Kubernetes that was half the size in terms of memory footprint. Kubernetes is a 10-letter word stylized as K8s. So something half as big as Kubernetes would be a 5-letter word stylized as K3s. There is no long form of K3s and no official pronunciation.

## Demo

I'll use Linux Instance For that Demo.

### Install Master
By default K3s using the traefik so we gonna disable the traefik and using the Nginx-ingress.

`curl -sfL https://get.k3s.io | sh -s - --disable traefik` 

### Copy the kubeconfig to run the kubectl command
`cp /etc/rancher/k3s/k3s.yaml ~/.kube/config`
OR
`export KUBECONFIG=/etc/rancher/k3s/k3s.yaml`
`kubectl get pods --all-namespaces`

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



