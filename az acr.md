## `az acr` CLI reference:
https://docs.microsoft.com/en-us/cli/azure/acr?view=azure-cli-latest

Tutorial with commands for creating docker image, creating container registry and pushing up image:
https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-app

Creating and managing the ACR registry:
https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-acr

### General ACR commands
```
set $acrName = <acrName>

# list all images in a repository
az acr repository list --name $acrName

# get registry password (after login)
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv
```

Show tags (revisions) in a repository
```
az acr repository show-tags -n $acrName --repository hello-aspnetcore
```