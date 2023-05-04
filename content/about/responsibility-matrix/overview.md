# Responsibility matrix

This page describes the responsibilities of Stakater and customers with respect to the various parts of SAAP.

## Applications and data

Customers are completely responsible for their applications, workloads, and data that they deploy to SAAP. However, SAAP provides various tools to help them set up, manage, secure, integrate and optimize their apps as described in the following tab:

=== "Customer data"

    Stakater responsibilities | Customer responsibilities
    --- | ---
    <ul><li>Maintain platform-level standards for data encryption.</li><li>Provide OpenShift components to help manage application data such as secrets.</li><li>Enable integration with third-party data services (such as AWS RDS or Google Cloud SQL) to store and manage data outside the cluster and/or cloud provider.</li></ul> | <ul><li>Maintain responsibility for all customer data stored on the platform and how customer applications consume and expose this data.</li></ul>

=== "Customer applications"

    Stakater responsibilities | Customer responsibilities
    --- | ---
    <ul><li>Provision clusters with Red Hat OpenShift components installed, so that you can access the Red Hat OpenShift API to deploy and manage your containerized apps.</li><li>Provide OpenShift components to help manage application data such as secrets.</li><li>Provide a number of managed add-ons to extend your app's capabilities. Maintenance is simplified for you because Stakater provides the installation and updates for the managed add-ons.</li><li>Provide storage classes and plug-ins to support persistent volumes for use with your apps.</li><li>Provide a container image registry, so customers can securely store application container images on the cluster to deploy and manage applications.</li></ul> | <ul><li>Maintain responsibility for customer and third-party applications; data; and their complete lifecycle.</li><li>If you add community third-party your own or other services to your cluster such as by using Operators then you are responsible for these services and for working with the appropriate provider to troubleshoot any issues.</li><li>Use the provided tools and features to configure and deploy; keep up-to-date; set up resource requests and limits; size the cluster to have enough resources to run apps; set up permissions; integrate with other services; manage any image streams or templates that the customer deploys; externally serve; save back up and restore data; and otherwise manage their highly available and resilient workloads.</li><li>Maintain responsibility for monitoring the applications run on SAAP.</li></ul>

## Disaster Recovery

| Stakater responsibilities | Customer responsibilities |
| --- | --- |
| <ul><li>Recovery of Red Hat OpenShift in case of disaster.</li></ul> | <ul><li>Recovery of the workloads that run the cluster and your applications data.</li><li>If you integrate with other cloud services such as file, block, object, cloud database, logging, or audit event services, consult those services' disaster recovery information.</li></ul> |
