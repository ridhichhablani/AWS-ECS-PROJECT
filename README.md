# AWS-ECS-PROJECT
Deploying a flask app on AWS ECS.
## 1. Installation
$ sudo apt-get update -y && sudo apt-get install -y linux-image-extra-$(uname -r)
### 1.2. Install docker

$ sudo apt-get install docker-ce -y

### 1.3. Start Docker
$ sudo service docker start

###1.4. Verify Docker
$ sudo docker run hello-world


##2. Build Simple python application
### 2.1. Make a directory for application and create app.py file.

$vim app.py
### 2.2. Copy following code in app.py.

from flask import Flask
from flask_restful import Resource, Api
app = Flask(__name__)
api = Api(app)
class MyClass(Resource):
    def get(self):
        return {'Hello': 'This is an flask rest API deployed in ECS'}
api.add_resource(MyClass, '/hello')
if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
### 2.3. Create requirments.txt file.

$vim requirements.txt
### 2.4. Copy following code in requirments.txt file.

flask
flask_restful
2.5. Create Dockerfile

$ vim Dockerfile
### 2.6. Copy following code.

FROM python:3.7
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
CMD python app.py
### 2.7. Build docker image

$ docker build -t my_docker_app:latest .
### 2.8. Run docker image

$ docker run -d -p 5000:5000 my_docker_app:latest
### 2.9. Test your app locally

$curl http://localhost:5000/hello
## 3. Upload our sample docker image to ECR
### 3.1. Create ECR Repository

Go to Amazon ECR and create a repository in AWS ECR and follow push commands to upload docker image to ECR as shown in below gif.



## 4. Create ECS Cluster
### 4.1. Go to Amazon ECS → Clusters → click on “Create Cluster”.


### 4.2. Then select “EC2 Linux + Networking” .



### 4.3. Configure cluster.


### 4.5. Then click on “Create”.

## 5. Create Task Definition
### 5.1. Go to Amazon ECS → Task Definitions → click Create new task definition and fill in below details.


## 5.2. Add containers, fill in details as shown below and click on “Create”.


## 6. Create Service
### 6.1. Go Amazon ECS → Clusters → select your cluster and click on “Create Service” .


### 6.2. In “load balancing” section, select Application Load Balancer and select your load balancer. 

In “Container to load balance” section, create a new listener port 80 and create a new target group. Set health check path to our API path i.e. /hello. Then click on the next step and skip auto scaling for now and finally click on “Create Service.”


7. Test your application.
Go to EC2 management console, click on Load Balancers, select the load balancer, we used for creating ECS service and grab its DNS name.

Go to your browser and access your API using following url.

“http://<LOAD_BALANCER_DNS>/hello”.
