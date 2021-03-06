# Play with Azure Container Instance (ACI)

## Commands

```
#The following variables will be used within the scope of the commands illustrated below:
RG=<your-resource-group-name>
ACI=<your-aci-name>
LOC=<your-aci-location>
CONT=<your-container-name>

#Create the resource group for the services for your ACI
az group create \
    -l $LOC \
    -n $RG

#Create your ACI from DockerHub
az container create \
    -g $RG \
    -n $ACI \
    -l $LOC \
    --image <dockerhub-username>/$CONT \
    --ip-address public \
    --ports 80 443 \
    -e CONTAINER_HOST=ACI

#Create your ACI from ACR
az container create \
    -g $RG \
    -n $ACI \
    -l $LOC \
    --image <your-acr>.azurecr.io/$CONT \
    --registry-password <your-acr-password> \
    --ip-address public \
    --ports 80 443 \
    -e CONTAINER_HOST=ACI 

#Optional parameters and default values for "az container create":
#--cpu 1
#--memory 1.5
#--os-type Linux
#--restart-policy Always
#--dns-name-label ; The dns name label for the container group with public ip. Should be unique per region. It will be your prefix" http://<dns-name-label>.<region>.azurecontainer.io

#List all your ACIs within a resource group
az container list \
    -g $RG
    -o table

#Check the status of your ACI
az container show \
    -n $ACI \
    -g $RG

#Execute a command (interactive bash shell in the example below) from within a running container
az container exec \
  --exec-command "/bin/bash" \
  -n $ACI \
  -g $RG \
  --container-name $CONT

#Get the logs of your ACI
az container logs \
    -n $ACI \
    -g $RG \
    --container-name $CONT

#Attach local std output and error streams to a container
az container attach \
  -n $ACI \
  -g $RG
  --container-name $CONT

#Delete your ACI (you could save some $$)
az container delete \
    -n $ACI \
    -g $RG
```

## Notes

- ACI has been released GA on April 25, 2018 - [http://aka.ms/aci/ga-blog](http://aka.ms/aci/ga-blog)
- ACI supports both Windows and Linux
- ACI is really great for a fast/burstable Docker container deployment and for on-demand process. Its goal is not very to host long-running process like web app or web api, [checkout how its pricing works](https://azure.microsoft.com/pricing/details/container-instances/).
- ACI is for a single container per instance, [it does not cover the higher-value services that are provided by Container Orchestrator](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-orchestrator-relationship)
- Make sure you understand the [ACI's restart policy setting](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-restart-policy)
- ACI doesn't provide for now a mechanism to redeploy/update a container instance - (see associated user voice)[https://feedback.azure.com/forums/602224-azure-container-instances/suggestions/32175820-allow-an-aci-container-to-update-itself-when-the-i] - you should recreate your container/container group with a new IP address
- ACI doesn't support for now scale out capabilities (i.e. increasing the number of instances for one Container Instance) but you could create more container group for this... For scale up, you could update the CPU/memory resource of existing Container Group but not yet by CLI/PowerShell but only by ARM Templates.
- [ACI Container Groups](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-container-groups) is like the Pod concept for Kubernetes. Rk: you could deploy multiple Containers/Instances in one Container Group (only on Linux for now) only by ARM Templates today, not by CLI nor PowerShell.
- You could [mount an Azure file, emptyDir, rigRepo or secret share with ACI](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-volume-azure-files)

## Resources

- [ACI service overview](https://azure.microsoft.com/services/container-instances/)
- [ACI service documentation](https://docs.microsoft.com/azure/container-instances/)
- [Quotas and region availability for ACI](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-quotas)
- [ACI pricing](https://azure.microsoft.com/pricing/details/container-instances/)
- [ACI CLI 2.0 documentation](https://docs.microsoft.com/cli/azure/container)
- [ACI PowerShell documentation](https://docs.microsoft.com/powershell/module/azurerm.containerinstance/#container_instances)
- Other labs:
  - https://github.com/Microsoft/MTC_ContainerCamp/blob/master/modules/azurecontainerinstances/aci.md#task-4-deploy-to-azure-container-instance