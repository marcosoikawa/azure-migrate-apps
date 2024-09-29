# Configure Azure Migrate and VM with simple .NET Framework App to migrate into App Service

This article shows you how to set up for demo proposes a environment with .NET Framework App to be migrate to App Service

# Architecture diagram



## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

---

## Set up Azure subscription
Register the resources providers

### Create VMs 

Create Resource Group

```bash
az group create \
    -n az-migrate-apps \
    -l brazilsouth
```

Create VNET

```bash
az network vnet create --name az-mig-vnet --resource-group az-migrate-apps --address-prefix 10.0.0.0/16 --subnet-name vms --subnet-prefixes 10.0.0.0/24
```

Create IIS VMs

```bash
vmname="AzMig-IIS-01"
username="azureuser"
az vm create --resource-group az-migrate-apps --name $vmname --image Win2022Datacenter --public-ip-sku Standard --admin-username $username

```
Configure IIS on VM
```bash
az vm run-command invoke -g az-migrate-apps -n $vmname --command-id RunPowerShellScript --scripts "Install-WindowsFeature -name Web-Server -IncludeManagementTools"

az vm open-port --port 80 --resource-group az-migrate-apps --name $vmname

```
> :warning: It is not recommended open public IP on production environment without proper security layer (Web Application Firewall, DooS Protection). See: [Azure Web Application Firewall on Azure Front Door](https://learn.microsoft.com/en-us/azure/web-application-firewall/afds/afds-overview), [What is Azure DDoS Protection?](https://learn.microsoft.com/en-us/azure/ddos-protection/ddos-protection-overview)

Create Azure Migrate VM
```bash
vmname="AzMig-Appliance"
username="azureuser"
az vm create --resource-group az-migrate-apps --name $vmname --image Win2022Datacenter --public-ip-sku Standard --admin-username $username --size Standard_D8s_v5

```



## Clean Up

1. Access Azure Preview Portal
2. Delete Resource Group where your resources got provisioned.
