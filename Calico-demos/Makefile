#Minikube Calico K8S Dev-ing.
MINIKUBE_MEM ?= 6144
MINIKUBE_CPU ?= 6
NAMESPACE ?= default
##
## TODO: Add logic to move into helm/routing dir in the next PR.
##
default-deny: ## Apply Default Deny
	kubectl apply -f backend_deny.yaml
	kubectl apply -f frontend_deny.yaml	
	## apply -f egress-ip.yaml
  ## kubectl apply -f allow-http-access.yaml
	## kubectl apply -f lates_policy/allow_frontend_backend.yaml
  ## kubectl label namespace frontend app=frontend
	## kubectl label namespace backend app=backend
	## Clean up
	## kubectl -n backend delete networkpolicy allow-backend-egress-external
	## kubectl -n backend delete networkpolicy allow-frontend-backend
	## kubectl -n backend delete networkpolicy default-deny
	## kubectl -n frontend delete networkpolicy default-deny
	## kubectl -n frontend delete networkpolicy allow-frontend-backend

install-calicoctl: ## Installing calicoctl as a Kubernetes pod 
	# kubectl exec -ti -n kube-system calicoctl -- /calicoctl get profiles -o  wide
	kubectl apply -f https://docs.projectcalico.org/manifests/calicoctl.yaml

setup-lab: ## Install nginx deployment objects in default namespace
	kubectl create namespace backend
	kubectl create namespace frontend
	kubectl create deployment -n frontend hello-world-nodeport1 --image=gcr.io/google-samples/hello-app:1.0
	kubectl create deployment -n backend hello-world-nodeport2 --image=gcr.io/google-samples/hello-app:1.0
	## apply -f egress-ip.yaml
  
ip-egress: ## Allow Egress icmp
	kubectl apply -f  egress-ip.yaml

delete-ip-egress: ## Delete Egress icmp
	kubectl delete -f  egress-ip.yaml

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'
