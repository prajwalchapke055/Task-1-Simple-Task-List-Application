###############################################################

# KUBERNETES

--> Launch an new EC2 instance (3 machines)
--> Connect to Putty with Key Pairs all 3 machines 
--> Ping all host Machine one by one 

----------------sudo nano /etc/hosts    ENTER ENTRIES FOR BOTH THE MACHIENS----------------------------------------------------------
- $ sudo nano /etc/hosts
    - 172.31.25.162    ip-172-31-25-162
	  - 172.31.16.33     ip-172-31-16-33
	  - 172.31.16.190    ip-172-31-16-190

- $ nano k8.sh 
-->
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install docker.io -y
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

- $ sudo chmod u+x k8.sh
- $ ./k8.sh

==============================================================================

## Now on MASTER NODE

- $ sudo kubeadm init --apiserver-advertise-address=$IP --pod-network-cidr=192.168.100.0/24
- $ sudo kubeadm reset [If Error Occurs , then run above command]

--> Note : Copy the token and store in Notepad

- $ mkdir -p $HOME/.kube 
- $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
- $ sudo chown $(id -u):$(id -g) $HOME/.kube/config

- $ sudo systemctl restart kublet
- $ sudo systemctl status kublet
- $ sudo kubectl get nodes

- $ ss -antp 
- $ ss -antp | grep "6443" 
    --> LISTEN : 6443 [if not present]
- $ sudo systemctl restart kublet

## Now on WORKER NODE 

--> Paste the command in terminal with
    " sudo " starting for token , that we have stored in notepad


- $ sudo reset kublet
- $ sudo reset kubeadm 

- $ sudo kubeadm join 172.--- 

## Now on MASTER NODE 

- $ kubectl get nodes 
    --> - It will show 3 nodes , Check Status --> Running 

- $ kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml

- $ kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml


watch kubectl get pods -n calico-system
CALICO / FLANNEL
After successful running

## Create the yaml files and run the below commands.

- $ nano nginx-deployment.yaml
--> 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest  # Replace with your custom image if necessary
        ports:
        - containerPort: 80

- $ nano nginx-service.yaml
-->

apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer  # Or use ClusterIP if you don't need external access


- $ kubectl apply -f nginx-deployment.yaml
- $ kubectl apply -f nginx-service.yaml

--> Reference Link : https://chaitannyaa.hashnode.dev/step-by-step-guide-installing-setting-up-and-deploying-applications-on-kubeadm#heading-step-1-make-at-least-three-machinesnodes-to-form-a-multi-node-kubernetes-cluster


###################################################################
             Kubernetes Deployment - kubeadm
###################################################################


Launch 3 nodes (1 master and 2 Worker)

-----*** Initialise Hostnames ***-----

$ sudo hostnamectl set-hostname k8s-master

$ sudo hostnamectl set-hostname k8s-worker1

$ sudo hostnamectl set-hostname k8s-worker2

$ sudo nano /etc/hosts
172.31.45.163 m1
172.31.42.84 w1
172.31.44.232 w2

>>> Exit node and connect again


-----*** Set up Docker Engine and containerd ***-----

>>> Load and enable kernel modules

$ cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

$ sudo modprobe overlay

$ sudo modprobe br_netfilter

>>> Iptable configs

$ cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

$ sudo sysctl --system

$ sudo apt-get update && sudo apt-get install -y ca-certificates curl gnupg lsb-release apt-transport-https

$ sudo mkdir -m 0755 -p /etc/apt/keyrings

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt-get update

$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

$ sudo usermod -aG docker $USER

>>> Make sure that 'disabled_plugins' is commented out in your config.toml file

$ sudo sed -i 's/disabled_plugins/#disabled_plugins/' /etc/containerd/config.toml

$ sudo systemctl restart containerd

>>> Disable swap

$ sudo swapoff -a


-----*** Install kubadm, kubectl and kubelet ***-----

$ sudo apt-get install -y apt-transport-https ca-certificates curl gpg

$ curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

$ echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

$ sudo apt-get update

$ sudo apt-get install -y kubelet kubeadm kubectl

$ sudo apt-mark hold kubelet kubeadm kubectl


>>> On the Control pane (master) - Initialise the kubernetes cluster

$ sudo kubeadm init --pod-network-cidr 192.168.0.0/16 


>>> Make kubectl work with non root user
$ mkdir -p $HOME/.kube

$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

$ sudo chown $(id -u):$(id -g) $HOME/.kube/config


>>> Verify if clutser is running
$ kubectl get nodes


>>> Install The Flannel network add-on
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

>>> Get join command for workers
$ kubeadm token create --print-join-command

(Paste on all workers)


>>> Check the cluster
$ kubectl get nodes


####################################################################