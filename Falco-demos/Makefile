#Minikube Calico K8S falco Dev-ing.
MINIKUBE_MEM ?= 6144
MINIKUBE_CPU ?= 6

##
## TODO: Add logic to move into helm/routing dir in the next PR.
##
launch-minikube: ## Launch Minikube
	minikube start --memory=${MINIKUBE_MEM} --cpus=${MINIKUBE_CPU} --network-plugin=cni

install-falco: ## Install falco kubectl logs -l app=falco -f
	@echo Installing falco
	helm repo add falcosecurity https://falcosecurity.github.io/charts
	helm repo update
	helm install falco -f helm-values/mini-kube.yaml -f rules/custom.yaml falcosecurity/falco
	minikube ssh "sudo modprobe falco-probe"
	minikube ssh "lsmod | grep -i falco"

install-falco-custome-rules: ## Upgarde and install custome rules in real time
	helm upgrade falco -f helm-values/custom.yaml falcosecurity/falco
	kubectl logs $(kubectl get po | grep falco | awk '{ print $1 }') -f
	@kubectl exec -it $(kubectl get po | grep falco | awk '{ print $1 }') -- /bin/sh
	
install-falco-exporter: ## Install falco-exporter 
	@kubectl port-forward falco-exporter-mh56r 45679:9376
	@curl http://localhost:45679/metrics
	helm install falco-exporter falcosecurity/falco-exporter

install-prometheus: ## Insall promethus to frontend metrics
	helm install prom stable/prometheus
	@kubectl port-forward prom-prometheus-server-75b8b868d7-mf4vj  9090

install-graphana: ## install grapnana
	helm install grafana stable/grafana
	@get secret
	kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
	@kubectl port-forward service/grafana 3000:80
	@ Add prom as mport http://prom-prometheus-server.default.svc.cluster.local
	@import chart at grapha https://grafana.com/grafana/dashboards/11914

install-elasticsearch: ## Insall Elastic Search Cluster
	helm repo add elastic https://helm.elastic.co
	helm upgrade --install --values helm-values/elasticsearch-values.yml elasticsearch elastic/elasticsearch

install-kibana: ## Insall Kibana
	helm upgrade --install kibana elastic/kibana --values helm-values/kibana-values.yaml

install-fluentd: ## Insall Fluentd
	helm repo add kiwigrid https://kiwigrid.github.io
	helm upgrade --install --values helm-values/fluentd-values.yaml fluentd kiwigrid/fluentd-elasticsearch

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'
