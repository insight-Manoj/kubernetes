- A multi-master Kubernetes cluster is a highly available (HA) setup where multiple master nodes (control planes) work together to prevent a single point of failure (SPOF). This setup ensures the cluster remains operational even if one master node fails.
- ![k8s](https://github.com/user-attachments/assets/53c91cfb-c905-420f-8432-03d906879b83)
-A Kubernetes cluster consists of two main types of nodes: Master Nodes (ControlPlane) – Manage the cluster.Worker Nodes (Compute Nodes) – Run the applications.

1)Master nodes handle cluster management & orchestration and run the following core components:
**API Server (kube-apiserver)**	The entry point for all requests to the cluster.
**Controller Manager (kube-controller-manager)**	Manages controllers like node, job, and endpoint controllers.
**Scheduler (kube-scheduler)**	Assigns workloads (Pods) to worker nodes.
**ETCD (Distributed Key-Value Store)**	Stores all cluster data (configurations, pod state, etc.).
**Cloud Controller Manager**	Integrates Kubernetes with cloud provider APIs.
-In a multi-master setup, multiple master nodes share these responsibilities.
-ETCD must be replicated across master nodes for data consistency.
	
2)Worker Nodes (Compute Nodes)
Worker nodes execute containerized workloads and contain the following components:
**Kubelet**	 Manages pod execution on the node.
**Kube-Proxy**	Manages networking and communication between pods.
**Container Runtime**	Runs containers (Docker, containerd, CRI-O).
**Pods** Group of containers running together.
-Worker nodes are managed by the control plane and execute applications.
-Pods are scheduled on worker nodes based on resource availability.

A multi-master cluster improves fault tolerance & scalability by:
**Load Balancing** → Distributes API requests among master nodes.
**ETCD Replication** → Ensures cluster state is synchronized.
**Failover Mechanisms** → If a master node fails, others take over.
**Distributed Scheduling** → Multiple schedulers prevent bottlenecks.

Multi-Master Deployment Strategies
**1)Stacked ETCD (Internal)**
ETCD runs inside master nodes.
Simpler but less scalable.
**2)External ETCD Cluster**
ETCD runs on separate nodes.
More reliable for large-scale deployments.

**Use Cases for Multi-Master Kubernetes**
o	Production-grade Kubernetes clusters
o	High availability & disaster recovery
o	Large-scale cloud deployments (AWS EKS, GKE, AKS, On-Prem K8s)

**Controller**                                                             **Purpose**                                               **Use Case**
Deployment	                                            Declarative Pod updates and rollbacks	                        Scalable and update-friendly applications
ReplicationController	                     Maintain a fixed number of Pod replicas(uses equaliry based selectors)	          Basic replication needs
ReplicaSet	                          Advanced replication with set based selectors	                                  Underlying Deployment management
StatefulSet	                              Manage stateful apps with unique Pods                                          	Databases, stateful services
DaemonSet	                                        One Pod per node	                                                           Cluster-wide services
Job	                                                 Execute a task to completion	                                          Batch jobs or one-time tasks
CronJob	                                           Scheduled task execution	                                                          Periodic jobs
HPA	                                                    Scale Pods based on metrics	                                                 Autoscaling
Custom Controllers	                                Extend Kubernetes functionality	                                               Custom workflows
____________________________________________________________________________________________________________________________________________________________________________
Ingress	                           component that manages external access to services within a                    Ingress Controller = API Gateway for Kubernetes Services.                                     Kubernetes cluster, typically via HTTP(S) routes. It allows                                                                                                               controlled traffic routing, load balancing, SSL termination, and security for services.	

Manages External Access → Routes traffic from outside the cluster to services inside.
✔ Load Balancing → Distributes traffic across multiple pods.
✔ SSL/TLS Termination → Handles HTTPS connections with TLS certificates.
✔ Path-based & Host-based Routing → Directs traffic to different services based on URL paths or domains.
✔ Security & Authentication → Supports OAuth, JWT, and Web Application Firewall (WAF).
✔ Custom Rules & Annotations → Allows flexible configurations per application.
____________________________________________________________________________________________________________________________________________________________________________
Ingress Controller	Best For
Nginx Ingress	General-purpose, highly configurable
Traefik	Lightweight, dynamic routing, supports gRPC
AWS ALB Ingress	Best for AWS EKS (integrates with ALB)
HAProxy Ingress	High-performance, scalable
Istio Gateway	Works with Istio service mesh
____________________________________________________________________________________________________________________________________________________________________________
![image](https://github.com/user-attachments/assets/ce348bd6-68b2-4f96-8216-a1f9e7d9574d)

Kubernetes Deployments:

•	Deployment- Main aim is to maintain the replicas of Pod at any given point of time.
- Deployment Controller is ideal for managing stateless applications(user session data never saves data @ server side, uses CommonAuthorizationGateway/tokens to validate users) or any workload requiring frequent updates with minimal downtime.
-features: Declarative update, pod scaling, rolling updates, rollbacks, self-healing

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.21.6
        ports:
        - containerPort: 80
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1

Common Commands
Create a Deployment:
**kubectl apply -f deployment.yaml**

View deployment status:
**kubectl get deployments
kubectl describe deployment my-deployment**

Scale the deployment:
**kubectl scale deployment my-deployment --replicas=5**

Update the deployment:
**kubectl set image deployment/my-deployment nginx=nginx:1.21.7**

Pause/Resume the deployment:
**kubectl rollout pause deployment/my-deployment
kubectl rollout resume deployment/my-deployment**	

Rollback deployment:
**kubectl rollout undo deployment/my-deployment**



