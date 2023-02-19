# Week 0 — Billing and Architecture

## Logical Architecture Diagram
I recreated the logical architecture diagram using lucid charts

![Cruddur Architectural Diagram](https://user-images.githubusercontent.com/113455719/219932410-ef797b62-00c3-42b5-866e-e7afb2b30a28.png)

## Install AWS cli
I downloaded and installed AWS cli on gitpod terminal using curl, unzip & sudo
![Screenshot_1](https://user-images.githubusercontent.com/113455719/219851861-ce808680-6d8a-4851-9cae-f94e03f8fb32.png)

![Screenshot_2](https://user-images.githubusercontent.com/113455719/219851909-9097dbd6-23c7-43e1-9d6e-a4e2d998590c.png)


Confirmed if it was installed

![Screenshot_3](https://user-images.githubusercontent.com/113455719/219852626-a5a9ea20-42cf-4d39-9b66-ff2f1bfc4d8c.png)

## Generate AWS credentials

Generated AWS credentials from IAM and set the environment variables in gitpod
used the 'env | grep AWS_' command to confirm if the environment variables were set

![Screenshot_5](https://user-images.githubusercontent.com/113455719/219854125-f037ac5a-6cce-468c-bf89-a69cdfcc9beb.png)

I blacked out part of the secret_access_key & access_key_id for security reasons

-- updated .gitpod.yml with the code below 

'''
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
      '''
      
-- pushed changes made in gitpod to github public repo using git add, git commit and git push


## Create a budget
Created a zero spend budget using the billing management console

![Screenshot_6](https://user-images.githubusercontent.com/113455719/219872831-62404594-9007-4043-b904-0d768a08317c.png)

## Create a billing alarm
First created a SNS topic and used the TopicArn to set up the billing alarm

![Screenshot_7](https://user-images.githubusercontent.com/113455719/219873233-1e854baf-38dd-4c9e-b7c7-fcc380487939.png)


