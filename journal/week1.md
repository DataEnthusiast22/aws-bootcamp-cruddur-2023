# Week 1 — App Containerization
## BUILD DOCKERFILE

In Gitpod, installed the docker extension,
then created a file 'Dockerfile' in the backend directory and placed
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

## BUILD A CONTAINER
ran the code below to build a container
```
# this is used to build the dockerimage and tag it 'backend-flask' from the dockerfile './backend-flask'

docker build -t  backend-flask ./backend-flask
```

![where_is_my_dockerimage](https://user-images.githubusercontent.com/113455719/220203742-3bd63278-9b9a-432e-bf75-10a6f6c0daf6.png)


the backend-flask docker image built can be found in the docker tab 

the command 'docker images' is used to list the available docker images

## RUN CONTAINER
ran the code 
```
# starts a new container in interactive mode based on the 'backend-flask' image and map port 4567 
# on the host machine to port 4567 inside the container. 
# interactive mode, means you'll be dropped into a shell prompt inside the container to allow run other 
# commands inside the container or with the running Flask application the cotainer will be removed 
# automatically once its stopped due to '--rm' in the line of code 

docker run --rm -p 4567:4567 -it backend-flask
```
the code above runs flask without setting the environment variables

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
