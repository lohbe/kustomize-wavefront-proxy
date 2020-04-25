# Wavefront Proxy Kustomization
![CI](https://github.com/lohbe/kustomize-wavefront-proxy/workflows/CI/badge.svg)

This repository consolidates advanced configuration of [Wavefront proxy](https://docs.wavefront.com/proxies.html) through [Kustomize](https://kustomize.io). This allows easy reference of configuration parameters and deployment of a customised Wavefront proxy in Kubernetes. This is a good starting point to modify Wavefront proxy deployment to existing automation pipelines, especially if not using the Wavefront [Helm chart](https://docs.wavefront.com/kubernetes.html#kubernetes-quick-install-using-helm).

Source: Wavefront Proxy Advanced Configuration [Guide](https://docs.wavefront.com/proxies_configuring.html)

## Getting Started

This guide assumes that you are familiar with Kustomize and have a ready cluster (e.g. minikube) with `kubectl` to run/test the kustomizations.

* Install [Kustomize](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/INSTALL.md) on your machine
* Clone this repository and note the structure as follows:

`base/` - adds [`wavefront.conf`](https://github.com/wavefrontHQ/wavefront-proxy/blob/master/pkg/etc/wavefront/wavefront-proxy/wavefront.conf.default) and [`log4j.xml`](https://github.com/wavefrontHQ/wavefront-proxy/blob/master/pkg/etc/wavefront/wavefront-proxy/log4j2.xml.default) advanced configuration to upstream K8s proxy [manifest](https://raw.githubusercontent.com/wavefrontHQ/wavefront-kubernetes/master/wavefront-proxy/wavefront.yaml). Environment variables override configuration files.

`overlays/memory-limit/` - overlays, or "inherits" `base/` and limits memory use of Wavefront proxy by adjusting Java heap size and patching the K8s [container](https://docs.wavefront.com/proxies_configuring.html#configuring-a-proxy-in-a-container) resource limits.

* Add your own `WAVEFRONT_TOKEN` and `WAVEFRONT_URL` credentials to `base/kustomization.yaml`

* To preview the generated manifest, run in `base/` or `memory-limit/` directory:

`$ kustomize build`

* To apply the generated manifest:

`$ kustomize build | kubectl apply --namespace wavefront -f -`

Alternatively, you may use built-in support for Kustomize: `kubectl apply -k .`

* To cleanup and remove the applied manifests, replace `apply` with `delete`

### Note about secrets management

This kustomization does not integrate K8s secrets management. For non-trivial deployments, it is recommended to keep your Wavefront tokens safe. For more details, please visit [Vault](https://github.com/hashicorp/vault) or [SealedSecrets](https://github.com/bitnami-labs/sealed-secrets), depending on your environment and security needs.

## Future Improvements

- Add native remote loading of upstream resources when this feature stabilises in Kustomize. [1], [2]
- Add [preprocessor](https://github.com/wavefrontHQ/wavefront-proxy/blob/master/pkg/etc/wavefront/wavefront-proxy/preprocessor_rules.yaml.default) rules configuration

[1]: https://github.com/kubernetes-sigs/kustomize/pull/2167
[2]: https://github.com/kubernetes-sigs/kustomize/issues/970

