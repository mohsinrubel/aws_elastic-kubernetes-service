# aws_elastic-kubernetes-service

## Install or update the AWS CLI
To update your current installation of AWS CLI, download a new installer each time you update to overwrite previous versions. Follow these steps from the command line to install the AWS CLI on Linux.
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
```
unzip awscliv2.zip
```
```
sudo ./aws/install
```

## To install or update eksctl on Linux

* Download and extract the latest release of eksctl with the following command.
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
````
* Move the extracted binary to /usr/local/bin.
 ```
sudo mv /tmp/eksctl /usr/local/bin
```
* Test that your installation was successful with the following command.
```
eksctl version
````

## Install kubectl

If you are on Ubuntu or another Linux distribution that supports the snap package manager, kubectl is available as a snap application.
```
snap install kubectl --classic
kubectl version --client
```


## install Helm (Debian/Ubuntu)
Members of the Helm community have contributed a Helm package for Apt. This package is generally up to date.
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
```
```
sudo apt-get install apt-transport-https --yes
```
```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
```
```
sudo apt-get update
```
```
sudo apt-get install helm
```
## Example aws eks

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: testDeepchainTracker
  region: ap-south-1

nodeGroups:
  - name: ng-1
    instanceType: t3.micro
    desiredCapacity: 2
    volumeSize: 15
    ssh:
      allow: true
      
  ```
  ## Some command
  ```
 create_cluster:
	eksctl create cluster -f cluster.yaml

delete_cluster:
	eksctl delete cluster -f cluster.yaml

describe_cluster:
	eksctl utils describe-stacks --region=ap-south-1 --cluster=testDeepchainTracker

aws_identity:
	aws sts get-caller-identity

set_context:
	eksctl utils write-kubeconfig --cluster=testDeepchainTracker --set-kubeconfig-context=true
```
## Example of loadbalancer application
```
# ---
# apiVersion: v1
# kind: Namespace
# metadata:
#   name: internal-tracker
---
apiVersion: v1
kind: Service
metadata:
  name: tracker
  # namespace: internal-tracker
spec:
  selector:
    app: tracker
  ports:
    - port: 80
      targetPort: 3000
 #     nodePort: 30005
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tracker
  # namespace: internal-tracker

spec:
  replicas: 2
  selector:
    matchLabels:
      app: tracker
  template:
    metadata:
      labels:
        app: tracker
    spec:
      containers:
        - name: tracker
          image: shihab24/testapp:latest
#          restartPolicy: Never
          ports:
            - containerPort: 3000
          env:
            - name: TZ
              value: Asia/Dhaka
          imagePullPolicy: Always
#       restartPolicy: Never
      # imagePullSecrets:
      # - name: deepchain
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: tracker
  # namespace: internal-tracker

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name:  tracker
  minReplicas: 1
  maxReplicas: 2
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 75
  - type: Resource
    resource:
      name: memory
      target:
        type: AverageValue
        averageValue: 400Mi
```
* create this example name file name ``` app.yml ``` 

``` nano app.yml #create and edit file ```

``` 
kubectl apply -f app.yml  # run this application 
kubectl get svc # get all services
kubectl get pods # get all pods
kubectl get deployment # get all deployment
kubectl get hpa # get autoscalling
kubectl get all # get all deployment , autoscalling, services, pods
```



