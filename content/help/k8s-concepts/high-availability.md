# High availability

## About high availability

High availability (HA) is a core discipline in an IT infrastructure to keep your apps up and running, even after a partial or full site failure. The main purpose of high availability is to eliminate potential points of failures in an IT infrastructure. For example, you can prepare for the failure of one system by adding redundancy and setting up fail-over mechanisms.

Availability and disaster avoidance are extremely important aspects of any application platform. SAAP provides many protections against failures at several levels, but customer-deployed applications must be appropriately configured for high availability. In addition, to account for cloud provider outages that might occur, other options are available, such as deploying a cluster across multiple availability zones or maintaining multiple clusters with fail-over mechanisms.

### What level of availability is best?

You can achieve high availability on different levels in your IT infrastructure and within different components of your cluster. The level of availability that is correct for you depends on several factors, such as your business requirements, the Service Level Agreements that you have with your customers, and the resources that you want to expend.

## Potential points of failure

SAAP provides many features and options for protecting your workloads against downtime, but applications must have proper architecture to take advantage of these features.

SAAP can help further protect you against many common Kubernetes issues by adding Stakater Site Reliability Engineer (SRE) support and the option to deploy a multi-zone cluster, but there are a number of ways in which a container or infrastructure can still fail. By understanding potential points of failure, you can understand risks and appropriately architect both your applications and your clusters to be as resilient as necessary at each specific level.

!!! note "NOTE"

 An outage can occur at several different levels of infrastructure and cluster components.

SAAP provides several approaches to add more availability to your cluster by adding redundancy and anti-affinity. Review the following image to learn about potential points of failure and how to eliminate them.

### Potential failure point 1: Container or pod availability

Containers and pods are, by design, short-lived and can fail unexpectedly. Appropriately scaling services so that multiple instances of your application pods are running protects against issues with any individual pod or container. The node scheduler can also ensure that these workloads are distributed across different worker nodes to further improve resiliency.

When accounting for possible pod failures, it is also important to understand how storage is attached to your applications. Single persistent volumes attached to single pods cannot leverage the full benefits of pod scaling, whereas replicated databases, database services, or shared storage can.

To avoid disruption to your applications during planned maintenance, such as upgrades, it is important to define a pod disruption budget. These are part of the Kubernetes API and can be managed with the OpenShift CLI (oc) like other object types. They allow the specification of safety constraints on pods during operations, such as draining a node for maintenance.

### Potential failure point 2: Worker node availability

A worker node is a VM that runs on physical hardware. Worker node failures include hardware outages, such as power, cooling, or networking, and issues on the VM itself. You can account for a worker node failure by setting up multiple worker nodes in your cluster.

Worker nodes are the virtual machines that contain your application pods. By default, SAAP cluster has a minimum of three worker nodes for a single availability-zone cluster. In the event of a worker node failure, pods are relocated to functioning worker nodes, as long as there is enough capacity, until any issue with an existing node is resolved or the node is replaced. More worker nodes means more protection against single node outages, and ensures proper cluster capacity for rescheduled pods in the event of a node failure.

!!! note "NOTE"

 When accounting for possible node failures, it is also important to understand how storage is affected.

### Potential failure point 3: Cluster availability

SAAP clusters have at least three control plane nodes and three infrastructure nodes that are preconfigured for high availability, either in a single zone or across multiple zones depending on the type of cluster you have selected. This means that control plane and infrastructure nodes have the same resiliency of worker nodes, with the added benefit of being managed completely by Stakater.

In the event of a complete control plane node outage, the OpenShift APIs will not function, and existing worker node pods will be unaffected. However, if there is also a pod or node outage at the same time, the control plane nodes will have to recover before new pods or nodes can be added or scheduled.

All services running on infrastructure nodes are configured by Stakater to be highly available and distributed across infrastructure nodes. In the event of a complete infrastructure outage, these services will be unavailable until these nodes have been recovered.

The Kubernetes master is the main component that keeps your cluster up and running. The master stores cluster resources and their configurations in the etcd database that serves as the single point of truth for your cluster. The Kubernetes API server is the main entry point for all cluster management requests from the worker nodes to the master, or when you want to interact with your cluster resources. To protect your cluster master from a zone failure: create a cluster in a multi-zone location, which spreads the master across zones or consider setting up a second cluster in another zone.

### Potential failure point 4: Zone availability

A zone failure from a public cloud provider affects all virtual components, such as worker nodes, block or shared storage, and load balancers that are specific to a single availability zone. Failures include power, cooling, networking, or storage outages, and natural disasters, like flooding, earthquakes, and hurricanes. To protect against a zone failure, SAAP provides the option for clusters that are distributed across three availability zones, called multi-availability zone clusters. Existing stateless workloads are redistributed to unaffected zones in the event of an outage, as long as there is enough capacity.

### Potential failure point 5: Region availability

Every region is set up with a highly available load balancer that is accessible from the region-specific API endpoint. The load balancer routes incoming and outgoing requests to clusters in the regional zones. The likelihood of a full regional failure is low. However, to account for this failure, you can set up multiple clusters in different regions and connect them by using an external load balancer. If an entire region fails, the cluster in the other region can take over the workload.

### Potential failure point 6: Storage availability

If you have deployed a stateful application, then storage is a critical component and must be accounted for when thinking about high availability. A single block storage PV is unable to withstand outages even at the pod level. The best ways to maintain availability of storage are to use replicated storage solutions, shared storage that is unaffected by outages, or a database service that is independent of the cluster.
