# Week 1 — App Containerization

## Assignment 1

## Run the dockerfile CMD as an external script
created /backend-flask/ext_flask.sh with the code

```
#!/bin/bash
python3 -m flask run --host=0.0.0.0 --port=4567
```

changed the CMD command in the ~/backend-flask/Dockerfile to

```
# Copy the external script file to the container
COPY ext_flask.sh ext_flask.sh

# Set permissions for the script file
RUN chmod +x ext_flask.sh

# Run the external script instead of the CMD instruction
CMD ["/bin/bash", "ext_flask.sh"]
```

## Push and tag a image to DockerHub

created a ~/Dockerfile using the code below
```
FROM python:3.9
RUN echo "Hello Bootcampers, this is my assignment"
```

Created a docker account and a public dockerhub repository.
Logged into dockerhub via my gitpod terminal using the docker login 
```
docker login --username=dataenthusiast22
```
![dockerhub_login_successful](https://user-images.githubusercontent.com/113455719/220939666-ffd12a8e-64c4-458a-9ba6-e00e75fb1a8d.png)

Changed directory to where the Dockerfile I created earlier is located, then built an image in the current directory
from Dockerfile & tagged it using docker build

```
docker build -t homeworkweek1 .
```

show the repository, tag, image ID, creation date & size

```
docker images
```

![docker_images](https://user-images.githubusercontent.com/113455719/220939820-6c6790d3-98bb-49d9-8403-e85b6e9fe4b4.png)

create a new tag for a Docker image in my public dockerhub repository

```
docker tag 6f39a5d141af dataenthusiast22/assignment1:latest
```

push the docker image into my public dockerhub repo

```
docker push dataenthusiast22/assignment1:latest
```

a check of my dockerhub public repo confirms the image has been successfully pushed

![dockerhub_image2](https://user-images.githubusercontent.com/113455719/220939900-725fa2b5-d877-47a8-a29d-67b8548a7dad.png)

## Research best practices of Dockerfiles and attempt to implement it in your Dockerfile
1. It's important for dockerfiles to create containers that can be stopped & destroyed and then rebuilt & replaced with a minimum set of setup & configuration. This allows for consistent & reproducible builds and prevents the presence of artifacts or dependencies installed during previous builds. 
2. The use of multistage builds is encouraged as it helps reduce the final image size.
3. Use dockerfile to decouple apps into multiple containers making it easier to scale horizontally and reuse containers.
4. Minimize the number of layers (==RUN==, ==COPY== or ==ADD== creates layers) & use multibuilds while only copying artifacts needed in the final stage.
5. Whenever possible, use current official images in the ==FROM== instruction as the basis for your images.
6. Split long or cmoplex RUN statement into multiple lines sperated by backlashes.
7. Always combine ==RUN apt-get update== with ```apt-get install``` in the same RUN statement as using 'apt-get update' alone in a RUN statement causes caching issues and the subsequent 'apt-get install' fails. && can be used to combine both.
8. CMD should rarely be used in the manner of 'CMD ["param1", "param2", ..] in conjunction with ENTRYPOINT except you are quite familiar with how ENTRYPOINT works.
9. Use of common traditional ports for your app is encouraged when using the EXPOSE instruction.
10. Copy files individually if you have multiple dockerfile steps that use different files as this ensures each steps build cache is only invalidated.
11. The use of AND to fetch packes is strongly discourages as it increases the size of the image to be created. 'curl' or 'wget' can be used instead
12. Always use absolute paths for your WORKDIR

# APP CONTAINERIZATION FOLLOW ALONG

## CONTAINERIZE BACKEND

### BUILD DOCKERFILE

In Gitpod, installed the docker extension,
then created a file 'Dockerfile' in the backend-flask directory and placed
the code below in it

```
FROM python:3.10-slim-buster

# indicates that /backend-flask is the working directory
WORKDIR /backend-flask

# it copies requirements.txt from the outside container to the inside
# container and names it requirements.txt
COPY requirements.txt requirements.txt

# # to read & install Python packages listed in requirements.txt file using pip3.
RUN pip3 install -r requirements.txt

# it copies everything from outside container to the inside container
COPY . .

# sets the environment variables
ENV FLASK_ENV=development

EXPOSE ${PORT}

# CMD = command. it is used to run flask app
# this basically says run flask module using python version 3 listening on host 0.0.0.0 port 4567

CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

changed directory up a level (to project home directory) using 
```
cd ..
```

### BUILD A CONTAINER
ran the code below to build a container
```
docker build -t  backend-flask ./backend-flask
```
this code is used to build the dockerimage and tag it 'backend-flask' from the dockerfile './backend-flask'


![where_is_my_dockerimage](https://user-images.githubusercontent.com/113455719/220203742-3bd63278-9b9a-432e-bf75-10a6f6c0daf6.png)


the backend-flask docker image built can be found in the docker tab 

the command 'docker images' is used to list the available docker images

### RUN CONTAINER
ran the code 
```
docker run --rm -p 4567:4567 -it backend-flask
```
the code above runs flask without setting the environment variables. starts a new container in interactive mode based on the 'backend-flask' image and map port 4567 on the host machine to port 4567 inside the container. interactive mode, means you'll be dropped into a shell prompt inside the container to allow run other commands inside the container or with the running Flask application the cotainer will be removed automatically once its stopped due to '--rm' in the line of code 

the code below to runs flask and sets the environment variables

```
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
```
By using environment variables to store these URLs, the Flask application can be easily configured to work with 
different frontend and backend applications simply by changing the values of these environment variables without 
having to modify the application code.

made the port public by unlocking the port in the ports tab

Note: CTRL+C is used to stop the server after its been spun up.

add /api/activities/home to your URL to see results


## CONTAINERIZE FRONTEND
navigate to frontend-react-js directory
install npm
```
npm i
```

create Dockerfile in frontend-react-js directory and fill with code below

```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]
```

create docker-compose.yaml at project home directory and fill with code below
```
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

Docker compose is a tool that makes it easier to orchestrate multiple docker containers that needs to function at the same time

rightclick on the docker-compose.yml file and click compose up to use docker compose up

make the port public by unlocking the port in the ports tab

use git add, git commit and git push to update github repository
