# Onboarding

Stakater provisions the Stakater App Agility Platform (SAAP) and takes first application to production in less than four weeks on the cloud of your choice!

## Customer Data Collected

This is a complete list of the customer data and permissions needed for getting started with SAAP:

* Stakater asks customer if they want logging enabled - logging is optional
* Stakater needs to know which cloud provider the customer is using
* Stakater asks the customer what Identity Provider is used for the cloud provider
* Stakater asks the customer to create a user in their cloud provider and download the credentials
* Stakater asks if the customer has any preference for their cluster name
* Stakater needs permissions to git repo access to set up pipelines - pipeline setup is optional

## Week 1 - Provisioning - Free

* Provision SAAP on the cloud of your choice
    * Stakater can arrange exclusive offers with cloud providers
* Configure access to systems
* On-boarding session and knowledge sharing
* Configure SSO

## Week 2-4 - Application Migration - Time and Material - Optional

* Select the first application or group of applications that work together
    * Setup infrastructure and application GitOps repositories for deploying application and dependent configurations
    * Setup tenant operator configuration and decide on the number of environments if required
    * Divide applications into microservices and containerize them
    * Deploy the application onto the cluster using Kubernetes Resources. List out secret required, dependent services, databases, storage requirements and implement them accordingly.
    * Deploy Kubernetes Resources using [Stakater Developed Helm Chart](https://github.com/stakater/application) on the environment chosen - prod, stage, test, dev - in the application GitOps configuration
    * Implement CI pipeline for the application if required. Deploy Tekton CI pipeline using the `Stakater Tekton Chart`
    * Implement monitoring, logging, validation/testing frameworks or any additional tools for the application
    * Validate
* Educate team on key concepts

## Next steps

* Reach out to Stakater for migrating next application, or
* Self-serviced migration of next application

## Customer Responsibilities

* Provide timely access to necessary people, systems, and information from relevant departments and teams
* Thorough engagement from management and teams

## Stakater Team

* Dedicated team
    * Project management: 20-40%
    * Solution architect: 50%
    * AppDev consultant: 100%

## Output

* First application live as container with cloud native CI/CD workflow
* Up-skilled team with key concepts of cloud native containerized workflow

## Customer Data Processing

This is a complete list of how customer data is processed in SAAP:

* Logs and events for Kubernetes resources are stored in ElasticSearch which is deployed in the customer's cluster
* Customer's cluster is deployed in the account of the customer's cloud provider
* Logs are retained for 7 days unless the customer requests otherwise
* In case customer wants to forward events to a data store they can do that optionally
* Infrastructure alerts are sent to Stakater Slack for incident management
* Application alerts are sent to ElasticSearch and can optionally be sent to Stakater Slack if requested
