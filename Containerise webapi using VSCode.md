## Containerise ASPNet Web App using VSCode and Docker Extension

Walkthrough available on YouTube: https://www.youtube.com/watch?v=Po9jQS7WBDQ

```
# Create webapi project
dotnet new webapi -o <ProjectName>
```
Use Docker VsCode extension to add `Dockerfile` to project:
```
Command Pallete -> Docker: Add Docker Files to Workspace...
```
Build and run the container image:
```
# Build using Dockerfile in current directory
docker build -t myimagename:v1 .

# Verify image is available
docker images

# Run the container (interactively)
docker run -it --rm -p 8080:80 myimagename:v1

# Run the container (as background daemon with nice display name)
docker run -name <display-name> -d -p 8080:80 myimagename:v1

# List all running containers
docker container ls
```

Commands for clean-up:
```
# stop container running
docker container stop <container-name>

# delete container
docker container rm <container-name>

# delete image
docker image rm <image-name>
```
Sample generated Webapi Dockerfile:
```
FROM mcr.microsoft.com/dotnet/aspnet:6.0-focal AS base
WORKDIR /app
EXPOSE 80

ENV ASPNETCORE_URLS=http://+:80

FROM mcr.microsoft.com/dotnet/sdk:6.0-focal AS build
WORKDIR /src
COPY ["HelloAspNetCore.csproj", "./"]
RUN dotnet restore "HelloAspNetCore.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "HelloAspNetCore.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "HelloAspNetCore.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "HelloAspNetCore.dll"]
```