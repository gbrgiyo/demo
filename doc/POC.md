Deploy one application within one cluster.

Requirements
Install minikube
To install minikube stable release on x86-64 Linux follow these steps:

Download the latest minikube binary:

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
Make the minikube binary executable:

chmod +x minikube
Move the executable binary file to a directory from the PATH environment variable:

sudo install minikube /usr/local/bin/
Kubectl
To manage minikube clusters you need the kubectl tool.

To install kubectl follow these steps:

Download the latest kubectl binary:

curl -LO https://dl.k8s.io/release/`curl -LS https://dl.k8s.io/release/stable.txt`/bin/linux/amd64/kubectl
Make the kubectl binary executable:

chmod +x ./kubectl
Move the executable binary file to a directory from the PATH environment variable:

sudo mv ./kubectl /usr/local/bin/kubectl
Make sure you have the latest version:

kubectl version --client
Verifying minikube installation
To verify that Minikube is installed correctly, run the following command, which starts the local Kubernetes cluster:

minikube start
After the minikube start command has completed successfully, run the command to check the status of the cluster:

minikube status
If your cluster is running, the output from the minikube status command should be something like this:

host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
Show information about the master node and services in the cluster.

kubectl cluster-info
The output of this command should be something like this:

Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
ArgoCD
Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes.

Install ArgoCD
First, let's create the `argocd' namespace in which the system will be installed by default:

kubectl create namespace argocd
To install ArgoCD, we will use the installation YAML file from the official repository:

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Let's check the state of resources and components of the namespace argocd:

kubectl get all -n argocd
Let's check if all containers are working:

kubectl get po -n argocd
The output of this command should be something like this:

NAME                                               READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                    1/1     Running   0          58s
argocd-applicationset-controller-5f975ff5-t9sdd    1/1     Running   0          58s
argocd-dex-server-7bb445db59-78vgl                 1/1     Running   0          58s
argocd-notifications-controller-566465df76-w8wd9   1/1     Running   0          58s
argocd-redis-6976fc7dfc-68s7j                      1/1     Running   0          58s
argocd-repo-server-6d8d59bbc7-4nw55                1/1     Running   0          58s
argocd-server-58f5668765-xv866                     1/1     Running   0          58s
Access Argo CD API Server
By default, the Argo CD API server is not exposed with an external IP.

Let's use port forward to access the ArgoCD API server using the local port 8080:

kubectl port-forward svc/argocd-server -n argocd 8080:443
The API server can then be accessed using https://localhost:8080.

Access password
To get the password for entering the system, use the kubectl get secret <secret_name> command, which returns a BASE64 encoded password, and execute the following command:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo