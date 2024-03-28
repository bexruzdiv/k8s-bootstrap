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
### AWX settings
> [!WARNING]
> If you are using `AWX!` Follow the steps below. This is for connecting to kubernetes.

- Convert kubeconfig file to json format
```
yq eval -o=json .kube/config > config.json
```
- Send json formatted file to vault
```
vault kv put secret/awx/kubeconfig value=@config.json
```
> [!NOTE]
> Open AWX now and follow the steps below!

- Create credential type in AWX. From left menu "Credential Types" ➝ Add
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/baa6e99d-a80c-48a0-a7e1-85bdbfed2536)

- Name: Name for credential Type.
- Description: Description for Credential type (Oprional)
- Injector configuration: 
```
fields:
  - id: vault_url
    type: string
    label: Vault URL
  - id: role_id
    type: string
    label: App Role ID
  - id: secret_id
    type: string
    label: App Role Secret ID
    secret: true
required:
  - vault_url
  - role_id
  - secret_id
```
- Second Injector configuration:
```
env:
  VAULT_ADDR: '{{ vault_url }}'
  VAULT_ROLE_ID: '{{ role_id }}'
  VAULT_SECRET_ID: '{{ secret_id }}'
  VAULT_AUTH_METHOD: approle
extra_vars:
  ansible_hashi_vault_url: '{{ vault_url }}'
  ansible_hashi_vault_role_id: '{{ role_id }}'
  ansible_hashi_vault_secret_id: '{{ secret_id }}'
  ansible_hashi_vault_auth_method: approle
```

![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/ebdf64aa-823e-4632-921f-c93aa23f772d)

- Push "Save" button

> [!NOTE]
> Now open Vault (UI or CLI) and follow the steps below!

- Enable approle auth if it is necessary
  ```
  vault auth enable approle
  ```

- Create access policy for access from AWX (For example policy name is "awx-user-policy")
  ```
  vault policy write awx-user-policy - <<EOF
  path "secret/data/awx/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
  }
  EOF
  ```
- Create role in approle and attach access policy (For example user name is "awx-user" and policy name is "awx-user-policy")
  ```
  vault write auth/approle/role/awx-user \
    token_max_ttl=24h \
    token_ttl=1h \
    token_policies="awx-user-policy"
  ```

- Print role id and secret id (User "awx-user"). We use this ids for creating credential in AWX
  ```
  vault read auth/approle/role/awx-user/role-id
  ```
  ```
  vault write -f auth/approle/role/awx-user/secret-id
  ```

- We can write and read data from Vault for checking access polity
  ```
  vault kv put secret/awx/test test='working'  
  ```
  ```
  vault kv get -format=json secret/awx/test | jq ".data.data"
  ```

> [!NOTE]
> Create Credential in AWX

- From left menu "Credentials" ➝ Add
- Name: Name for credential
- Description: Description for Credential (Oprional)
- Organization: Choose organization
- Credential Type: Find choose credential type name, which we created above
- Vault URL: Url of vault server (For example https://vault.uz)
- App Role ID: Role id, which we get above 
- App Role Secret ID: Secret id, which we get above
- Push "Save" button 

## How to use ansible roles?

## Cilium 
#### Required of you!
 - Install `Helm`
 - Disable other CNI in kubernetes
 - Set domain name to Loadbalancer for `Hubble UI`
 - Set variables in the defaults/main.yml
1. Set the variable from defaults file to the path to the Vault where config.json is located (In my case: secret/awx/kubeconfig)

![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/9318f759-e8aa-4487-8441-d4f3dc23ffe5)



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

## Ingress-Nginx 
#### Required of you!
 - Install `Helm`
 - Set variables in the defaults/main.yml

If you are using `AWX!` Enter your `Vault address` and `Token`. And specify the path where your kubernetes kubeconfig __json__ file is located! This is for connecting to kubernetes.
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/07bfb55a-f132-4e5e-8d3d-8154af8174c1)

1. Set `ingress_nginx_hostPort_enabled` "true" if the k8s cluster is `Bare Metal`
2. Set `ingress_nginx_hostNetwork` "true" if the k8s cluster is `Bare Metal`
3. Set `ingress_nginx_service_type` "ClusterIP" if the k8s cluster is `Bare Metal` 
4. Set `ingress_nginx_kind` "DaemonSet" if the k8s cluster is `Bare Metal` 
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/90a8bff1-9452-4259-8661-5b9e783d0c72)

## Certmanager
#### Required of you!
 - Install `Helm`
 - Set variables in the defaults/main.yml
If you are using `AWX!` Enter your `Vault address` and `Token`. And specify the path where your kubernetes kubeconfig __json__ file is located! This is for connecting to kubernetes.
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/d79863d7-6def-41ee-9506-17852a247aa3)

1. Set path to your kube config. If you are using awx, don`t change it
2. Set your email to `certmanager_email`
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/6c31472e-49da-451f-a39a-376e55797d0e)

## CSI 
#### Required of you!
 - Install `Helm`
 - Set variables in the defaults/main.yml
 If you are using `AWX!` follow the steps below ! This is for connecting to kubernetes.
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/41ff33ce-ac15-45e4-9f6b-047de7c24934)
1. Set `cni_hcloud_check` to "true" if you use `hcloud`
2. Set path to your kube config. If you are using awx, don`t change it
3. Set `csi_hcloud_api_token` to your hetzner account `token`
![image](https://github.com/bexruzdiv/k8s-bootstrap/assets/107495220/b116f4a4-56f6-4d9f-ada8-b9199d925a60)

