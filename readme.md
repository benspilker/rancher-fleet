## Install Nextcloud Instance in a GitOps method using Rancher Fleet

Yaml Files 1-3 can be pushed to the repository all at once.

0.A. **Prerequisite A, Fleet Pipeline** Assuming you already have a Rancher Fleet repository setup using Github for Continuous Delivery. See Jim's Garage: https://youtu.be/ulKimzGWtqc?si=uBBu3owusGF2BbDW

0.B. **Prerequisite B, Setup DNS**: Setup of DNS Server and ensure the Nextcloud domain is correctly resolved to the soon to be Ingress IP, ie nextcloud.yourexampledomain.com resolves to 192.168.100.203

1. **Deploy Nextcloud with Persistent Storage**: Deploy Nextcloud using Helm in its own Kubernetes namespace with persistent storage.
2. **Create Self-Signed Certificate**: Generate a self-signed certificate for HTTPS access to Nextcloud.
3. **Define Ingress**: Create and apply an Ingress resource to expose Nextcloud via HTTPS.

---

This completes the setup for Nextcloud with persistent storage and a fully functioning K3s cluster. You should now be able to acccess it using https://nextcloud.yourexampledomain.com