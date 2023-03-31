# "SAAP or bare Kubernetes?" – The cost of building your own Kubernetes application platform

SAAP is often referred to as "enterprise Kubernetes," but it can be hard to understand what that really means at first glance. Customers frequently ask,
"SAAP or bare Kubernetes?" However, it's important to understand that SAAP already uses Kubernetes. In the overall SAAP architecture, Kubernetes provides
both the foundation on which the SAAP platform is built and much of the tooling for running SAAP.

Kubernetes is an incredibly important open-source project—one of the key projects of the Cloud Native Computing Foundation and an essential technology
in running containers.

However, the real question that potential users of SAAP might have is, "Can I just run my applications with Kubernetes alone?" Many organizations start
by deploying Kubernetes and find that they can get a container, even an enterprise application, running in just a few days. However, as the Day-2
operations begin, security requirements arise, and more applications are deployed, some organizations find themselves falling into the trap of building
their own platform as a service (PaaS) with Kubernetes technology. They add an open-source ingress controller, write a few scripts to connect to their
continuous integration/continuous deployment (CI/CD) pipelines, and then try to deploy a more complex application... and that is when the problems start.
If deploying Kubernetes were the tip of the iceberg, then the complexity of Day-2 management would be the hidden, vast, ship-sinking bulk of that same
iceberg under the water.

While it's possible to start solving these challenges and problems, it often takes an operations team of several people, and several weeks and months of
effort to build and maintain this "custom PaaS built on Kubernetes." This leads to inefficiency in the organization, complexity in supporting this from
a perspective of security and certification, as well as having to develop everything from scratch when onboarding developer teams. If an organization
were to list out all the tasks of building and maintaining this custom Kubernetes platform—that is, Kubernetes—with all the extra components needed to run
containers successfully, they would be grouped up as follows:

- **Cluster management:** This includes installing OSes, patching the OS, installing Kubernetes, configuring CNI networking, authentication integration,
Ingress and Egress setup, persistent storage setup, hardening nodes, security patching, and configuring the underlying cloud/multicloud.
- **Application services:** These include log aggregation, health checks, performance monitoring, security patching, container registry, and setting up
the application staging process.
- **Developer integration:** This includes CI/CD integration, developer tooling/IDE integration, framework integration, middleware compatibility, providing
application performance dashboards, and RBAC.

While there are many more actions and technologies that could be added to the list—most of those activities are essential for any organization to seriously
use containers—the complexity, time, and effort in just setting all of that up is insignificant to the ongoing maintenance of those individual pieces. Each
integration needs to be thoroughly tested, and each component and activity will have a different release cycle, security policy, and patches.
