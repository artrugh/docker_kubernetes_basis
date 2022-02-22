## CLI

Run those command on your terminal to:
- pull images from docker-hub
- create a network named mongo-network
- set ports and variables

```sh
docker pull mongo
docker pull mongo-express
docker network create mongo-network
docker run -d -p docker:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo
docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express   
```