# TOPICS COVERED
- Containers
- ECS - Concepts and Clutermode
- Elastic Container Registry (ECR)
- Kubernetes 101
- Elastic Kubernetes Service (EKS)

## Containers
In virtual machines, The OS takes up most of the disk and memory space, leaving very little for applications to run on them. Many of these OS may also be the same, hence we are duplicating a lot. 

A container is similar to a VM - provides an isolated environment for applications to run in. A container differs from a VM in that it uses the host's OS, and not its own, making them much lighter than a VM. We use a container engine on top of the Host's OS to run applications in independent envs. Each dabba on level 4 is a container in the figure below. 
![alt text](<Screenshots/Screenshot 2024-06-11 at 5.39.23 PM.png>)

### Docker
#### Docker images
- Built using a dockerfile in layers, each layer is a file system.
- Built from scratch or from a base image (First line of dockerfile) - the layer that goes at the very bottom.
- Each subsequent line from a dockerfile has some changes (additional file systems) that are added to the image. 
- End result is a docker image having individual file system layers. 

#### Docker containers
- Made from docker images, they are a running copy of the docker image along with an additional read/write file system layer.
- Multiple containers can be made from one image, which only differ in the read/write layers.  

#### Container registry - Docker Hub
- A hub of docker images which can be used for making containers on different hosts. 

## Elastic Container Service (ECS)
`ECS : Containers :: EC2 : Virtual Machines`
![alt text](<Screenshots/Screenshot 2024-06-11 at 6.45.45 PM.png>)

- Container definition: Where the container is stored, the container image used and what port is exposed. `The only container standard supported is Docker.`
- Task definition: A collection of container definitions, CPU, memory, task role (`IAM role assumed by task`) - usually all those containers needed for an application. 
- Service definition: how we want a task to scale - we can have multiple, independent copies of our task. Helps with high availability. 


### Cluster Types
Defines how much admin overhead the customer manages and how much AWS manage. 
ECS management engine: High level component that handles scheduling and orchestration, Cluster management, Placement engine. Present in both cluster types. 
1. EC2 mode 
    - EC2 instances are used to run containers within a VPC. (OS + networking ability)
    - Container images that can be found in the container registry are deployed onto hosts (EC2 instances) with the task and service definitions.
    - `Use` when we have large workloads and we are price conscious

2. Fargate mode 
    - Its a cluster model that has no server (Network only).
    - Containers are deployed on `fargate shared infrastructure` based on task and service definitions. 
    - Containers are then injected into a VPC, and given an ENI each. 
    - If the VPC has public addresses, the task and services inside the container are now given public addresses. 
    - `Use` if we are overhead conscious, have small or burst workloads, batch or periodic workloads. 

## Elastic Container Registry (ECR)
- AWS provided service to host and manage container images. 
- Each AWS account has a public and private registry. Registries have repos, repos have container images.
- Public registries allow read-only access to all. read-write needs explicit permission. Private registries require permissions for everything. 
- Offers container image replication in cross-region and cross-account. 
- Offers close to real time metrics that can be logged into Cloudwatch.
- logs API actions to cloudtrail.

## Kubernetes 101
Kubernetes is a container orchestration - It manages multiple docker containers and enables efficient use. 
### Clusters
Clusters are run by the control plane, which controls the nodes. Each node has containers in it, along with kubelets which communicate w the control plane through the kubernetes API. 
![alt text](<Screenshots/Screenshot 2024-06-11 at 7.11.15 PM.png>)

![alt text](<Screenshots/Screenshot 2024-06-11 at 7.21.06 PM.png>)

## Elastic Kubernetes Service (EKS)
- Kubernetes on AWS.
- Control plane is managed by AWS, scales to run on multiple AZs. 
- Integrates with AWS services like Elastic Container Registry (ECR), Elastic Load Balancer (ELB), IAM, VPC.
- The EKS cluster is the control plane + nodes
- the etcd is distributed accross multiple AZs. 
![alt text](<Screenshots/Screenshot 2024-06-11 at 7.29.05 PM.png>)
