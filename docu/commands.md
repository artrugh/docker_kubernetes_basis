## Docker


### Images

| keys | description |
| --- | --- |
| docker images | show local images |
| docker pull <image-name> | pull an image |
| docker run <image-name> | pull (if necessary) an image and create a local container running environment for the image |
| docker run -d <image-name> | run in a detach mode |
| docker rmi <image-id> | remove an image |
| docker build -t <image-name>:1.0 .<dockerfile-path> | create an image by the docker file settings |
| docker ps -a \| grep <image-name> | search a running container base on an image - the -a means all | 
| docker tag <existing-image-name>:<tag> <new-image-name>:<tag> | rename an image |

### Containers

| keys | description |
| --- | --- |
| docker container | show containers |
| docker ps | see running containers |
| docker ps -a | show all the running containers |
| docker run -p<host-port>:<container-port> <image-name>| create a new container and bind host port to container port |
| docker run --name <container-name> <image-name> | pull an image and create a container with an specif name |
| docker run --net <network-name> <image-name> | pull an image and create a container running in a specific network |
| docker stop <container-id> | stop a container |
| docker start <container-id> | start a container |
| docker rm <container-name>  | remove a container |
| docker logs <container-id> | show logs of the container |
| docker logs <container-name> | show logs of the container |
| docker logs <container-name> \| tail | show last logs of the container |
| docker logs <container-name> -f | show logs as strings of the container |
| docker exec -it <container-id> /bin/bash | get inside the container by the virtual machine environment |
| docker exec -it <container-id> /bin/sh | get inside the container by the virtual machine environment |
| exit | exit the virtual machine environment |
| docker network ls | show docker network |
| docker network create <network-name> | create a network |
| docker-compose -f <file-name> up | recreate and start all the container specify in the file |
| docker kill $(docker ps -q) | stop all the running containers - the docker ps return all running container ids and the q list only the ids for those containers |

### Compose

| keys | description |
| --- | --- |
| docker-compose down | stop and remove running containers |
| docker-compose up -d | create and run all the container |
| docker-compose start | run containers which have been stopped |
| docker-compose stop | stop running containers |
| docker-compose restart | restart stopped and running containers |
| docker-compose list | show list of all containers |
| docker-compose build | build images |


### Commands to use inside the docker operation system

| keys | description |
| --- | --- |
| env | show the environment |


### Push local image to docker hub

You can add as many images as you want in a repository, by adding specific tag (by defaulr it is set "latest")

1. Create a repository in docker hub
2. naming it using the docker hub username and the repository
    - build a new image
    ```sh
        docker build -t <hub-user>/<repo-name>[:<tag>]
    ```
   - re-tagging an existing local image
    ```sh
        docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]
    ```
   - using commit changes
    ```sh
        docker commit <existing-container> <hub-user>/<repo-name>[:<tag>]
    ```
3. docker push <hub-user>/<repo-name>:<tag>



### Kubernetes Commands

| keys | description |
| --- | --- |
| kubectl get all | get all the components inside the cluster |
| kubectl get all \| grep <component-name> | get all the components with this name |
| kubectl get namespace | get namespaces |
| kubectl get pod | get all the pods |
| kubectl get pod -watch | watch mode |
| kubectl get service | get all the services |
| kubectl get secret | get all the secret |
| kubectl apply -f <file-name> | create pod |
| kubectl apply -f <file-name> --namespace=<namespace-name> | create pod into an specif namespace |
| kubectl describe service <service-name> | get info of a service |
| kubectl get pod -o wide | get pod info o=output wide=more-info |
| kubectl get deployment <deployment-name> -o yaml | console the deployment status in yaml format, getting the Etcs, which store this status |
| kubectl get deployment <deployment-name> -o yaml > <file-name>| save the deployment status in yaml format in a file |
| kubectl delete -f <deployment-file> | delete de deployment component|
| kubectl describe pod <pod-name> | get info from a pod |
| kubectl create namespace <namespace-name> | create a namespace |
| kubectl api-resources --namespaced=false | list resources out of namespaces |
| kubectl api-resources --namespaced=true | list resources into namespaces |
| --- | document separation sintax |
| minikube service <service-name> | assign an external service a public IP address |
| minikube addons enable ingress | add ingress image |