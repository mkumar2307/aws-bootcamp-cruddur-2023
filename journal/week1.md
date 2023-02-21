# Week 1 — App Containerization     
     
Added Docker VSCode extension It will make things easy to work with Docker     
       

## Containerize Application (Dockerfiles, Docker Compose)      
Created Docker files for Back end and front applications and built images using these docker files    

```sh
docker build -t  backend-flask ./backend-flask
docker build -t  backend-flask:latest ./backend-flask     
```
    
## Running these Docker Images as Containers       
  
```sh
docker container run --rm -p 4567:4567 -d backend-flask     
docker run -p 3000:3000 -d frontend-react-js      
```     

Send Curl to the Test Server (Backend)        
curl -X GET http://localhost:4567/api/activities/home -H "Accept: application/json" -H "Content-Type: application/json"      
     
## To Check Docker Container Logs        
  
```sh
docker logs CONTAINER_ID -f      
docker logs backend-flask -f     
docker logs $CONTAINER_ID -f      
```     
     
## Gain Access to a container   
     
```sh
docker exec CONTAINER_ID -it /bin/bash      
CONTAINER_ID=$(docker run --rm -p 4567:4567 -d backend-flask)      
```     

## To Delete a Docker Image  
     
```sh
docker image rm Docker_Image_Name --force     
```     

## To get list of Docker Images and Running Container ID's    
      
```sh
docker ps      
docker images    
```     

## Multiple Containers      
   
We use docker-compose tool to create and run multi-container Docker applications      
With Compose, we use a YAML file  to configure our application's services.        
      
docker-compose.yaml       
```sh
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js
      
# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur     
```     

## Adding DynamoDB Local and Postgres      
     
We are going to use DynamoDB Local and Postgres. We will integrate the following into our existing compose file.      
      
postgres    
```sh     
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local     
 ```    
 
DynamoDB Local     
```sh
services:
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal      
 ```     
 
Directory Volume Mapping      
```sh     
volumes: 
- "./docker/dynamodb:/home/dynamodblocal/data"      
```   

Named Volume Mapping     
 ```sh     
volumes: 
  - db:/var/lib/postgresql/data

volumes:
  db:
    driver: local
```
      
## Notifications Feature      

Created Notifiactions feature for backend and frontend appliactions as part of new feature addition.   
## Backend     

Made changes in backend-flask/app.py to add import and api for notifications     
```sh     
from services.notifications_activities import *
@app.route("/api/activities/notifications", methods=['GET'])
def data_notifications():
  data = NotificationsActivities.run()
  return data, 200    
 ```    
   
Updated openapi yaml file to check how added api works.    
Added Notifications activities service file (Python)
     
## Front-end     
       
Made changes in frontend-react-js/src/App.js to add import and /path    
```sh     
import NotificationsFeedPage from './pages/NotificationsFeedPage';
 {
    path: "/notifications",
    element: <NotificationsFeedPage />
  },     
 ```   
 
Added simple CSS file for Notifications Feed Page     

```sh 
article {
    display: flex;
    flex-direction: row;
    justify-content: center;
  }     
```   

Added Notifications Feed page JS file.    
frontend-react-js/src/pages/NotificationsFeedPage.js
