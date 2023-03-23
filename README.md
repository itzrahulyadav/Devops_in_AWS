# 1. Codecommit 
### Get the source code 

- Create a repo in codecommit and provide codecommitsuperuseraccess role
- push all your files in codecommit

# 2. CodeBuild
### Build your code just like jenkins

- It needs buildspec file
- Create a buildspec file
- example of buildspec file

```
version: 0.2

phases:
  install:
    commands:
      - echo installing NGINX
      - sudo apt-get update
      - sudo apt-get install nginx -y
  build:
    commands:
      - echo build started on 'date'
      - cp index.html /var/www/html/
  post_build:
    commands:
      - echo Configuring nginx

artifacts:
  files:
    - '**/*'

```

# 3. Upload artifacts

-  you can use artifacts stage to upload artifacts 
-  create a s3 bucket and provide the name of the bucket and the folder name 
-  refer the image below for reference 

![Screenshot (122)](https://user-images.githubusercontent.com/65400893/227285717-187478f5-af26-4dec-9afb-2ff884fe83c9.png)

![Screenshot (123)](https://user-images.githubusercontent.com/65400893/227285763-abfe9ed6-33b6-46f4-a9fb-cf5d64031d61.png)



# 4. Codedeploy stage

- Create an ec2 instance (remember the name of the instance as it will be needed in deployment group)
- make sure to give ec2 permission/iam role for codedeploy
- the role should have following policies attached
 ![Screenshot (125)](https://user-images.githubusercontent.com/65400893/227295274-438859a0-5d07-4bf4-a653-bdac1a2863c7.png)

- use ubuntu for ami
- install codedeploy agent
- steps to install codedeploy-agent

```
sudo yum update -y
sudo yum install ruby 
sudo yum install wget
wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install
# replace bucket-name with aws-codedeploy-ap-south-1 and region-identifier with ap-south-1
# the code for ap-south-1 region will be
# wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent status


```

- This stage uses appspec.yml file
- create a file like the example given below
- appspec.yml file

```
version: 0.0
os: linux
files:
    - source: /
      destination: /var/www/html
hooks:
  AfterInstall:
    - location: scripts/install_nginx.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_nginx.sh
      timeout: 300
      runas: root

```

- in the script folder these are the following files
- install_nginx.sh

```
#!/bin/bash

sudo apt-get update
sudo apt-get install nginx -y

```

- start_nginx.sh

```

sudo service nginx start

```

- 
-  Create a codedeployment group
-  Give the permission by creating the following role
- 
![Screenshot (124)](https://user-images.githubusercontent.com/65400893/227292057-cf7a7c7e-d522-472c-b3f2-b52df44e1a83.png)


