# Plan your Deployment

In this section, we are going to briefly discuss considerations for migrating your application workloads to `Stakater App Agility Platform (SAAP)`.

## Evaluating the Application

You need to gather knowledge about the application. Following questions can be considered for evaluating your application requirements:

### Do you need to run your application as container?

Application(s) needs to evaluated carefully for whether they need to be run as containerized workloads or not. The advantages and disadvantages of running applications in containers must be kept in mind while making this decision. Containers are an abstraction around processes and almost all applications can be containerized. However, they might not provide value for all use cases.

- [Why is Containerization not for Everyone?](https://medium.com/@HirenDhaduk1/why-is-containerization-not-for-everyone-48043e9290ac)
- [To containerize or not to containerize](https://www.mirantis.com/blog/containers-vs-vms-eternal-debate/)

### Is it stateless or stateful?

Evaluate whether you application is stateless or stateful. Stateless apps are easy to deploy and require less configurations compared to stateful apps. They don't require a persistent data storage or a stable network IP address and leverage other services. If an application doesn't require any stable identifiers or ordered deployment, deletion, or scaling, you should deploy your application using a workload object that provides a set of stateless replicas.

### What components make up the application being migrated? Is it a single service or a collection of services that work together?

Evaluate your application for any related services or components that are required for running it such as databases, message queues, caches. Evaluate whether you need to deploy these services along with your workloads or use external Software as a service (SaaS) products. An example can be workloads using AWS S3 for artifact storage and ease themselves of the overhead of managing blob storage on the cluster.

### What users need to access this service ?

Evaluate whether application needs to be exposed externally or only within the cluster. An example can be database running on OpenShift which should only be exposed within the cluster.

### What configurations are required by the application ?

Evaluate the application to identify external service credentials, internal configuration files, certificates required for proper function. You will need to provision appropriate resource `(configmaps or secrets)` on the cluster depending on whether the information.

### What files and directories are used by the application ?

Evaluate what configurations are provided as files. For each file or directory, determine whether the content is static, configuration, or dynamic. You can use mount `persistent volumes`, `configmaps` and `secrets` as files to containers filesystem depending on requirements.

### Do you want to restrict access in/out from your app ?

Evaluate if you want to restrict ingress/egress for your application. You'll use network policies for implementing this.

## Understanding Kubernetes objects for apps

You can define many objects in Kubernetes for various use.

### What is a Pod ?

Pod is the smallest unit in Kubernetes and it runs one or more containers with shared network and filesystem. Typically, pod will contain a single container, but other containers can be run for assistance such as container syncing static files from remote source, containers reading logs from a shared volume and transferring them into a log aggregation system.

### What type of Kubernetes objects can I make for my app?

You can make many different types of workloads for your application. The following table describe all workloads and why you might need them.

| Workload    | Usage                                                                                                                                                                                                                                                                                                                                                                           |
|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pod         | Pod is the smallest unit in Kubernetes and it runs one or more containers with shared network and filesystem. It is recommended to manage pods with Kubernetes controllers listed below to avoid downtime,updates,scaling. Find More Info on Pods [here](https://kubernetes.io/docs/concepts/workloads/pods/)                                                                   |
| ReplicaSet  | ReplicaSet ensure a multiple replicas of pods keep running. If your pod is deleted, it schedules another pod. Find More Info on ReplicaSet [here](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)                                                                                                                                                        |
| Deployment  | Deployment is a controller that manages ReplicaSets, It allows you to manage updates, scaling and `rollouts`. Find More Info on Deployment [here](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)                                                                                                                                                          |
| StatefulSet | StatefulSets is a controller that manages ReplicaSets, like deployments, but ensures that your pod has a unique network identity and storage that maintains its state across rescheduling. This type of workload is used for stateful applications like databases. Find More Info on StatefulSet [here](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) |
| DaemonSet   | DaemonSet is a controller that runs same pod on every worker node typically used log collection applications. Find More Info on DaemonSet [here](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)                                                                                                                                                          |
| Job         | Job ensures that a number of pods are completed successfully. You can use jobs for batch or parallel processing or configuring other services. Additionally, you can use CronJob to schedule Job at certain times. Find More Info on Job [here](https://kubernetes.io/docs/concepts/workloads/controllers/job/)                                                                 |

### What if I want my app configuration to use variables? How do I add these variables to the YAML?

If you application used environments variables or configuration files, you can define them in separate ConfigMap or Secrets. You can reference values from these resources and specify them as either files at required path or environment variables.

Typically, [ConfigMaps](https://kubernetes.io/docs/concepts/configuration/configmap/) are used for storing non sensitive configuration information. [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are used for sensitive information like system credentials or personally identifiable information.

### How can I make sure that my app has the correct resources?

You can configure your pod YAML or pod template to set resource (CPU/Memory) limits and requests for each container.

Resource Quotas can be setup by Administrators to enforce resource limits and prioritize pods with [Resource Quotas](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) and [Pod Priority](https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/).

### How can I add capabilities to my app configuration?

There can be various different configuration available for your pod running your application.
Visit the following link to view tutorials and documentation to [configure pods and containers](https://kubernetes.io/docs/tasks/configure-pod-container/)

### How can I increase the availability of my app?

There are various methods to increase the availability of your application.

- Use deployments and replica sets to deploy your app and its dependencies, Include enough replicas for your app's workload, plus two.
- Assign pods to dedicated worker nodes with node selectors and Spread pods across multiple nodes (anti-affinity) More Info [here](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)
- Distribute pods across multiple zones or regions. More Info [here](https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/)

### How can I update my app ?

Manage your Kubernetes Yaml inside source code repository. We recommend packaging applications as Helm Chart along with source code. You can Kustomize for reusing your configuration. You can update Kubernetes YAML files and update them with `oc apply` for raw Kubernetes Manifests and `helm template chart_dir | oc apply` or `helm install chart_dir` for Helm Charts.

You can use different strategies for update your Application. You might start with a rolling deployment or instantaneous switch before you progress to a more complicated canary deployment.

- Rolling deployment
You can use Kubernetes-native functionality to create a v2 deployment and to gradually replace your previous v1 deployment. This approach requires that apps are backwards-compatible so that users who are served the v2 app version don't experience any breaking changes.

- Instantaneous switch
This refers to creating a newer version of your application and wait for it to be ready, modifying the service to send traffic to the newer deployment.
It is referred to as a blue-green deployment.

- Canary or A/B deployment
This strategy enables you to test a new application version on a real user base without committing to a full `rollout`. You pick a percentage of users such as 5% and send them to the new app version. You collect metrics in your logging and monitoring tools on how the new app version performs, do A/B testing, and then roll out the update to more users.

See [Deployment Updates](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)
See [StatefulSet Updates](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#update-strategies)

### How can I scale my app?

There can be multiple ways to scale your application.

- Use Horizontal Pod Autoscaler (HPA) to specify how OpenShift Container Platform should automatically increase or decrease the scale of a replication controller or deployment configuration, based on metrics collected from the pods that belong to that replication controller or deployment configuration. See [Horizontal Pod AutoScaling Kubernetes](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) & [Horizontal Pod AutoScaling Openshift](https://docs.openshift.com/container-platform/4.9/nodes/pods/nodes-pods-autoscaling.html)

- Use Vertical Pod Autoscaler Operator (VPA) to automatically reviews the historic and current CPU and memory resources for containers in pods and can update the resource limits and requests based on the usage values it learns. See [Vertical Pod AutoScaling](https://docs.openshift.com/container-platform/4.9/nodes/pods/nodes-pods-vertical-autoscaler.html)

### How can I automate my app deployment?

Setup a CI/CD pipeline to for Continuous Integration and Continuous Deployments. We will be using Tekton Pipelines for building, testing and packaging your applications and ArgoCD for deploying of your application.

### How can I expose my application ?

Users can expose your application in/out the cluster using Services, Ingresses, Routes.

- Users can create two types of services for external networking: NodePort, LoadBalancer.
- Users can create Routes to exposes a service at a host name, such as www.example.com, so that external clients can reach it by name.
- Users can create Ingresses on a host name which is being watched by ingress controller and creates one or more routes to satisfy the conditions of the ingress object.

OpenShift has a built-in `HAProxy` ingress controller that allows users to expose services externally with routes and ingress.

Read more about Routes vs Ingress [here](https://cloud.redhat.com/blog/kubernetes-ingress-vs-openshift-route)

Read More about Routes Configuration [here](https://docs.openshift.com/container-platform/4.12/networking/routes/route-configuration.html)
