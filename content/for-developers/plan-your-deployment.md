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
Evaluate whether application needs to be exposed publically or only within the cluster. An example can be database running on OpenShift which should only be exposed within the cluster.

### What configurations are required by the application ?
Evaluate the application to identify external service credentials, internal configuration files, certificates required for proper function. You will need to provision appropriate resource `(configmaps or secrets)` on the cluster depending on whether the information.

### What files and directories are used by the application ?
Evaluate what configurations are provided as files. For each file or directory, determine whether the content is static, configuration, or dynamic. You can use mount `persistent volumes`, `configmaps` and `secrets` as files to containers filesystem.

## Understanding Kubernetes objects for apps

