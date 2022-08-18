# Installing Jenkins inside Kubernetes

Step-by-step guide how to install Jenkins in the Kubernetes cluster and run Jenkins agents inside that cluster.

For minikube install ingress controller with following command:

`minikube addons enable ingress`

## Create Kubernetes namespace

`kubectl create namespace jenkins`

## Apply service account configuration

`kubecl apply -f config/jenkins/service-account.yaml`

## Apply Jenkins deployment configuration

`kubectl apply -f config/jenkins/jenkins.yaml`

## Apply Jenkins ingress configuration

`kubectl apply -f jenkins-ingress.yaml`

## Get minikube ip address and add jenkins.example.com to /etc/hosts file

`echo "$(minikube ip) jenkins.example.com" >> /etc/hosts`

## Log into Jenkins and create new user/or update password for 'admin'

## Install 'Kubernetes' Jenkins plugin

## Tune up Kubernetes plugin

Navigate to 'Manage Jenkins' -> 'Manage Nodes and Clouds' -> 'Configure Clouds'

Press 'Add new cloud' and select 'Kubernetes'

Press 'Kubernetes Cloud details...' button to configure Kubernetes plugin

1. Under 'Kubernetes Namespace' write the namespace that was previously configured. In my case it is 'jenkins'.
Press 'Test Connection' button - 'Connected to Kubernetes vX.XX.XX' should appear.

2. Config 'Jenkins URL' field.
It should combined from following info: `http://<service-name>.<namespace>.svc.cluster.local:8080`. To check service name just issue following command: `kubectl get svc -n jenkins`. In my case it looks as: `http://jenkins-service.jenkins.svc.cluster.local:8080`

3. Under 'Pod Labels' section press 'Add Pod Label' button and select 'Pod Label'. Fill 'Key' - 'Jenkins' and 'Value' - 'agent'

4. Under 'Pod Templates' press 'Add Pod Template' button and 'Pod Template Details...' button. Fill the following fields with info:
    - 'Name' - 'kube-agent'
    - 'Namespace' - 'jenkins'
    - 'Labels' - 'kubeagent'

5. Under 'Containers' press 'Add Container' button and select 'Container Template'. Fill 'Name' - 'jnlp' and 'Docker image' - 'jenkins/inbound-agent:latest'. Remove values from 'Command to run' and 'Arguments to pass to the command' fields.

Now Jenkins will build jobs that have label 'kubeagent' inside container.

## Apply prometheus and grafana configs in the next order

*Prometheus:*

clusterRole.yaml

config-map.yaml

prometheus-deployment.yaml

prometheus-service.yaml

prometheus-ingress.yaml

*Grafana:*

grafana-datasource-config.yaml

grafana-deployment.yaml

grafana-service.yaml

grafana-ingress.yaml

## Add ingress addresses to /etc/hosts

`echo "$(minikube ip) prometheus.example.com" >> /etc/hosts`

`echo "$(minikube ip) grafana.example.com" >> /etc/hosts`

## Verify Services

<http://jenkins.example.com>

<http://prometheus.example.com>

<http://grafana.example.com>
