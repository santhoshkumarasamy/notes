Setup K3d and docker
~~~
#https://docs.docker.com/engine/install/ubuntu/
#https://k3d.io/stable/#installation
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash

apiVersion: k3d.io/v1alpha2
kind: Simple
servers: 1
agents: 2


# Create a cluster
k3d cluster create argocd-cluster --config ./cluster-config.yaml

~~~

## ArgoCD Components

* API Server
* Repository Service
* Application Controller

The Api server exposes gRPC apis
repository service manages connnections with git repository
application controller is a kubernetes controller which syncs the desired state of the application with the current live state

Argo CD have UI and CLI to communicate with

Since ArgoCD have gRPC api endpoints through API Server other clients can also interact with it

### API Server
* Manages credentials
* Application operations
* Authentications
* RBAC
* Listener & forwarder for Github WebHook events
### Repository Service
* Contains local cache of the git repository
* Contains application manifest
### Application Controller
* It is a kubernetes controller
* continuously monitors the current state
* Compares it with the state mentioned in repo
* Invokes user-defined hooks for lifecycle events

## Setting up argocd

~~~
# Create a namespace

kubectl create ns argocd
# https://argo-cd.readthedocs.io/en/stable/getting_started/
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Install argocd cli

# https://argo-cd.readthedocs.io/en/stable/cli_installation/

curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64


argocd version

argocd version --client
# this with allow any client to connect to port 8080
kubectl port-forward --address 0.0.0.0 svc/argocd-server -n argocd 8080:443

# get the initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo

#connect argocd cli with the server

argocd login localhost:8080

# to change the password

argocd account update-password

# adding new cluster to argocd

argocd cluster add k3d-dev-cluster --name dev-cluster # here k3d-dev-cluster is actually the kubectl context

# list current projects in argocd

argocd proj list

# to list clusters
argocd cluster list

# to list repos connected
argocd repo list

# to list apps
argocd app list

# to add a new project in argocd
argocd proj create dev-argocd -d https://172.31.6.250:34005,default -s https://github.com/santhoshkumarasamy/argo-cd-learning

# to add repo source to a project

argocd proj add-source dev-argocd https://github.com/santhoshkumarasamy/argo-cd-learning # name of the project and url of the repo

~~~
