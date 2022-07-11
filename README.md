# Theia.cloud customization

This guide is currently based on the development branch: https://github.com/eclipsesource/theia-cloud/tree/develop

This guide is currently based on the `0.8.0.MS1` tags of the docker images.

## Cutomize Theia Deployment Template

If you need to cutomize more than ports, theia-image, resource-limits, etc., you may customize the kubernetes resource templates used by Theia.cloud.
You may find the currently used templates here: https://github.com/eclipsesource/theia-cloud/tree/develop/java/operator/org.eclipse.theia.cloud.operator/src/main/resources
The templates may be adapted by copying new tempaltes in the `/templates/` directory of the operator docker image. The operator will check whether a template of the same name exists in this directory and will use this template if it exists.
Please see the `custom-operator` for a sample that adds an additional side-care container (`nginx-hello-world`) and adjusts the security context to run as the root user (which allows to install e.g. curl to test access to the nginx).
Usually both `templateDeployment.yaml` and `templateDeploymentWithoutOAuthProxy.yaml` should be customized. The first template is used when keycloak is available, the second one is used when there is no keycloak.

Currently this customization was build like this (available as `theiacloud/theia-cloud-operator:template`):

```bash
docker build -t theia-cloud-operator:template -f custom-operator/Dockerfile .
docker tag theia-cloud-operator:template theiacloud/theia-cloud-operator:template
docker push theiacloud/theia-cloud-operator:template
```

## Helm

### Prerequisites for K8s-Cluster

Please install cert-manager: https://cert-manager.io/docs/installation/

Theia.cloud used the nginx-ingress controller within its default templates and within the helm-chart templates. Please install nginx ingress controller: https://kubernetes.github.io/ingress-nginx/deploy/

### Multiple Helm Chart installations

Currently the helm chart also installs some cluster-wide resources. This may cause issues when the helm chart is used to install Theia.cloud in multiple namesspaces, because the cluster-wide resources may already have been installed.
For this guide, we have moved the cluster-wide resources under the `k8s`-directory. Those have to be applied before running `helm install`.

### Adjust values.yaml

You should at least adjust:

* app.id
* issuer.email
* image.name
* hosts.service
* hosts.landing
* hosts.instance
* operator.image (our custom image from above)

### Adjust helm/theia.cloud/templates/theia-appdefinition-spec.yaml

This is the definition of our app to deploy. Most of the values that are part of the spec will be filled into `templateDeployment.yaml` and `templateDeploymentWithoutOAuthProxy.yaml` we've adjusted above.
