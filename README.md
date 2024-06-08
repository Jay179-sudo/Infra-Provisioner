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
	 

   
The email will be validated. Only requests from the 'iiitm.ac.in' domain will be allowed access to the resources.

The API will send a request to the kube-apiserver. The custom controller running inside the Kubernetes cluster will intercept this request and spin up a Deployment, a NodePort service and a data storage facility (with the help of PV, PVC and Storage Classes).

An open port will be selected and allocated to the user. An email will be sent from the API to the user which explains how to access the pod that has been provisioned for them.

The API server has been granted a Service Token which allows it access to creating and deleting the user request custom resource.

All the resources created by the user will  have their emails prefixed to the name of the instance of the resource which will help in uniquely identifying the resources allocated to a user.

Any changes made the repo will result in a CI/CD pipeline being kicked in which will perform unit and integration tests before pushing it up to docker hub. It will also create a new branch and push changes to the manifest files used in the Infra-Manifest Repository. This way a PR can be created and merged with the main branch once the features have been implemented. 


### [Infra-Manifests](https://github.com/Jay179-sudo/Infra-Manifests)


This repository contains the manifest files for ArgoCD to use to continuously deploy resources to the Kubernetes cluster. 

ArgoCD will continuously poll this repository for changes made to the manifest files to update deployments. The default polling time of three minutes has been used.

All user requests will not be tracked by ArgoCD since it is a volatile resource which ArgoCD will have difficulty to maintain.

Monitoring has been provided with Prometheus and visualization has been provided using Grafana. 

Testing has been performed using Grafana K6.
