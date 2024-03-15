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
 - Install `Helm`
 - Disable other CNI in kubernetes
 - Set domain name to Loadbalancer for `Hubble UI`
 - Set variables in the defaults/main.yml

If you are using `AWX!` Enter your `Vault address` and `Token`. And specify the path where your kubernetes kubeconfig __json__ file is located! This is for connecting to kubernetes.

![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/70007c01-95e6-4e35-9499-f5d382171401)

 1. Set your **kubeconfig** path
 2. Write the `domain` name you previously set on the loadbalancer
 
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/0c90b546-cb5a-49e5-ba93-f3f1fb085f59)

***Hublle UI*** is encrypted for security by **basic-auth**. The **password** and **username** are stored in a secret named `save-hubble-basic-auth` in the `cilium namespace`

![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/0445851a-6b85-42db-a046-fac9e9b90b3a)
> [!TIP]
> You can easily read username and password using the following commands
```
kubectl get secret save-hubble-basic-auth -n cilium -o jsonpath="{.data.username}" | base64 --decode
```
```
kubectl get secret save-hubble-basic-auth -n cilium -o jsonpath="{.data.password}" | base64 --decode
```

### Cilium 
#### Required of you!
 - Install `Helm`
 - Set variables in the defaults/main.yml

If you are using `AWX!` Enter your `Vault address` and `Token`. And specify the path where your kubernetes kubeconfig __json__ file is located! This is for connecting to kubernetes.
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/07bfb55a-f132-4e5e-8d3d-8154af8174c1)

 1. Set `ingress_nginx_hostPort_enabled` "true" if the k8s cluster is `Bare Metal`
 2. Set `ingress_nginx_hostNetwork` "true" if the k8s cluster is `Bare Metal`
 3. Set `ingress_nginx_service_type` "ClusterIP" if the k8s cluster is `Bare Metal` 
 3. Set `ingress_nginx_kind` "DaemonSet" if the k8s cluster is `Bare Metal` 
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/90a8bff1-9452-4259-8661-5b9e783d0c72)

