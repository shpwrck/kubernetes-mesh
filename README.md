# Connecting Kubernetes Clusters

## Problem Statement

There are several ways to enable multicluster service discovery, but various pitfalls and tradeoffs in each method.
The evaluation below does not take into consideration factors outside of the technical implementation (velocity, community, ownership, etc). 

## Solutions

##### Underlay Network

Standard networking tools such as BGP can be used with most modern CNIs to connect clusters together. This is a lightweight solution (no additional components need to be installed), but brittle and dangerous approach. Misconfiguring BGP can have a wide area of effect. This approach also reduce the amount of translation and manipulation of the raw data as it is transported.

* [calico](https://github.com/projectcalico/calico)
* [cilium](https://github.com/cilium/cilium)


#### Overlay Network

Adding additional tools allows for less disruption, but adds additional maintenance overhead. Several tools exist using Wireguard as an overlay network that allow you to establish VPNs between clusters with varying levels of granularlity. The solutions in this category are compatible with most CNIs, but may require a specific set of configurations for the existing CNI. The actual implementation varies between each project, but the fundamental components are roughly the same: vpn tunnel, gateways, controlplane/operator, authentication service, secrets management.

##### Wireguard
* [kubeslice](https://github.com/kubeslice/kubeslice)
* [liqo](https://github.com/liqotech/liqo)
* [submariner](https://github.com/submariner-io/)
* [kilo](https://github.com/squat/kilo)
* [netmaker](https://github.com/gravitl/netmaker) <- Do It Yourself Option

#### Network Agnostic

The final category can be installed on top of most CNI configurations without issue. The level of kubernetes complexity has increased because of the introduction of sidecars and additional DNS configuration, but the no additional networks are required. Most of these options are in the Service Mesh category with the exception of "Skupper". Skupper is unique in that it replicates the same type of behavior as you would see in a traditional VPN, but at a higher layer in the networking stack (Layer 7).

* [istio](https://github.com/istio/istio)
* [linkerd](https://github.com/linkerd/linkerd2)
* [consul](https://github.com/hashicorp/consul)
* [skupper](https://github.com/skupperproject)

#### Links

* [Kubernetes Blog](https://www.cncf.io/blog/2021/04/12/simplifying-multi-clusters-in-kubernetes/)
* [FOSDEM Talk](https://av.tib.eu/media/56957)