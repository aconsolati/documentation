## Comparison between different serverless app environments:
https://docs.microsoft.com/en-us/azure/container-apps/compare-options

Container app deployment notes:
1. Resource group: container-app-test
2. Registry: antreg.azurecr.io


Cheat sheet commands
* create docker file for project: VSCode, docker extension, Add Docker Files to Workspace
* build Docker image: `docker build . -t aci-tutorial-app:v1`
* remove Docker image: `docker image rm <image>, eg docker image rm fedora:24`

Must tag docker image with acr name before pushing:
* docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1

To delete resource group:
* az group delete --name <resource-group-name>

### Deploy Container App

```
# set var to name of private acr
$acrName = "acrname"

# get the ACR password
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv

# create the container app from the registry image
az containerapp create `
	--name my-container-app `
	--resource-group $RESOURCE_GROUP `
	--image "$acrname.azurecr.io/aci-tutorial-app:v1" `
	--environment $CONTAINERAPPS_ENVIRONMENT `
	--registry-login-server "$acrname.azurecr.io" `
	--registry-username "$acrname" `
	--registry-password "$password" `
	--target-port 80 `
	--ingress 'external' `
	--query configuration.ingress.fqdn
```

### Azure Container Instances
* Low-level access than Container apps
* Tutorial: https://markheath.net/post/build-container-images-with-acr
```
# get the ACR password
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv

# create a new ACI instance to run this container
az container create -n samplewebapp -g $resourceGroup `
            --image "$acrName.azurecr.io/samplewebapp:acr" `
            --registry-username "$acrName" `
            --registry-password "$password" `
            -e TestSetting=ACI `
            --dns-name-label samplewebapp --ports 80
```