## Django Todo App Deployment with Kuberntes and AWS

A todo application built with Django and React .

## Architecture

![image](https://user-images.githubusercontent.com/98258627/215501005-a4f6090e-cc0d-47b9-ab57-aaeb95edb8d2.png)

## Prerequisites

Before getting started, you will need to have the following tools installed on your machine:

-   Minikube
-   Docker
-   Django
-   Python
-   React

## Setting up the EC2 instance on AWS

1.  Log in to your AWS account.
2.  Go to the EC2 dashboard.
3.  Click the Launch Instance button.
4.  Choose the Ubuntu 20.04 LTS image.
5.  Choose the desired instance type and click the Next button.
6.  Configure the security group for your instance, allowing inbound traffic for ports 22 (SSH) and 80 (HTTP).
7.  Launch the instance and select a key pair to use for connecting to the instance via SSH.
8.  Connect to the instance via SSH.

## Building the Docker Image

1.  Navigate to the root directory of your project where the Dockerfile is located.
2.  Run the following command to build the Docker image:

```
docker build -t todo-app:latest .
```


Replace `<image-name>` with the name you want to give your image and `<tag>` with a version number for the image.

## Pushing the Docker Image to a Registry

1.  Log in to your Docker registry. You can use a public registry such as Docker Hub or a private registry.

```
docker login <registry-name>
```

Replace `<registry-name>` with the name of the registry you are using.

2.  Tag the Docker image with the registry name so that it can be pushed.

```
docker tag todo-app:latest <registry-name>/<image-name>:<tag>
```

3.  Push the Docker image to the registry.

```
docker push <registry-name>/<image-name>:<tag>
```

## Running Minikube

1.  Install Minikube by following the instructions in the [official documentation](https://kubernetes.io/docs/tasks/tools/install-minikube/).
2.  Start Minikube by running the following command:

```
minikube start
```

## Applying the YAML files

1.  Clone this repository to your machine.
2.  Change into the directory where the YAML files are located.
3.  Apply the YAML files to the cluster by running the following command:

```
kubectl apply -f pod.yaml
```

```
kubectl apply -f deployment.yaml
```

```
kubectl apply -f service.yaml
```

## Accessing the application

1.  Get the IP address of the Minikube service by running the following command:

```
minikube service <service-name> --url
```

2.  Run a `curl` command to access the application:

```
curl <service-IP>
```

## Allocating a host IP with Route 53

1.  Go to the Route 53 dashboard in the AWS console.
2.  Create a new hosted zone.
3.  Create a new record set in the hosted zone and specify the IP address of the Minikube service as the value for the record.
4.  Update your domain's name servers to use the name servers for the hosted zone in Route 53.

That's it! Your project should now be up and running 😄. 

## Integrating with Jenkins for CICD

You can also integrate it with Jenkins for Continous Integration and continueous Deployment 
This involves the following steps:

1.  Install Jenkins on the server where you want to run the CICD pipeline.
2.  Create a new Jenkins job for the application and configure the source code repository where the application code is stored.
3.  Configure the build triggers, such as whenever a change is pushed to the repository.
4.  Add build steps for building the Docker image and pushing it to the Docker registry.
5.  Add post-build actions to deploy the application on Minikube.

Here is an example of the Jenkinsfile that implements the above steps:

```
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t <image_name> .'
            }
        }
        stage('Push') {
            steps {
                sh 'docker push <image_name>'
            }
        }
        stage('Deploy') {
            steps {
                sh 'kubectl apply -f <deployment_file>.yaml'
            }
        }
    }
}

```

By integrating the application with Jenkins, we can automate the deployment process and ensure that the application is deployed consistently and with minimal manual intervention.
