# Sysdig-demo

# You will install Falco using Helm (already installed in the cluster), a package manager for Kubernetes.

helm repo add falcosecurity https://falcosecurity.github.io/charts

helm repo update

kubectl create ns falco

helm install falco -n falco falcosecurity/falco

# You will create three pods (client, mysql, and ping) for this workshop:

kubectl create namespace ping

kubectl create -f mysql-deployment.yaml --namespace=ping

kubectl create -f mysql-service.yaml --namespace=ping

kubectl create -f ping-deployment.yaml --namespace=ping

kubectl create -f ping-service.yaml --namespace=ping

kubectl create -f client-deployment.yaml --namespace=ping

# you will add a few custom rules to Falco

helm upgrade falco falcosecurity/falco -f custom_rules.yaml -n falco
