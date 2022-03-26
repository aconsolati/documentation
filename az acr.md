## Using Azure Container registry (ACR)
`az` commands for creating resource group and acr:
https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-app

### General ACR commands
```
set $acrName = <acrName>

# list all images in a repository
az acr repository list --name $acrName

# get registry password (after login)
$password = az acr credential show -n $acrName --query "passwords[0].value"  -o tsv
```