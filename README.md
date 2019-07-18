# Multi-Container-Pods-in-Kubernetes


Simple tutorial to demonstrate the concept of packaging multiple containers into a single pod. 
* Web Pod has a Python Flask container and a Redis container
* DB Pod has a MySQL container
* When data is retrieved through the Python REST API, it first checks within Redis cache before accessing MySQL
* Each time data is fetched from MySQL, it gets cached in the Redis container of the same Pod as the Python Flask container
* When the additional Web Pods are launched manually or through a Replica Set, co-located pairs of Python Flask and Redis containers are scheduled together

![Architecture](https://github.com/sangam14/Multi-Container-Pods-in-Kubernetes/blob/master/multi-container-pod.png?raw=true)

Make sure that you have access to a Kubernetes cluster.

```
Biradars-MacBook-Air-4:~ sangam$ minikube start
😄  minikube v1.2.0 on darwin (amd64)
🔥  Creating virtualbox VM (CPUs=2, Memory=2048MB, Disk=20000MB) ...
🐳  Configuring environment for Kubernetes v1.15.0 on Docker 18.09.6
🚜  Pulling images ...
🚀  Launching Kubernetes ... 
⌛  Verifying: apiserver proxy etcd scheduler controller dns
🏄  Done! kubectl is now configured to use "minikube"
Biradars-MacBook-Air-4:~ sangam$ git clone https://github.com/sangam14/Multi-Container-Pods-in-Kubernetes.git
Cloning into 'Multi-Container-Pods-in-Kubernetes'...
remote: Enumerating objects: 34, done.
remote: Counting objects: 100% (34/34), done.
remote: Compressing objects: 100% (29/29), done.
remote: Total 34 (delta 8), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (34/34), done.
Biradars-MacBook-Air-4:~ sangam$ cd Multi-Container-Pods-in-Kubernetes/
Biradars-MacBook-Air-4:Multi-Container-Pods-in-Kubernetes sangam$ cd Build/
Biradars-MacBook-Air-4:Build sangam$ ls
Dockerfile		app.py			docker-compose.yml	readme.md		requirements.txt
Biradars-MacBook-Air-4:Build sangam$ 

Biradars-MacBook-Air-4:~ sangam$ git clone https://github.com/sangam14/Multi-Container-Pods-in-Kubernetes.git
Cloning into 'Multi-Container-Pods-in-Kubernetes'...
remote: Enumerating objects: 34, done.
remote: Counting objects: 100% (34/34), done.
remote: Compressing objects: 100% (29/29), done.
remote: Total 34 (delta 8), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (34/34), done.
Biradars-MacBook-Air-4:~ sangam$ cd Multi-Container-Pods-in-Kubernetes/
Biradars-MacBook-Air-4:Multi-Container-Pods-in-Kubernetes sangam$ cd Build/
Biradars-MacBook-Air-4:Build sangam$ ls
Dockerfile		app.py			docker-compose.yml	readme.md		requirements.txt
Biradars-MacBook-Air-4:Build sangam$ docker build  -t sangam14/py-red-sql .
Sending build context to Docker daemon   7.68kB
Error response from daemon: Bad response from Docker engine
Biradars-MacBook-Air-4:Build sangam$ docker build  -t sangam14/py-red-sql .
Sending build context to Docker daemon   7.68kB
Step 1/3 : FROM python:2.7-onbuild
2.7-onbuild: Pulling from library/python
d660b1f15b9b: Pull complete 
46dde23c37b3: Pull complete 
6ebaeb074589: Pull complete 
e7428f935583: Pull complete 
0c3de61682aa: Pull complete 
56f10ddf1173: Pull complete 
4473537c621d: Pull complete 
3106f7df3d1c: Pull complete 
3de1c6ceef68: Pull complete 
Digest: sha256:5af88e1d011bf7e845e83813712d9f91be1a39e2ede092008fc53e0a0ce1333b
Status: Downloaded newer image for python:2.7-onbuild
# Executing 3 build triggers
 ---> Running in 096955170f27
Collecting flask (from -r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/9b/93/628509b8d5dc749656a9641f4caf13540e2cdec85276964ff8f43bbb1d3b/Flask-1.1.1-py2.py3-none-any.whl (94kB)
Collecting redis (from -r requirements.txt (line 2))
  Downloading https://files.pythonhosted.org/packages/ac/a7/cff10cc5f1180834a3ed564d148fb4329c989cbb1f2e196fc9a10fa07072/redis-3.2.1-py2.py3-none-any.whl (65kB)
Collecting mysql-python (from -r requirements.txt (line 3))
  Downloading https://files.pythonhosted.org/packages/a5/e9/51b544da85a36a68debe7a7091f068d802fc515a3a202652828c73453cad/MySQL-python-1.2.5.zip (108kB)
Collecting click>=5.1 (from flask->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/fa/37/45185cb5abbc30d7257104c434fe0b07e5a195a6847506c074527aa599ec/Click-7.0-py2.py3-none-any.whl (81kB)
Collecting itsdangerous>=0.24 (from flask->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/76/ae/44b03b253d6fade317f32c24d100b3b35c2239807046a4c953c7b89fa49e/itsdangerous-1.1.0-py2.py3-none-any.whl
Collecting Werkzeug>=0.15 (from flask->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/d1/ab/d3bed6b92042622d24decc7aadc8877badf18aeca1571045840ad4956d3f/Werkzeug-0.15.5-py2.py3-none-any.whl (328kB)
Collecting Jinja2>=2.10.1 (from flask->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/1d/e7/fd8b501e7a6dfe492a433deb7b9d833d39ca74916fa8bc63dd1a4947a671/Jinja2-2.10.1-py2.py3-none-any.whl (124kB)
Collecting MarkupSafe>=0.23 (from Jinja2>=2.10.1->flask->-r requirements.txt (line 1))
  Downloading https://files.pythonhosted.org/packages/fb/40/f3adb7cf24a8012813c5edb20329eb22d5d8e2a0ecf73d21d6b85865da11/MarkupSafe-1.1.1-cp27-cp27mu-manylinux1_x86_64.whl
Installing collected packages: click, itsdangerous, Werkzeug, MarkupSafe, Jinja2, flask, redis, mysql-python
  Running setup.py install for mysql-python: started
    Running setup.py install for mysql-python: finished with status 'done'
Successfully installed Jinja2-2.10.1 MarkupSafe-1.1.1 Werkzeug-0.15.5 click-7.0 flask-1.1.1 itsdangerous-1.1.0 mysql-python-1.2.5 redis-3.2.1
You are using pip version 10.0.1, however version 19.1.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
Removing intermediate container 096955170f27
 ---> 22384f3b59b2
Step 2/3 : EXPOSE 5000
 ---> Running in e64049f5c985
Removing intermediate container e64049f5c985
 ---> 1df738cafe71
Step 3/3 : CMD [ "python", "app.py" ]
 ---> Running in 23f63db2145c
Removing intermediate container 23f63db2145c
 ---> 67e9b2023fa7
Successfully built 67e9b2023fa7
Successfully tagged sangam14/py-red-sql:latest
Biradars-MacBook-Air-4:Build sangam$ 
```




## Build a Docker image from existing Python source code and push it to Docker Hub. Replace DOCKER_HUB_USER with your Docker Hub username.
```
cd Build
docker build . -t <DOCKER_HUB_USER>/py-red-sql
docker push <DOCKER_HUB_USER>/py-red-sql

Biradars-MacBook-Air-4:Build sangam$ docker push sangam14/py-red-sql
The push refers to repository [docker.io/sangam14/py-red-sql]
f40aa3d7f00d: Pushed 
36dbcd55c7c6: Pushed 
527c622cfaff: Pushed 
3e397f5b8357: Mounted from library/python 
e257add70b4b: Mounted from library/python 
ce7e990ce056: Mounted from library/python 
633d23790c1d: Mounted from library/python 
d071a18d9802: Mounted from library/python 
8451f9fe0016: Mounted from library/python 
858cd8541f7e: Mounted from library/python 
a42d312a03bb: Mounted from library/python 
dd1eb1fd7e08: Mounted from library/python 
latest: digest: sha256:6e875b18d5c53223c092d62fadf76e3237074813a1d36ea60c2cf55111bee944 size: 2844
Biradars-MacBook-Air-4:Build sangam$ 

```

## Deploy the app to Kubernetes
```
cd ../Deploy
kubectl create -f db-pod.yml
kubectl create -f db-svc.yml
kubectl create -f web-pod-1.yml
kubectl create -f web-svc.yml
```
```
Biradars-MacBook-Air-4:~ sangam$ cd Multi-Container-Pods-in-Kubernetes/
Biradars-MacBook-Air-4:Multi-Container-Pods-in-Kubernetes sangam$ ls
Build			Deploy			README.md		multi-container-pod.png
Biradars-MacBook-Air-4:Multi-Container-Pods-in-Kubernetes sangam$ cd Deploy/
Biradars-MacBook-Air-4:Deploy sangam$ kubectl create -f db-pod.yml
pod/mysql created
Biradars-MacBook-Air-4:Deploy sangam$ kubectl create -f db-svc.yml
service/mysql created
Biradars-MacBook-Air-4:Deploy sangam$ kubectl create -f web-pod-1.yml
pod/web1 created
Biradars-MacBook-Air-4:Deploy sangam$ kubectl create -f web-svc.yml
service/web created
Biradars-MacBook-Air-4:Deploy sangam$ 
```

## Check that the Pods and Services are created
```
kubectl get pods
kubectl get svc
```
```
Biradars-MacBook-Air-4:Deploy sangam$ kubectl get pods
NAME    READY   STATUS              RESTARTS   AGE
mysql   0/1     ContainerCreating   0          65s
web1    0/2     ContainerCreating   0          64s
Biradars-MacBook-Air-4:Deploy sangam$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        14m
mysql        ClusterIP   10.104.154.233   <none>        3306/TCP       74s
web          NodePort    10.98.123.39     <none>        80:31414/TCP   71s
Biradars-MacBook-Air-4:Deploy sangam$ 
```


## Get the IP address of one of the Nodes and the NodePort for the web Service. Populate the variables with the appropriate values
```
kubectl get nodes
kubectl describe svc web

kubectl get nodes
export NODE_IP=<NODE_IP>
export NODE_PORT=<NODE_PORT>
```
```
Biradars-MacBook-Air-4:Deploy sangam$ kubectl get nodes
NAME       STATUS   ROLES    AGE   VERSION
minikube   Ready    master   15m   v1.15.0
Biradars-MacBook-Air-4:Deploy sangam$ kubectl describe svc web
Name:                     web
Namespace:                default
Labels:                   app=demo
                          name=web
Annotations:              <none>
Selector:                 name=web
Type:                     NodePort
IP:                       10.98.123.39
Port:                     http  80/TCP
TargetPort:               5000/TCP
NodePort:                 http  31414/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
Biradars-MacBook-Air-4:Deploy sangam$ 
```

## Initialize the database with sample schema
```
curl http://$NODE_IP:$NODE_PORT/init
```
## Insert some sample data
```
curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "1", "user":"John Doe"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "2", "user":"Jane Doe"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "3", "user":"Bill Collins"}' http://$NODE_IP:$NODE_PORT/users/add
curl -i -H "Content-Type: application/json" -X POST -d '{"uid": "4", "user":"Mike Taylor"}' http://$NODE_IP:$NODE_PORT/users/add
```

## Access the data 
```
curl http://$NODE_IP:$NODE_PORT/users/1
```
## The second time you access the data, it appends '(c)' indicating that it is pulled from the Redis cache
```
curl http://$NODE_IP:$NODE_PORT/users/1
```

## Create 10 Replica Sets and check the data
```
kubectl create -f web-rc.yml
curl http://$NODE_IP:$NODE_PORT/users/1
```
```
Biradars-MacBook-Air-4:Deploy sangam$ kubectl create -f web-rc.yml
replicationcontroller/web created
Biradars-MacBook-Air-4:Deploy sangam$ 
```

