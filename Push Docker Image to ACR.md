## Push Docker Image to Azure Container Registry

Local image must be tagged prior to upload
```
$acrName = "<acrName>"
$DOCKER_IMAGE = "hello-aspnetcore:v2"

# tag image with repo name and version
docker tag $DOCKER_IMAGE "$acrName.azurecr.io/$DOCKER_IMAGE"
```

Log into Registry
```
az acr login --name $acrName
```

Push image to Registry
```
docker push "$acrName.azurecr.io/$DOCKER_IMAGE"
```

List images in Registry
```
az acr repository list --name $acrName
```