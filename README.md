# Installing Jenkins inside Kubernetes

Step-by-step guide how to install Jenkins in the Kubernetes cluster and run Jenkins agents inside that cluster.

## Create Kubernetes namespace

`kubectl create namespace jenkins`

## Apply service account configuration

`kubecl apply -f config/jenkins/service-account.yaml`

## Apply Jenkins deployment configuration

`kubectl apply -f config/jenkins/jenkins.yaml`
