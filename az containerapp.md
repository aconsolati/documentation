## az containerapp CLI reference
https://docs.microsoft.com/en-us/cli/azure/containerapp?view=azure-cli-latest&preserve-view=true

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
Setup session variables for deployment
```
$RESOURCE_GROUP="my-container-apps"
$CONTAINERAPPS_ENVIRONMENT="my-environment"

# set var to name of private acr
$acrName = "acrname"

# get the ACR password
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv
```

Deploy the image into a new container app:
```
$CONTAINERAPP_NAME = "new-container-app"
$CONTAINERAPP_IMAGE = "$acrname.azurecr.io/aci-tutorial-app:v1"

# create the container app from the registry image
az containerapp create `
	--name $CONTAINERAPP_NAME `
	--resource-group $RESOURCE_GROUP `
	--image $CONTAINERAPP_IMAGE `
	--environment $CONTAINERAPPS_ENVIRONMENT `
	--registry-login-server "$acrname.azurecr.io" `
	--registry-username "$acrname" `
	--registry-password "$password" `
	--target-port 80 `
	--ingress 'external' `
	--query configuration.ingress.fqdn
```
Update the app from a new container image
```
az containerapp update `
	--name $CONTAINERAPP_NAME `
	--resource-group $RESOURCE_GROUP `
    --image $CONTAINERAPP_IMAGE
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

## Comparison between different serverless Azure App options:
https://docs.microsoft.com/en-us/azure/container-apps/compare-options