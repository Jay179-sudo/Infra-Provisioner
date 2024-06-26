
# Infrastructure Provisioner using Kubernetes

This repository is the "frontend" for the project I had made which has been split up into multiple repos for separation of concerns.

### [Infra-Backend](https://github.com/Jay179-sudo/Infra-Backend)
This project contains the backend GO code for the API that intercepts requests from the user about the specification of the container they want to provision.

Users have to specify the RAM, the number of CPUs, the storage capacity. Their public key will also be required for SSH authentication.

A sample request looks like

    {
	"Email": "bcs2021_080@iiitm.ac.in",
	"Specification": {
		"RAM": "2",
		"CPU": "1",
		"PublicKey": "ssh-rsa ..."
	}
	 
![Submit-Request](https://github.com/Jay179-sudo/Infra-Provisioner/assets/70968485/e4613edb-2182-483d-8ea3-efa2b7ba0faf)

   
The email will be validated. Only requests from the 'iiitm.ac.in' domain will be allowed access to the resources.

The API will send a request to the kube-apiserver. The custom controller running inside the Kubernetes cluster will intercept this request and spin up a Deployment, a NodePort service and a data storage facility (with the help of PV, PVC and Storage Classes).

An open port will be selected and allocated to the user. An email will be sent from the API to the user which explains how to access the pod that has been provisioned for them.

The API server has been granted a Service Token which allows it access to creating and deleting the user request custom resource.

All the resources created by the user will  have their emails prefixed to the name of the instance of the resource which will help in uniquely identifying the resources allocated to a user.

Any changes made the repo will result in a CI/CD pipeline being kicked in which will perform unit and integration tests before pushing it up to docker hub. It will also create a new branch and push changes to the manifest files used in the Infra-Manifest Repository. This way a PR can be created and merged with the main branch once the features have been implemented. 

We can also delete the systems we have provisioned. We will require the email of the user. 

A sample request will look like 

   
	{
		"Email": "bcs_2021080@iiitm.ac.in"
	}

We can log into the system that has been provisioned for us by using ssh. Instructions will be sent via email.

![SSH-Login-Example](https://github.com/Jay179-sudo/Infra-Provisioner/assets/70968485/beb9edcd-0ee3-47dc-899a-19a568731b74)

### [Infra-Manifests](https://github.com/Jay179-sudo/Infra-Manifests)


This repository contains the manifest files for ArgoCD to use to continuously deploy resources to the Kubernetes cluster. 

ArgoCD will continuously poll this repository for changes made to the manifest files to update deployments. The default polling time of three minutes has been used.

All user requests will not be tracked by ArgoCD since it is a volatile resource which ArgoCD will have difficulty to maintain.

Monitoring has been provided with Prometheus and visualization has been provided using Grafana. 

Traefik has been used as an Ingress Controller. 
The dashboard can be seen here

![Dashboard-1](https://github.com/Jay179-sudo/Infra-Provisioner/assets/70968485/1ff84d1c-752d-47ec-ba53-21df36f11a43)

![Dashboard-2](https://github.com/Jay179-sudo/Infra-Provisioner/assets/70968485/5d8b9d2a-8fec-479e-a19a-ff9cfd295f76)

![Dashboard-3](https://github.com/Jay179-sudo/Infra-Provisioner/assets/70968485/b19b9ac8-8226-4648-928d-c7f0155c8bd3)



