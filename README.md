# Mondane Setup

```bash
helm upgrade --install --namespace kube-system prometheus prometheus-community/kube-prometheus-stack -f kube-prometheus-stack.yaml
helm upgrade --install --create-namespace --namespace mondane-user blackbox-exporter prometheus-community/prometheus-blackbox-exporter -f prometheus-blackbox-exporter.yaml
for f in mondane/*; do (sops --decrypt "$f" 2>/dev/null || cat "$f") | kubectl apply -f - ; done
```
