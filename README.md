URL : https://infotechys.com/kubernetes-development-environment-with-vagrant/
# Vagrantfile and Scripts to Automate Kubernetes Setup using Kubeadm [Practice Environment for CKA/CKAD and CKS Exams]

A fully automated setup for CKA, CKAD, and CKS practice labs is tested on the following systems:

- Windows
- Ubuntu Desktop
- Mac Intel-based systems

If you are MAC Silicon user, Please use the following repo.

- [Vagrant Kubeadm Setup on MAC Silicon](https://github.com/techiescamp/vagrant-kubeadm-mac-silicon)

## Kubernetes Certification Voucher (UpTo 38% OFF) 🎉

If you are learning Kubernetes and preparing for Kubernetes certifications, these voucher codes will help you save money on your certification registration.

CKA, CKAD, CKS, KCNA etc.. aspirants can **save 30%** today

> [!IMPORTANT]
> Use code **DCUBE30** at https://kube.promo/devops. It is a limited-time offer from the Linux Foundation.

Use code **DCUBE30** with the following bundles to **save upto 38%**

- CKA + CKAD: [kube.promo/cka-ckad](https://kube.promo/cka-ckad)
- CKA + CKS Bundle: [kube.promo/bundle](https://kube.promo/bundle)
- CKA + CKAD + CKS Exam bundle: [kube.promo/k8s-bundle](https://kube.promo/k8s-bundle)

> Checkout all the latest bundle offers at [Linux Foundation Coupon](https://github.com/techiescamp/linux-foundation-coupon) repo.

> [!NOTE]
>⌛ Act fast—this limited-time offer won’t be around much longer!
> You have one year of validity to appear for the certification exam after registration

## Organized Kubernetes & CKA Learning

If you Looking for an organized way to learn Kubernetes and prepare for the CKA exam with practice questions? 

> Check out our [Complete CKA Certification Course](https://courses.devopscube.com/p/cka-complete-prep-course-practice-tests). 

It includes illustrations, hands-on exercises, real-world examples, and dedicated Discord support. 

> [!NOTE]
>⌛ For a lmited time, use code **DCUBE30** to get 30% OFF today!

## Setup Prerequisites

- A working Vagrant setup using Vagrant + VirtualBox

Here is the high level workflow.


<p align="center">
  <img src="https://github.com/user-attachments/assets/cc5594b5-42c2-4c56-be21-6441f849f537" width="65%" />
</p>

## Documentation

Current k8s version for CKA, CKAD, and CKS exam: 1.30

The setup is updated with 1.31 cluster version.

Refer to this link for documentation full: https://devopscube.com/kubernetes-cluster-vagrant/


## Prerequisites

1. Working Vagrant setup
2. 8 Gig + RAM workstation as the Vms use 3 vCPUS and 4+ GB RAM

## For MAC/Linux Users

The latest version of Virtualbox for Mac/Linux can cause issues.

Create/edit the /etc/vbox/networks.conf file and add the following to avoid any network-related issues.
<pre>* 0.0.0.0/0 ::/0</pre>

or run below commands

```shell
sudo mkdir -p /etc/vbox/
echo "* 0.0.0.0/0 ::/0" | sudo tee -a /etc/vbox/networks.conf
```

So that the host only networks can be in any range, not just 192.168.56.0/21 as described here:
https://discuss.hashicorp.com/t/vagrant-2-2-18-osx-11-6-cannot-create-private-network/30984/23

## Bring Up the Cluster

To provision the cluster, execute the following commands.

```shell
git clone https://github.com/scriptcamp/vagrant-kubeadm-kubernetes.git
cd vagrant-kubeadm-kubernetes
vagrant up
```
## Set Kubeconfig file variable

```shell
cd vagrant-kubeadm-kubernetes
cd configs
export KUBECONFIG=$(pwd)/config
```

or you can copy the config file to .kube directory.

```shell
cp config ~/.kube/
```

## Install Kubernetes Dashboard

The dashboard is automatically installed by default, but it can be skipped by commenting out the dashboard version in _settings.yaml_ before running `vagrant up`.

If you skip the dashboard installation, you can deploy it later by enabling it in _settings.yaml_ and running the following:
```shell
vagrant ssh -c "/vagrant/scripts/dashboard.sh" controlplane
```

## Kubernetes Dashboard Access

To get the login token, copy it from _config/token

Port forward:
```shell
kubectl -n kubernetes-dashboard port-forward svc/kubernetes-dashboard-kong-proxy 8443:443
```

Open the site in your browser:
```shell
https://localhost:8443
```

## To shutdown the cluster,

```shell
vagrant halt
```

## To restart the cluster,

```shell
vagrant up
```

## To destroy the cluster,

```shell
vagrant destroy -f
```
# Network graph

```
                  +-------------------+
                  |    External       |
                  |  Network/Internet |
                  +-------------------+
                           |
                           |
             +-------------+--------------+
             |        Host Machine        |
             |     (Internet Connection)  |
             +-------------+--------------+
                           |
                           | NAT
             +-------------+--------------+
             |    K8s-NATNetwork          |
             |    192.168.99.0/24         |
             +-------------+--------------+
                           |
                           |
             +-------------+--------------+
             |     k8s-Switch (Internal)  |
             |       192.168.99.1/24      |
             +-------------+--------------+
                  |        |        |
                  |        |        |
          +-------+--+ +---+----+ +-+-------+
          |  Master  | | Worker | | Worker  |
          |   Node   | | Node 1 | | Node 2  |
          |192.168.99| |192.168.| |192.168. |
          |   .99    | | 99.81  | | 99.82   |
          +----------+ +--------+ +---------+
```

This network graph shows:

1. The host machine connected to the external network/internet.
2. The NAT network (K8s-NATNetwork) providing a bridge between the internal network and the external network.
3. The internal Hyper-V switch (k8s-Switch) connecting all the Kubernetes nodes.
4. The master node and two worker nodes, each with their specific IP addresses, all connected to the internal switch.


run test application:

Step 1: The Login Page Application CodeIf you do not have your login code written yet, you can use this lightweight Node.js/Express application. It serves a static HTML login form and a simple authentication API.server.js (Backend Logic)javascriptconst express = require('express');
const path = require('path');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Serve the HTML file
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'));
});

// Authentication endpoint
app.post('/login', (req, res) => {
    const { username, password } = req.body;
    // Replace this with your actual database authentication logic
    if (username === 'admin' && password === 'password123') {
        res.status(200).json({ message: "Login successful!" });
    } else {
        res.status(401).json({ error: "Invalid credentials" });
    }
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
Use code with caution.index.html (Frontend Form)html<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login Page</title>
    <style>body { font-family: Arial; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; background: #f0f2f5; } form { background: white; padding: 30px; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }</style>
</head>
<body>
    <form action="/login" method="POST">
        <h2>Login Portal</h2>
        <input type="text" name="username" placeholder="Username" required><br><br>
        <input type="password" name="password" placeholder="Password" required><br><br>
        <button type="submit">Sign In</button>
    </form>
</body>
</html>
Use code with caution.Step 2: Containerize the CodeKubernetes cannot run raw code directly; it requires a container image. Use this configuration to containerize the app.DockerfiledockerfileFROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY server.js index.html ./
EXPOSE 3000
CMD ["node", "server.js"]
Use code with caution.Build and Push CommandsRun these commands in your local terminal to build the container and send it to an image repository like Docker Hub:bash# Build the docker container
docker build -t your-dockerhub-username/login-page:v1 .

# Upload it to your registry
docker push your-dockerhub-username/login-page:v1
Use code with caution.Step 3: Kubernetes Deployment ManifestsCreate a file named login-deployment.yaml. This file tells Kubernetes how to pull your login page container, run 3 replica pods of it, and expose it internally over port 3000.login-deployment.yamlyamlapiVersion: apps/v1
kind: Deployment
metadata:
  name: login-page-deployment
  labels:
    app: login-page
spec:
  replicas: 3
  selector:
    matchLabels:
      app: login-page
  template:
    metadata:
      labels:
        app: login-page
    spec:
      containers:
      - name: login-app
        image: your-dockerhub-username/login-page:v1 # Replace with your real image link
        ports:
        - containerPort: 3000
        resources:
          limits:
            cpu: "500m"
            memory: "256Mi"
          requests:
            cpu: "250m"
            memory: "128Mi"
Use code with caution.Step 4: Expose the Login Page to the WebTo route internet traffic to your application pods, define a Network Service. Create a file named login-service.yaml.login-service.yamlyamlapiVersion: v1
kind: Service
metadata:
  name: login-page-service
spec:
  type: LoadBalancer # Change to NodePort if testing locally on Minikube or MicroK8s
  selector:
    app: login-page
  ports:
    - protocol: TCP
      port: 80         # The port accessible to internet traffic
      targetPort: 3000 # The port your application container listens to
Use code with caution.Step 5: Deploy and AccessExecute the following commands sequentially using the Kubernetes Command Line Tool (kubectl) to run the infrastructure:bash# 1. Apply the application deployment configuration
kubectl apply -f login-deployment.yaml

# 2. Apply the routing service configuration 
kubectl apply -f login-service.yaml

# 3. Monitor the deployment progress until status is running
kubectl get pods

# 4. Extract the external network IP address assigned to your app
kubectl get service login-page-service
Use code with caution.

cat <<EOF > package.json
{
  "name": "login-page",
  "version": "1.0.0",
  "description": "Simple K8s login app",
  "main": "server.js",
  "dependencies": {
    "express": "^4.19.2"
  }
}
EOF
