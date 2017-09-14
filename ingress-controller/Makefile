include $(MAKE_INCLUDE)/Makefile.inc

deploy_ingress_controller: install_default_backend install_ingress_controller install_ingress_service

create_ns:

	@kubectl create ns $(NS)

test_loadbalancer_http:

	$(eval IP := $(shell kubectl --namespace $(NS) get svc -o json | jq '.items[].status.loadBalancer.ingress[]?.ip'))
	@echo "LOADBALANCER IP: $(IP)"

	curl -vv http://$(IP)
	curl -k -vv https://$(IP)

watch_controller_logs:

	$(eval POD_NAME := $(shell kubectl --namespace monitoring get pod -o json | jq '.items[].metadata | select(.labels["k8s-app"] == "ingress-controller").name'))

	kubectl logs --namespace $(NS) $(POD_NAME) -f

install_default_backend:

	@kubectl apply --namespace $(NS) -f default-backend-deployment.yaml
	@kubectl apply --namespace $(NS) -f default-backend-service.yaml

delete_default_backend:

	@kubectl --ignore-not-found delete --namespace $(NS) -f default-backend-deployment.yaml
	@kubectl --ignore-not-found delete --namespace $(NS) -f default-backend-service.yaml

install_ingress_controller:

	@kubectl apply --namespace $(NS) -f controller-deployment.yaml

delete_ingress_controller:

	@kubectl delete --ignore-not-found --namespace $(NS) -f controller-deployment.yaml

reinstall_ingress_controller: delete_ingress_controller install_ingress_controller

install_ingress_service:

	@kubectl apply --namespace $(NS) -f controller-service.yaml


delete_ingress_service:

	@kubectl delete --ignore-not-found --namespace $(NS) -f controller-service.yaml
	@kubectl delete --ignore-not-found --namespace $(NS) ing/ingress