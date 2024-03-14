# Welcome kubernetes bootstrap
### Cert-Manager
__Cert-Manager__ is a Kubernetes add-on that helps automate the management and issuance of TLS certificates. It integrates with Kubernetes to provide certificate lifecycle management as a native feature.

__Features:__
- **Automated Certificate Issuance:** Cert-Manager automates the process of obtaining and renewing TLS certificates from various issuing authorities like Let's Encrypt, Venafi, or a self-hosted CA.

- **Certificate Lifecycle Management:** Cert-Manager handles the entire lifecycle of TLS certificates, including issuance, renewal, and revocation, ensuring that HTTPS connections are always secured with valid certificates.

- **Integration with Ingress Controllers:** It works well with various Kubernetes ingress controllers, automatically configuring TLS termination for your ingress resources.

### Cilium (cni)
- __Cilium__ is an open source project that enables networking, security, and observability for Kubernetes clusters and other containerized environments. Cilium is based on a technology called eBPF, which can inject network control logic, security controls, and observability features directly into the Linux kernel. Cilium uses eBPF to provide high-performance networking, multi-cluster and multi-cloud capabilities, encryption, load balancing, and network security features.