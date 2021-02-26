# FortiADC Cluster (2 units) with Microsoft Standard Loadbalancer




## Introduction

More and more enterprises are turning to Microsoft Azure to extend or replace internal data centers and take advantage of the elasticity of the public cloud. While Azure secures the infrastructure, you are responsible for protecting the resources you put in it. As workloads are being moved from local data centers connectivity and security are key elements to take into account. The FortiADC family of application delivery controllers (ADC) optimizes the availability, user experience, performance and scalability of enterprise application delivery.

An ADC is like an advanced server load balancer. An ADC routes traffic to available destination servers based on health checks and load-balancing algorithms; full-featured ADC like FortiADC also improve application performance by assuming some of the server task load. Server tasks that can be handled by the FortiADC appliance include SSL encryption/decryption, WAF protection, Gzip compression, and routing processes, such as NAT.

FortiADC complements Microsoft Loadbalancer with the above mentioned features in an easy to use and easy to manage environment. FortiADC also connects to other Fortinet services like:

* FortiGSLB (useful if you want to deploy a global deployment of FortiADC or want to combine on-premise and cloud based FortiADC units).
* Fortigate
* FortiAnalyzer

FortiADC further also offers features for Kubernetes, more information is currently available through the Fortinet sales channels. 

### Features

FortiADC uses Layer 4 and Layer 7 session information to enable an ADC policy and management framework for:

Server load balancing
Link load balancing
Global load balancing
Security
The FortiADC D-series family includes physical appliances and virtual appliances. 

## Design

In Microsoft Azure, you can deploy a pair of FortiADC appliances in multiple modes. The design in this chapter is a pair of FortiADC appliances across an Availability Set. This allows an SLA of 99,95% based on Microsoft calculation. 

When deployed you can publish your web application through the Microsoft Loadbalancer towards the cluster of FortiADC appliances. The Microsoft loadbalancer will take care of the traffic load between the cluster of FortiADC appliances. FortiADC will then inspect and route traffic to one or more backend servers (not part of the template). 

This Azure ARM template will automatically deploy a full working environment containing the the following components:

* One external Microsoft Standard Loadbalancer for communication with Internet
* Single Public IP used for application delivery and management (through NAT policies)
* Two FortiADC appliances (model can be selected during deployment)
* Network Security group allowing traffic towards the FortiADC on the following ports: 22, 80 and 443
* 1 VNET with 2 protected subnets

This ARM template can also be used to extend or customized based on your requirements. Fortinet is also offering other templates that might be of better fit to your organisation.


## Deployment

The FortiADC solution can be deployed using the Azure Portal (or Azure CLI). There are 4 variables needed to complete kickstart the deployment. When you deploy the ARM template the Azure Portal will request the variables as a requirement:

* RESOURCE GROUP: All resources for this template will be deployed in this group, recommend to create a new one.
* REGION : This is the Azure region where the deployment will be deployed
* ADMIN USERNAME : The username used to login to the FortiADC GUI and SSH mangement UI (the name: admin is not allowed).
* ADMIN PASSWORD : The password used for the FortiGate GUI and SSH management UI.
* FORTI ADC NAME PREFIX: This prefix will be added to each of the resources created by the templates for easy of use, manageability and visibility.
* FORTI ADC IMAGE SKU: Select which version you want to use, BYOL means you buy license from Fortinet, PAYG, all costs go through Azure
* FORTI ADC IMAGE VERSION: Select which version of FortiADC you want to use, latest means the latest available on Azure
* INSTANCE TYPE: See https://docs.fortinet.com/vm for more details on what version you need to choose
* PUBLIC IP NEW OR EXISTING: (default is new) If you want to get a new Public IP or re-use an existing one. Recommend to leave to default.
* PUBLIC IP NAME: Name of the Public IP
* PUBLIC IP RESOURCE GROUP: leave empty
* PUBLIC IP ADDRESS TYPE: (default is static) type of IP address, recommended to keep static
* VNET NEW OR EXISTING: (default is new) The VNet that will be used in this setup, choose new for a new vnet (recommended)
* VNET NAME: (required) name of the new to create Vnet
* VNET RESOURCE GROUP: (default empty) if left empty, resource group created above will be chosen
* VNET ADDRESS PREFIX: Subnet prefix for the new Vnet
* SUBNET1NAME:
* SUBNET1PREFIX:
* SUBNET2NAME:
* SUBNET2PREFIX:
* LOCATION:
* FORTINET TAGS:


### Azure Portal

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmduijm%2Ffortinet-azure-solutions%2Fmain%2FFortiADC%2Fha-lb-azure%2Fazuredeploy.json" target="_blank">
  <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true"/>
</a>
<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fjvhoof%2Ffortinet-azure-solutions$2Fmain%2FFortiGate%2FA-Single-VM%2Fazuredeploy.json" target="_blank">
  <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true"/>
</a>

### Azure CLI
To fast track the deployment, use the Azure Cloud Shell. The Azure Cloud Shell is an in-browser CLI that contains Terraform and other tools for deployment into Microsoft Azure. It is accessible via the Azure Portal or directly at [https://shell.azure.com/](https://shell.azure.com). You can copy and paste the below one-liner to get started with your deployment.

`cd ~/clouddrive/ && wget -qO- https://github.com/jvhoof/fortinet-azure-solutions/archive/main.tar.gz | tar zxf - && cd ~/clouddrive/fortinet-azure-solutions-main/FortiGate/A-Single-VM/ && ./deploy.sh`

![Azure Cloud Shell](images/azure-cloud-shell.png)

After deployment, you will be shown the IP addresses of all deployed components. This information is also stored in the output directory in the 'summary.out' file. You can access both management GUI's using the public management IP addresses using HTTPS on port 443.

## Requirements and limitations

The Azure ARM template deployment deploys different resources and is required to have the access rights and quota in your Microsoft Azure subscription to deploy the resources.

### Licenses

- The template will deploy Standard F2s VMs for this architecture. Other VM instances are supported as well with a minimum of 2 NICs. A list can be found [here](https://docs.fortinet.com/document/fortigate/6.2.0/azure-cookbook/562841/instance-type-support)
- Licenses for Fortigate
  - BYOL: A demo license can be made available via your Fortinet partner or on our website. These can be injected during deployment or added after deployment.
  - PAYG or OnDemand: These licenses are automatically generated during the deployment of the FortiGate systems.

### Fabric Connector
The FortiGate-VM uses [Managed Identities](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/) for the SDN Fabric Connector. A SDN Fabric Connector is created automatically during deployment. After deployment, it is required apply the 'Reader' role to the Azure Subscription you want to resolve Azure Resources from. More information can be found on the [Fortinet Documentation Libary](https://docs.fortinet.com/vm/azure/fortigate/6.4/azure-cookbook/6.4.0/236610/creating-a-fabric-connector-using-a-managed-identity).

## Support
Fortinet-provided scripts in this and other GitHub projects do not fall under the regular Fortinet technical support scope and are not supported by FortiCare Support Services.
For direct issues, please refer to the [Issues](https://github.com/jvhoof/fortinet-azure-solutions/issues) tab of this GitHub project.

## License
[License](LICENSE) © Fortinet Technologies. All rights reserved.
