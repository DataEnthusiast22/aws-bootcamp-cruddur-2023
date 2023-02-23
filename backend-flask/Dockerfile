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

# Copy the external script file to the container
COPY ext_flask.sh ext_flask.sh

# Set permissions for the script file
RUN chmod +x ext_flask.sh

# Run the external script instead of the CMD instruction
CMD ["/bin/bash", "ext_flask.sh"]