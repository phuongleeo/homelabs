# Update
- To update charts, run:
backup custom values and virtual service template:

```bash
cp jenkins/templates/virtualservice.yaml ../../
```
- Check upstream for new versions: https://artifacthub.io/packages/helm/jenkinsci/jenkins

```bash
helm repo update
rm -rf jenkins
# Pull the latest version of the Jenkins chart
helm pull jenkinsci/jenkins --version x.x.x --untar
```

- Move those 2 files back to the jenkins/templates directory:

```bash
mv ../../jenkins/templates/virtualservice.yaml jenkins/templates/
```
# Add these lines to the controller section of values.yaml

```yaml
controller:
  istioIngressGateways:
    name: jenkins-ingressgateway
```
# Test the chart

```bash
helm template jenkins jenkinsci/jenkins \
  --namespace jenkins \
  --create-namespace \
  -f values.yaml
  -s templates/virtualservice.yaml
```

# Jenkins Helm Chart

```bash
helm install jenkins jenkinsci/jenkins \
  --namespace jenkins \
  --create-namespace \
  -f values.yaml
```