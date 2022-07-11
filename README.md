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
docker build -t theia-cloud-operator:template -f dockerfiles/operator/Dockerfile .
docker tag theia-cloud-operator:template theiacloud/theia-cloud-operator:template
docker push theiacloud/theia-cloud-operator:template
```
