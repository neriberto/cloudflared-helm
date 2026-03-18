# Clouflare ZeroTrust Chart

## Como usar

Para instalar o chart, você pode seguir os passos padrão do Helm:

```bash
helm install nome-do-release ./caminho-para-o-chart

```

## ArgoCD

Exemplo de deploy com ArgoCD (recomendação: não colocar secrets em claro no Application).

1) Crie o Secret que contém o token no namespace de destino (ex.: `zerotrust`). O nome do Secret deve seguir o padrão usado pelo chart: `<release>-cloudflared-token` — para a Application de exemplo abaixo use `cloudflared-tunnel-cloudflared-token`:

```bash
kubectl -n zerotrust create secret generic cloudflared-tunnel-cloudflared-token \
  --from-literal=token='SEU_CLOUDFLARED_TOKEN_AQUI'
```

2) Exemplo de `Application` do ArgoCD (sem o token embutido):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cloudflared-tunnel
  namespace: argocd
spec:
  destination:
    namespace: zerotrust
    server: https://kubernetes.default.svc
  source:
    path: charts/cloudflared-tunnel
    repoURL: https://github.com/neriberto/cloudflared-helm
    targetRevision: main
    helm:
      parameters:
        - name: createNamespace
          value: 'true'
        - name: namespace
          value: zerotrust
  sources: []
```

Boas práticas:

- Nunca coloque `token` ou outros segredos em claro no repositório ou no campo `helm.values` do `Application`.
- Considere usar ferramentas como SealedSecrets, ExternalSecrets, ou `argocd-vault-plugin` para gerenciar secrets de forma segura.
- Para reprodutibilidade, prefira pinagem em `targetRevision` por tag ou SHA em vez de `main`.

