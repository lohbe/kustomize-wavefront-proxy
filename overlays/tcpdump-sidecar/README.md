## Wavefront Proxy - tcpdump Sidecar Overlay 

This Kustomize overlay extends `base/` kustomization and adds a patch to Wavefront proxy that injects a tcpdump sidecar container for additional troubleshooting.

# Getting Started

To run this overlay and deploy a wavefront proxy with tcpdump sidecar in an existing `wavefront` namespace, run `kustomize build | kubectl apply --namepsace wavefront -f -`

The tcpdump container is from [itsthenetwork](https://hub.docker.com/r/itsthenetwork/alpine-tcpdump/) and runs for 1 year (365d) upon deployment (the `infinity` time-range is not supported in the `alpine` base Docker image).

To access the tcpdump instance, run the sidecar as follows: `kubectl exec -it wavefront-proxy-cdjgka -c tcpdump-sidecar -- sh`

Run tcpdump: `/ # tcpdump -A 'tcp port 9411 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'` 

The command above captures all TCP payload (PUSH flag) from port 9411 (Zipkin HTTP traces) and decodes ASCII.