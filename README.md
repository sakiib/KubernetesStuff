# KubernetesStuff

## Resources

### Kubernetes Documentation:

* [Kubernetes Basics - Basic Modules](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
  * Create a Kubernetes cluster
  * Deploy an app
  * Explore your app
  * Expose your app publicly
  * Scale up your app
  * Update your app

---
### [Nana Video Tutorial](https://www.youtube.com/watch?v=X48VuDVv0do&t=3s):

* What is Kubernetes | Basic Explanation:
  * Official Definition
  * Problem-Solution case study
  * Basic architecture (Master-Slave, K8S processes)
  * Basic concepts
  * Example Configuration

Kubernetes in an open source container orchestration tool, developed by google. It helps you manage containerized application in different environment like: physical, virtual machine, cloud or even hybrid environment.

So, what problems does kubernetes solves? Helps the transition from monolithic design to microservices. It ensures high availability or not downtime, scalability or high performance, disaster recovery, backup/restore etc.

* Kubernetes Components | Pods, Services, Secrets, ConfigMap:
  * Node and Pod
  * Service and Ingress
  * ConfigMap and Secret
  * Volumes
  * Deployment and StatefulSet
  * Main K8s components summarized

Node: Node is like a simple server, physical or virtual machine. 

Pod: Smallest unit of Kubernetes. It's an abstraction over container, creates a runtime environment for the container. K8s wants to abstract away the container technologies, so that you can replace them if you want to & you don't directly need to work with docker/container etc. So, you only interact with the K8s layer. Usually an application runs a single Pod but multiple can be run, in which case they are very tightly coupled. 

Each Pod gets an IP address, not the container. They can communicate using this IP address. Pods are ephimeral, means that they can die very easily. When a pod dies & gets replaced, it gets a new IP address, which is mostly inconvenient to manage. Because of this change in IP address, Service is used.

A service has a permanent IP address. Every Pod has it's own service. Lifecycle of Service & Pod are not connected. Even if a Pod dies, the service IP remains as it is. App should be accessible by browser. For this we need an external service to open communication from external services. But, you won't allow your databases to open on public requests. For that, we can create something called Internal Service. So, this is the type of service you specify when creating one. However, the URL of the external service isn't very practical. So, you want a service protocol & domain name. For that, there's another component called Ingress. So, the request first goes to Ingress & then forwarded to the service.

ConfigMap & Secret: Suppose we have a DB called mongoDB-service, that's usually configures in the application or in env file which is inside the image. Now, if we update the URL of the DB, then we need to rebuild the image, push it to docker hub, pull the image again & then use it, which is a very tedious process for a small change. To handle these stuff, K8s has a component called ConfigMap. 

So, ConfigMap is an external configuration for our application. We just connect the ConfigMap to the Pod, so that Pod has the data of the ConfigMap. Now, in order to change/update any config, we adjust the ConfigMap only & that's it.

On the other hand, user details like passwords can also be part of the configMap. But, putting sensitive informations like passwords in configMap wouldn't be secure. For this purpose, K8s has another component called Secret. It's just like configMap, but secured to store secrets like passwords, which serves it as base64 encoded form, not as plain text. We can use it as env, variable or as a property file.

Volumes: Data storage. Volumes can help in data persistance, even if pods stop or get replaced. It attaches a physical storage on a hard-drive to your pod, that storage can be on local/remote machine, outside of the K8s cluster.

Think of storage as an external hard-drive plugged in K8s cluster. Because, K8s doesn't explicitely manage any data persistance, you're responsible for keeping up data, replicating data, managing data, etc.

Deployment & Stateful Set: Say if I built an image again or my pod dies & I restart it, then my application would have a downtime(which isn't good). So, instead of having one Node, we replicate everything & say have another Node (Node-2) with the same stuff, which is also connected to the service. Not only service has a permanent IP address, but also works as a load balancer. So, it'll catch the request & forward it to whichever pos is less busy. 

We won't be simply creating a second replica, rather we'll create a blueprint & specify how many replica of that pod we'll require. This blueprint is called Deployment. 

So, in practice you won't be creating pods, rather the deployment where you'll specify how many replicas you want. This way, you can scale up/down your service. 

So, deployment is another abstracion of Pods. But, for database, we can't simply replicate DB using deployment. Because database has a state, which is its data. If we have replicas of databases, they need to access the same shared data store. There you'd need kind of mechanism to manage which pod is currently reading/writing from the storage. That mechanism in addition to replication feature is given by StatefulSet. So, this component is mostly for databases or any other Stateful application. It'll do the scale up/down just like deployments. However, StatefulSet is rather difficult, so, it's a common practice to host the DBs outside of K8s cluster & communicate with this external database.
 
* Pods and Containers - Kubernetes Networking | Container Communication inside the Pod:
  * Why is a Pod abstraction useful?
  * Container vs Pods
  * When are multiple containers necessary in a Pod?
  * How do containers communicate in a Pod?
  * Pods - solving the port allocation problem.
