# Welcome kubernetes bootstrap
### Cert-Manager
__Cert-Manager__ is a Kubernetes add-on that helps automate the management and issuance of TLS certificates. It integrates with Kubernetes to provide certificate lifecycle management as a native feature.

__Features:__
- **Automated Certificate Issuance:** Cert-Manager automates the process of obtaining and renewing TLS certificates from various issuing authorities like Let's Encrypt, Venafi, or a self-hosted CA.

- **Certificate Lifecycle Management:** Cert-Manager handles the entire lifecycle of TLS certificates, including issuance, renewal, and revocation, ensuring that HTTPS connections are always secured with valid certificates.

- **Integration with Ingress Controllers:** It works well with various Kubernetes ingress controllers, automatically configuring TLS termination for your ingress resources.

### Cilium (cni)
- __Cilium__ is an open source project that enables networking, security, and observability for Kubernetes clusters and other containerized environments. Cilium is based on a technology called eBPF, which can inject network control logic, security controls, and observability features directly into the Linux kernel. Cilium uses eBPF to provide high-performance networking, multi-cluster and multi-cloud capabilities, encryption, load balancing, and network security features.
### Hubble
- __Hubble__ is an observability and troubleshooting tool that integrates with Cilium to provide real-time visibility into network traffic within Kubernetes clusters. It captures network flow data and presents it in an intuitive user interface, allowing operators to understand how applications communicate and diagnose network-related issues quickly.

### Ingress-Nginx
 **Ingress-Nginx** is a Kubernetes Ingress controller that provides traffic routing, load balancing, and SSL termination for Kubernetes services. It is built on top of NGINX and designed to handle large-scale production workloads.
 - **HTTP(S) Load Balancing:** Ingress-Nginx efficiently distributes incoming HTTP and HTTPS traffic across your Kubernetes services.
 - **Path-Based Routing:**  Route traffic based on the request URL path to different backend services, allowing for fine-grained traffic control.
 - **Web Application Firewall (WAF):** Built-in support for ModSecurity provides protection against common web vulnerabilities.

### CSI

- **CSI hcloud** is a Container Storage Interface (CSI) driver specifically designed for integrating Hetzner Cloud Block Volumes with Kubernetes clusters. It allows Kubernetes users to easily provision, attach, and manage Hetzner Cloud Block Volumes as persistent volumes for their applications.
- **Longhorn** delivers simplified, easy to deploy and upgrade, 100% open source, cloud-native persistent block storage without the cost overhead of open core or proprietary alternatives, offering features such as snapshots, backups, and volume replication. 

## How to use ansible roles?

### Cilium 
#### Required of you!
 - Install **Helm** 
 - Disable other CNI in kubernetes
 - Set domain name to Loadbalancer for **Hubble UI** 
 - Set variables in the defaults/main.yml

![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/e05bcbf7-cd95-48ec-94f7-80e0b409f21d)
