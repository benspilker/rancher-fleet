## Install Nextcloud Instance in a GitOps method using Rancher Fleet

This repository is an extension of the Proxmox-K3s repository.
https://github.com/benspilker/proxmox-k3s

The assumption is that you already have a working Kubernetes Cluster with Rancher, meaning steps 1-4 in the Proxmox-K3s repository have been setup in your environment.

Another Prerequite is having a working Rancher Fleet Pipeline, See 0.A

---

0.A. **Prerequisite A, Fleet Pipeline** Assuming you already have a Rancher Fleet repository setup using Github for Continuous Delivery. See Jim's Garage: https://youtu.be/ulKimzGWtqc?si=uBBu3owusGF2BbDW

0.B. **Prerequisite B, Setup DNS**: Setup of DNS to ensure the Nextcloud domain is correctly resolved to the soon to be Ingress IP, ie nextcloud.yourexampledomain2.com resolves to 192.168.100.203.
See DNS shell script in Proxmox-k3s repository: https://github.com/benspilker/proxmox-k3s/blob/main/5-6_Install-Nextcloud/5A-domainname-dns.sh

Run that script again but add the number 2 to the end of your domain.
---
Note: YAML files 1-3 can be deployed all at once. Their dependencies are handled by the file names: 1, 2, 3.

NOTE THIS CREATES A SEPARATE NAMESPACE: NEXTCLOUD2

1. **Deploy Nextcloud with Persistent Storage**: Deploy Nextcloud using Helm in its own Kubernetes namespace with persistent storage.
2. **Create Self-Signed Certificate**: Generate a self-signed certificate for HTTPS access to Nextcloud.
3. **Define Ingress**: Create and apply an Ingress resource to expose Nextcloud via HTTPS.

---

This completes the setup for Nextcloud with persistent storage and a fully functioning K3s cluster. You should now be able to acccess it internally using https://nextcloud.yourexampledomain2.com