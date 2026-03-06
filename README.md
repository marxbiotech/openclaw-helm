# OpenClaw Helm Chart

A slim Helm chart for OpenClaw designed without Chromium browser integration. This chart focuses on minimal resource usage while maintaining full OpenClaw functionality for text-based interactions.

## Install

```bash
helm install openclaw oci://ghcr.io/thepagent/openclaw-helm --version 1.0.1
```

## Design Philosophy

This chart is intentionally slim and excludes the Chromium browser sidecar container to:
- Reduce memory and CPU usage
- Simplify deployment in resource-constrained environments
- Focus on text-based AI agent capabilities

If you need browser automation features, consider the official OpenClaw chart or enable browser support by modifying `config.browser.enabled` in values.yaml.

## Uninstall

```bash
helm uninstall openclaw-custom
```

## Configuration

Edit `values.yaml` to customize:

- `image.tag` - OpenClaw version
- `config.gateway.controlUi.allowedOrigins` - CORS origins
- `config.browser.enabled` - Enable/disable browser (default: false)
- `skills` - List of skills to install
- `resources` - CPU/memory limits

## AI Provider Authentication

**This chart deploys OpenClaw without pre-configured AI providers.** After installation, add your API key:

### OpenAI (Recommended)

Create a Kubernetes secret with your OpenAI API key:

```bash
kubectl create secret generic openclaw-api-key \
  --from-literal=OPENAI_API_KEY=sk-...
```

Update your Helm values and upgrade:

```yaml
# values.yaml
envFrom:
  - secretRef:
      name: openclaw-api-key
```

```bash
helm upgrade openclaw oci://ghcr.io/thepagent/openclaw-helm --version 1.1.0 -f values.yaml
```

### Verify Authentication

```bash
POD=$(kubectl get pod -l app.kubernetes.io/name=openclaw-helm -o jsonpath='{.items[0].metadata.name}')
kubectl exec $POD -- openclaw models status
```

### Other Providers

For Anthropic, Google, or other providers, use the same pattern:

```bash
kubectl create secret generic openclaw-api-key \
  --from-literal=ANTHROPIC_API_KEY=sk-ant-...
```

## Example: Add more skills

```yaml
skills:
  - weather
  - gog
  - your-skill-name
```
