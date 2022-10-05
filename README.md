# Assemble Platforms

This repository contains automation to install the assemble platform including supporting components. 

## Platform Provisioning Instructions

The charts directory contains Helm charts that can be used to setup supporting applications used by the Assemble platform. These charts are referenced by ArgoCD applications and composed in a root application which is designed to install all platform dependencies.

### Provision the platform using ArgoCD

Provision the ArgoCD instance:

```bash
oc new-project assemble-argocd
helm upgrade -i argocd charts/argocd
```

Apply the root application:

```bash
oc apply -f assemble-root.yaml
```

### Enable Cluster Authentication Integration with GitHub

Create a [GitHub OAuth Application](https://docs.github.com/en/developers/apps/building-oauth-apps/creating-an-oauth-app) within the desired GitHub organization

Configure the authorization callback URL following this pattern:
```
https://keycloak-rhsso.apps.<cluster wildcard domain>/auth/realms/openshift/broker/github/endpoint
```

Run the cluster authentication Helm chart and supply the client ID and a client secret for the GitHub OAuth app:
```bash
helm upgrade -i rhsso-cluster-auth charts/rhsso-cluster-auth -n rhsso --set keycloak.realm.identityProvider.clientId=<GITHUB_OAUTH_CLIENTID> --set keycloak.realm.identityProvider.clientSecret=<GITHUB_OAUTH_CLIENTSECRET>
```

### Configure Backstage Github Apps

1. Follow the steps from the [Backstage Documentation](https://backstage.io/docs/integrations/github/github-apps)
2. The CLI command will set up everything but some permissions are missing. Make sure you have the following permissions to the GitHubApp created by the CLI:

* You need Read and Write access for:
    * Actions
    * Administration
    * Contents

* You need Read Only access for:
    * Metadata
    * Members

## Chart Documentation

* [ArgoCD](/charts/argocd)
* [Generic Operator (OLM)](/charts/operator)
* [Red Hat SSO (cluster auth)](charts/rhsso-cluster-auth)
