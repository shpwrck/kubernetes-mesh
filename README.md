# Connecting Kubernetes Clusters

## Problem Statement

There are several ways to enable multicluster service discovery, but various pitfalls and tradeoffs in each method.
The evaluation below does not take into consideration factors outside of the technical implementation (velocity, community, ownership, etc). 

## Solutions

### Underlay Network

Standard networking tools such as BGP can be used with most modern CNIs to connect clusters together. This is a lightweight solution (no additional components need to be installed), but brittle and dangerous approach. Misconfiguring BGP can have a wide area of effect. This approach would reduce the amount of translation and manipulation of the raw data as it is transported.

* [calico](https://github.com/projectcalico/calico)
  * [this talk](https://av.tib.eu/media/56957) describes how to do it with calico, but the example is strictly for educational purposes
* [cilium](https://github.com/cilium/cilium)
  * The existence of [Cluster Mesh](https://docs.cilium.io/en/stable/network/clustermesh/#multicluster-network-root) suggests that BGP should not be a primary tool for multicluster networking with Cilium.

### Overlay Network

Adding additional tools allows for less disruption, but adds additional maintenance overhead. Several tools exist using Wireguard or other Layer 3 VPNs (IPSEC) as an overlay network that allows you to establish VPN Tunnels between clusters with varying levels of granularlity (i.e. per service, per namespace, per labels). The solutions in this category are compatible with most CNIs, but often require a specific set of configurations from the existing CNI (non-overlapping pod cidrs). The actual implementation varies between each project, but the fundamental components are roughly the same: vpn tunnel, gateways, controlplane/operator, authentication service, secrets management. Before adopting a solution in this space take note of the project velocity, community support, and stewardship.

* [kubeslice](https://github.com/kubeslice/kubeslice)
  * Created by [Avesha](https://avesha.io/)
  * Not a CNCF project
* [liqo](https://github.com/liqotech/liqo)
  * LiqoTech is the organization supporting Liqo
  * Not a CNCF project
* [submariner](https://github.com/submariner-io/)
  * Unaffiliated, but largely maintained by RedHat
  * Is a CNCF Project
* [kilo](https://github.com/squat/kilo)
  * Unaffialted to my knowledge
  * Is a CNCF Project
* [netmaker](https://github.com/gravitl/netmaker) <- Do It Yourself Option
  * Created by [Netmaker](https://netmaker.io)
  * Not a CNCF project

### Network Agnostic

The final category can be installed on top of most CNI configurations without issue or requiring a specific IP setup. The level of kubernetes complexity is higher than the Overlay Networks because of the introduction of sidecars, additional DNS configuration, bundled ingress/egress, etc, but no additional networks are required*. Most of these options are in the Service Mesh category with the exception of "Skupper". Skupper is unique in that it replicates the same type of behavior as you would see in a traditional VPN, but at a higher layer in the networking stack (Layer 7). The solutions here will impact data plane traffic the most of all three options, but the proxies are generally within acceptable levels.

* [istio](https://github.com/istio/istio)
* [linkerd](https://github.com/linkerd/linkerd2)
* [consul](https://github.com/hashicorp/consul)
* [skupper](https://github.com/skupperproject)* <- Skupper creates a Layer 7 network