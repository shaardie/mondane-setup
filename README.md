# Mondane Setup

```bash
# Install Cert-Manager with self-signed ca for testing
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm upgrade --install --namespace kube-system cert-manager jetstack/cert-manager -f cert-manager/cert-manager.yaml
kubectl apply -f cert-manager/clusterissuer.yaml

# Install Prometheus Operator
helm upgrade --install --namespace kube-system prometheus prometheus-community/kube-prometheus-stack -f kube-prometheus-stack.yaml

# Install blackbox exporter
helm upgrade --install --create-namespace --namespace mondane-user blackbox-exporter prometheus-community/prometheus-blackbox-exporter -f prometheus-blackbox-exporter.yaml

# Install Alertmanager, Prometheus base for Mondane
for f in mondane/*; do (sops --decrypt "$f" 2>/dev/null || cat "$f") | kubectl apply -f - ; done

# Install operator
git clone git@github.com:shaardie/mondane-operator.git
cd mondane-operator
make deploy
cd -

# Install UI
kubectl create namespace mondane-ui
kubectl apply -f mondane-ui/certificate.yaml

# Install API
git clone git@github.com:shaardie/mondane-api.git
helm upgrade --install --namespace mondane-ui mondane-api mondane-api/charts/mondane-api -f mondane-ui/mondane-api.yaml

# Install Oauth2 Proxy
helm repo add oauth2-proxy https://oauth2-proxy.github.io/manifests
helm upgrade --install --namespace mondane-ui oauth2proxy oauth2-proxy/oauth2-proxy -f <(sops --decrypt mondane-ui/oauth2-proxy.yaml)
```
