# KubernetesStuff

### [Nana Video Tutorial](https://www.youtube.com/watch?v=X48VuDVv0do&t=3s):

* **What is Kubernetes | Basic Explanation:**
  * Official Definition
  * Problem-Solution case study
  * Basic architecture (Master-Slave, K8S processes)
  * Basic concepts
  * Example Configuration

Kubernetes in an open source container orchestration tool, developed by google. It helps you manage containerized application in different environment like: physical, virtual machine, cloud or even hybrid environment.

So, what problems does kubernetes solves? Helps the transition from monolithic design to microservices. It ensures high availability or not downtime, scalability or high performance, disaster recovery, backup/restore etc.

* **Kubernetes Components | Pods, Services, Secrets, ConfigMap:**
  * Node and Pod
  * Service and Ingress
  * ConfigMap and Secret
  * Volumes
  * Deployment and StatefulSet
  * Main K8s components summarized

**Node:** Node is like a simple server, physical or virtual machine. 

**Pod:** Smallest unit of Kubernetes. It's an abstraction over container, creates a runtime environment for the container. K8s wants to abstract away the container technologies, so that you can replace them if you want to & you don't directly need to work with docker/container etc. So, you only interact with the K8s layer. Usually an application runs a single Pod but multiple can be run, in which case they are very tightly coupled. 

Each Pod gets an IP address, not the container. They can communicate using this IP address. Pods are ephimeral, means that they can die very easily. When a pod dies & gets replaced, it gets a new IP address, which is mostly inconvenient to manage. Because of this change in IP address, Service is used.

**Services:** A service has a permanent IP address. Every Pod has it's own service. Lifecycle of Service & Pod are not connected. Even if a Pod dies, the service IP remains as it is. App should be accessible by browser. For this we need an external service to open communication from external services. But, you won't allow your databases to open on public requests. For that, we can create something called Internal Service. So, this is the type of service you specify when creating one. However, the URL of the external service isn't very practical. So, you want a service protocol & domain name. For that, there's another component called Ingress. So, the request first goes to Ingress & then forwarded to the service.

**ConfigMap & Secret:** Suppose we have a DB called mongoDB-service, that's usually configures in the application or in env file which is inside the image. Now, if we update the URL of the DB, then we need to rebuild the image, push it to docker hub, pull the image again & then use it, which is a very tedious process for a small change. To handle these stuff, K8s has a component called ConfigMap. 

So, ConfigMap is an external configuration for our application. We just connect the ConfigMap to the Pod, so that Pod has the data of the ConfigMap. Now, in order to change/update any config, we adjust the ConfigMap only & that's it.

**Secret:** On the other hand, user details like passwords can also be part of the configMap. But, putting sensitive informations like passwords in configMap wouldn't be secure. For this purpose, K8s has another component called Secret. It's just like configMap, but secured to store secrets like passwords, which serves it as base64 encoded form, not as plain text. We can use it as env, variable or as a property file.

**Volumes:** Data storage. Volumes can help in data persistance, even if pods stop or get replaced. It attaches a physical storage on a hard-drive to your pod, that storage can be on local/remote machine, outside of the K8s cluster.

Think of storage as an external hard-drive plugged in K8s cluster. Because, K8s doesn't explicitely manage any data persistance, you're responsible for keeping up data, replicating data, managing data, etc.

