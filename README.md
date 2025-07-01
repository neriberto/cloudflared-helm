# Clouflare ZeroTrust Chart

## Como usar

Para instalar o chart, você pode seguir os passos padrão do Helm:

```bash
helm install nome-do-release ./caminho-para-o-chart

```

## ArgoCD

Exemplo de deploy com ArgoCD.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cloudflared-tunnel
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/neriberto/cloudflared-helm
    targetRevision: main
    path: charts/cloudflared-tunnel
    helm:
      values: |
        token: "YOUR_CLOUDFLARED_TOKEN_HERE"
  destination:
    server: https://kubernetes.default.svc
    namespace: zerotrust
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

```