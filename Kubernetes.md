# Kubernetes
Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation

# The evolution of building a system
![Flow](https://i.imgur.com/RjKwaxb.png)

### Container Deployment
* Container(sharing OS) will be much lighter than VM system. 
* Easier to deploy and rollback
* Similar to a VM, a container has its own filesystem, CPU, memory, process space.


# Why you need Kubernetes?
* Minimum the downtime if application collapse occurs.
  - self-healing by killing dead nodes, restart the nodes and container.
* Easy to do node load balacing.

# Things Kubernetes won't help
* Does not deploy source code and does not build your application (CI/CD) - So, we use GitLab CI/CD

# [Kubernetes Cluster - Key Components](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)
This part we are going to overview the components of a kubernetes cluster.

### ***1. Nodes***
A Kubernetes cluster consists of a ***set of worker machines, called nodes***, that run containerized applications. Every cluster has at least one worker node.

- changing node topology of your cluster by regist it to your K8s API SERVER.


### ***2. Pods***
- The worker node(s) host the Pods that are the components of the application workload. 
- The smallest and simplest unit in the Kubernetes object model that you create or deploy.

 Q. How to let multiple containers works together? 
   - Each Pod is assigned a ***unique IP address***
   - Containers ***inside a Pod*** can communicate with one another using ***localhost***

### ***3. Jobs***
What is the differences between Jobs & Pods?
- Job is higher-level than Pods
  - Job creates one or more Pods and ensures that a specified number of them successfully terminate.
  - Deleting a Job will clean up the Pods it created.

### 4. ***Volume***
It's like disk, we append it on a Pod. Even the job is end, the volume will be preserve.

# How to deploy on k8s?
Write a [yaml] (https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) with k8s objects to deploy.



# Useful commands
* ***kubectl get*** - get the nodes
* ***kubectl describe***

* Forwarding Pods port to local (Because we can't access the Pods directly)
  - ***kubectl port-forward -n group-news service/mysql 3306***

* ***kubectl get service*** 

* Start a kubernete pods with namespace TEST
  - ***kubectl apply -f [file(json, yaml)] -n TEST***

* ***kubectl cp some-namespace/some-pod:/tmp/foo /tmp/bar***
  - just like scp [target][dest]