**Deployment & Stateful Set:** Say if I built an image again or my pod dies & I restart it, then my application would have a downtime(which isn't good). So, instead of having one Node, we replicate everything & say have another Node (Node-2) with the same stuff, which is also connected to the service. Not only service has a permanent IP address, but also works as a load balancer. So, it'll catch the request & forward it to whichever pos is less busy. 

We won't be simply creating a second replica, rather we'll create a blueprint & specify how many replica of that pod we'll require. This blueprint is called Deployment. 

So, in practice you won't be creating pods, rather the deployment where you'll specify how many replicas you want. This way, you can scale up/down your service. 

So, deployment is another abstracion of Pods. But, for database, we can't simply replicate DB using deployment. Because database has a state, which is its data. If we have replicas of databases, they need to access the same shared data store. There you'd need kind of mechanism to manage which pod is currently reading/writing from the storage. That mechanism in addition to replication feature is given by StatefulSet. So, this component is mostly for databases or any other Stateful application. It'll do the scale up/down just like deployments. However, StatefulSet is rather difficult, so, it's a common practice to host the DBs outside of K8s cluster & communicate with this external database.

* **Kubernetes Architecture:**
  *  Worker Nodes
  *  Master Nodes
  *  Api Server
  *  Scheduler
  *  Controller Manager
  *  etcd - the cluster brain

There're two types of K8s nodes, Worker nodes & Master nodes. 3 processes must be installed on every node. Woker nodes do the actual work. A container runtime must be there in every node (docker for example). Application pods have container running inside, so container runtime needs to be installed on every node. But, the process that scedules those pods & containers underneth is Kubelet, which is a process of K8s itself. It has interface with both the container runtime & Node itself because kubelet is responsible for taking that configuration file & running/starting a pod with a container inside & assigning resources from that node to the container like CPU, RAM etc. Usually a K8s cluster will have multiple nodes & each of them must have container runtime & kubelet installed. The third process in Kube Proxy, which also must be installed in a K8s node. KubeProxy forwards the request. It has an intelligent forwarding logic inside, that ensures performance with low overhead in forwarding/communication.

Master node manages processed, how to schedule a pod, monitoring, re-schedule/re-start pod, join a new node etc. There are 4 processes that run on evevy master node. 

**API Server:** Basically cluster gateaway, also acts as a gatekeeper for authentication. We have only 1 entrypoint the the cluster. Some request -> API Server -> Validates request -> other processes -> Pod, which is also good for security.

**Scheduler:** If you send a request to schedule a new pod:
Schedule a new Pod -> API Server -> Scheduler -> Where to put the Pod? So, scheduler looks for a appropriate node to schelude the new pod deployment. Important to notice that, Scheduler just decides on which node new pod should be scheduled but Kubelet is the one that starts it.

**Controller Manager:** What happens when a pod dies in any node? CM detects state changes & tries to recover the cluster state asap. Controller manager -> Scheduler -> Kubelet -> 

**etcd:** Key-Value store for cluster state (the cluster brain). Cluster changes get stored in the key value store. So, how does Scheduer knows what resources are available or how Controller manager knows about the change in cluster state change? All of those information in stored in etcd. But, the actual application data in NOT stored in etcd (for example the DB data). Master processes are cruical for a cluster & there're usually multiple master for safety, where API server is load balanced & etcd forms a distributed storage across all the master nodes.

Master nodes require less resources CPU, RAM, STORAGE etc, but Worker nodes require more resources because they have more load of work.

* **Minikube and kubectl - Local Setup:**
  * What is minikube?
  * What is kubectl?
  * install minikube and kubectl
  * create and start a minikube cluster

**Minikube** is a tool that lets you run Kubernetes locally. minikube runs a single-node Kubernetes cluster on your personal computer (including Windows, macOS and Linux PCs) so that you can try out Kubernetes, or for daily development work. It's a one node cluster where the master process & the worker processes both run on one node & this node will have a docker container runtime pre-installed. 

**Kubectl:** You need a way to interact with the cluster, create pods & other K8s components. Minikube runs both master & processes. Master has the API Server, so to do anything you need to talk to the API Server using clients UI, API, CLI (Kubectl). Kubectl is not only for minikube cluster, but also for any other K8s clusters.

* **Main Kubectl Commands - K8s CLI:**
  * Get status of different components
  * create a pod/deployment
  * layers of abstraction
  * change the pod/deployment
  * debugging pods
  * delete pod/deployment
  * CRUD by applying configuration file

**CRUD commnads:**
**Create deployment:** `kubectl create deployment [name]`
**Edit deployment:** `kubectl edit deployment [name]`
**Delete deployment:** `kubectl delete deployment [name]`

**Status of different K8s components:** `kubectl get nodes | pods | services | replicset | deployment`

In kubernetes although pod is the smallest unit but we don't create pods directly, we create deployments. Deployment is the abstraction over pods. It's going to create the pod underneath. Deployment has all the information or blueprint to create pods. Most basic configuration for deployment (name & image to use), rest is default.

Replicaset is managing the replicas of a pod. You won't have to create/update/delete Replicaset. You'll be working with deployments directly, because in deployment you can configure the pod blueprint completely.

So, the layers of abstraction: Deployment -> ReplicaSet -> Pod -> Container & everything below the Deployment is handled by Kubernetes.

**Create deployment:** `kubectl create deployment name --image=image [options]`

**Debugging pods:**
**Log to console:** `kubectl logs [pod name]`
**Get Interactive Terminal:** `kubectl exec -it [pod name] -- /bin/bash`
**Applying configuration file:** `kubectl apply -f [filename.yaml]`

**Some Minikube Commands:**
| Commands | Description |
| -------- | ----------- |
| `minikube start` | Starts a local Kubernetes cluster |
| `minikube status` | Gets the status of a local Kubernetes cluster |
| `minikube stop` | Stops a running local Kubernetes cluster |
| `minikube delete` | Deletes a local Kubernetes cluster |
| `minikube dashboard` | Access the Kubernetes dashboard running within the minikube cluster |
| `minikube pause` | pause Kubernetes |
| `minikube unpause` | unpause Kubernetes |
| `minikube --help` | get help for the minikube commands |


* **Pods and Containers - Kubernetes Networking | Container Communication inside the Pod:**
  * Why is a Pod abstraction useful?
  * Container vs Pods
  * When are multiple containers necessary in a Pod?
  * How do containers communicate in a Pod?
  * Pods - solving the port allocation problem.
