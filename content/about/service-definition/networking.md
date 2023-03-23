# Networking

## Custom domains for applications

To use a custom hostname for a route, you must update your DNS provider by creating a canonical name (CNAME) record. Your CNAME record should map the OpenShift canonical router hostname to your custom domain. The OpenShift canonical router hostname is shown on the Route Details page after a Route is created. Alternatively, a wildcard CNAME record can be created once to route all subdomains for a given hostname to the cluster’s router.

## Custom domains for cluster services

Custom domains and subdomains are not available for the platform service routes, for example, the API or web console routes, or for the default application routes.

## Domain validated certificates

SAAP includes TLS security certificates needed for both internal and external services on the cluster. For external routes, there are two, separate TLS wildcard certificates that are provided and installed on each cluster, one for the web console and route default hostnames and the second for the API endpoint. Let’s Encrypt is the certificate authority used for certificates. Routes within the cluster, for example, the internal API endpoint, use TLS certificates signed by the cluster’s built-in certificate authority and require the CA bundle available in every pod for trusting the TLS certificate.

## Load-balancers

## Network usage

## Cluster ingress

## Cluster egress
