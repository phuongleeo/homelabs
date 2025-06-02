# Update
- Check upstream for new versions: https://artifacthub.io/packages/helm/jenkinsci/jenkins

# Generate gateway TLS certificate

```bash
openssl genrsa -out jenkins.key 2048
openssl req -new -key jenkins.key -out jenkins.csr
openssl x509 -req -days 365 -in jenkins.csr -signkey jenkins.key -out jenkins.crt
kubectl -n istio-system create secret tls jenkins-server-tls --cert=jenkins.crt --key=jenkins.key
```


# Final steps
- Navigate to the Jenkins UI at `http://your.jenkins.url/jenkins` (replace with your domain).
- Manage Jenkins > Systems > Jenkins URL: `http://your.jenkins.url/jenkins`

```bash
helm template jenkins . \
  --namespace jenkins \
  --create-namespace \
  -f values.yaml
```

# Jenkins Helm Chart

```bash
helm upgrade jenkins . \
  --namespace jenkins \
  --create-namespace \
  -f values.yaml
```