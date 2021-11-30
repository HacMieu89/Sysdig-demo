# Sysdig-demo

You will install Falco using Helm (already installed in the cluster), a package manager for Kubernetes.

helm repo add falcosecurity https://falcosecurity.github.io/charts
helm repo update
kubectl create ns falco
helm install falco -n falco falcosecurity/falco
