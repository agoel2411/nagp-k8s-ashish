# Demo : Deployment of Mongo Express and Monogo DB on Kubernetes Cluster using Minikube

This is an assignment for demonstrating the deployment of a MongoDB as backend (using statefulset)  and MongoExpress as frontend on Kubernetes Cluster using Minikube


## 1 Code Repo

Below is the Github Reppo where manifest is placed: https://github.com/agoel2411/nagp-k8s-ashish


## 2 Images

Here, Focus is on deployment resources on kubernetes cluster. Therefore, We are keeping deafult images for demonstration

### 2.1 Frontend Image:
We are using default mongo-express docker image for Mongo-Express as Frontend API service

### 2.2 Backend Image:
We are using default mongo docker image for MongoDB as Backend Database


## 3 Service URL

Here, API service will be available when we will be starting service via Minikube. This is shown in demonstrated video.


## 4 Steps needs to be performed

### 4.1 Pre-Requisites

Make sure following dependencies are already installed on loca machine:

1. Install docker on machine
2. Install Minikube

Check if Docker is installed& Minikube is UP and running:

    ```
    $ docker --version
    $ minikube status
    $ minikube addons list
    ```

### 4.2 Backend Deployment Steps 

1. Create a Namespace where all services will be installed

    ```
    $ kubectl apply -f .\namespace.yml
    ```

2. Create a Secret to securely store Mongo DB credentials

    ```
    $ kubectl apply -f .\database\mongodb-secret.yml
    ```
3. Create a Persistent Volume Claim which will be bounded to Mongo DB where data will be presisted even on pod restart

    ```
    $ kubectl apply -f .\database\mongodb-pvc.yml
    ```
4. Create a headless service which will help to access database within k8s cluster and to connect to indvidual pod (master) directly. There is no cluster IP address assigned to Headless service.

    ```
    $ kubectl apply -f .\database\mongodb-service.yml
    ```
5. Create a Statefulset which willbe keeping same pod name even on restart. It is must for stateful application like database

    ```
    $ kubectl apply -f .\database\mongodb-statefulset.yml
    ```

### 4.3 Frontend Deployment Steps 

1. Create a ConfigMap to securely store Mongo DB connection details

    ```
    $ kubectl apply -f .\api\mongoexpress-configmap.yml
    ```
2. Create a deployment which will maintain a desire number of replicas of API service

    ```
    $ kubectl apply -f .\api\mongoexpress-deployment.yml
    $ kubectl apply -f .\api\mongoexpress-deployment.yml # To update the deployment/image
    $ kubectl rollout status deployment <deploymentName> -n <NameSpace> # To check the status of deployment
    $ kubectl rollout undo deployment <deploymentName> -n <NameSpace> # To rollback to previous version of deployment
    ```
3. Create a HPA which is horizontal Pod Autoscaler to provide the feature of auto-scale up and down the number of pod based of utilization of resources (CPU/Memory) due to increase/decrease traffic on API

    ```
    $ kubectl apply -f .\api\mongoexpress-hpa.yml
    ```
4. Create a service to expose API outside cluster to access the application externally

    ```
    $ kubectl apply -f .\api\mongoexpress-service.yml
    ```

5. Access the mongo express application externally

    ```
    $ minikube service mongoexpress-service -n <NameSpace>
    ```
