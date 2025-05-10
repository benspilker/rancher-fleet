## Install Nextcloud Instance in a GitOps method using Rancher Fleet

This repository is an extension of the Proxmox-K3s repository.
https://github.com/benspilker/proxmox-k3s

The assumption is that you already have a working Kubernetes Cluster with Rancher, meaning steps 1-4 in the Proxmox-K3s repository have been setup in your environment.

Another Prerequisite is having a working Rancher Fleet Pipeline and Git installed on your workstation, See 0.A

---

0.A. **Prerequisite A, Fleet Pipeline** Assuming you already have a Rancher Fleet repository setup using Github for Continuous Delivery. See Jim's Garage: https://youtu.be/ulKimzGWtqc?si=uBBu3owusGF2BbDW

---
## Getting Started

To get a copy of this repository up and running on your local machine and push code to your own, follow these instructions:

1. Clone the repository (requires git installed on your machine first):
    ```bash
    git clone https://github.com/benspilker/rancher-fleet.git

2. Create a new folder with a blank readme on your local machine
    ```bash
    mkdir new-repo
    cd new-repo
    echo 'New Readme' >> readme.md

3. Initialize a New Git Repository from your new folder
    ```bash
    git init
    git add readme.md
    git commit -m "first commit"
    git branch -M main
    git remote add origin https://github.com/youruser/new-repo.git
    git push -u origin main

4. Copy the File(s) as needed from rancher-fleet repo into your new folder

5. Commit and push the File(s) as needed
    ```bash
    git add make-namespace.yaml
    git status
    git commit -m "added test yaml file"
    git push origin main

6. Manually Delete the test file, copy and add all files (After Prereq B, DNS is met)
      ```bash
    git add .
    git status
    git commit -m "adding all yaml files"
    git push origin main
---
0.B. **Prerequisite B, Setup DNS**: Setup of DNS to ensure the Nextcloud domain is correctly resolved to the soon to be Ingress IP, ie nextcloud.yourexampledomain2.com resolves to 192.168.100.203.
See DNS shell script in Proxmox-k3s repository: https://github.com/benspilker/proxmox-k3s/blob/main/5-6_Install-Nextcloud/5A-domainname-dns.sh

Run that script again but add the number 2 to the end of your domain.
---
Note: YAML files 1-7 can be deployed all at once. Their dependencies are handled by the file names: 1, 2, 3, etc.

NOTE THIS CREATES A SEPARATE NAMESPACE: NEXTCLOUD2

---
## GitOps Nextcloud Install with MySQL and Persistent Storage

1. **Create Nextcloud2 Namespace and Apply Permissions**: Create the namespace and create the RBAC permissions for our deployment to be able to create and use a Kubernetes Secret. This way we will have the ability to define a password for our MySQL instance later when it gets deployed.
2. **Generate Kubernetes Secret**: Run a batch job pod that generates the Kubernetes secret. The secret is 12 characters long, has capital and lowercase letters, has at least 1 number and 1 special character, then gets base64 encoded.
3. **Deploy MariaDB MySQL**: Deploys the MariaDB MySQL install as a blank database. This Mariadb installation has persistent storage and gets its root password from the Kubernetes secret created from the previous step.
4. **Customize MariaDB MySQL**: Runs a separate batch job pod that customizes the database setup in MariaDB by creating a new database called nextcloud with a user called nextcloud and also uses the same root password that was our Kubernetes secret, calling it mariadb-secret.
5.  **Nextcloud Deployment with MySQL and Persistent Storage**: Deploys nextcloud with persistent storage and connects it to our MariaDB instance nextcloud database. Runs a 'sidecar' container as a secondary container in the same nextcloud pod to check the configuration file and ensures through shell script that the config.php file has the correct content.
6. **Self-Signed Certificate Creation**: Creates and assigns a self-signed certificate for HTTPS access to Nextcloud.
7. **Define Ingress**: Create and applies an Ingress resource to expose Nextcloud deployment to our LAN via HTTPS, using our self-signed certificate.
8. **Wait for Configuration to Modify**: After the nextcloud instance is initialized, the config file may still have its default settings. The nextcloud pod may restart and then the config.php file goes back to default settings. 

For this reason, the shell script on the sidecar container has a 3 minute waiting period, then checks in a loop to modify the settings again. It may take up to 5 minutes of the pod being online for the configuration to correct. If we were to browse to our deployment ( ie https://nextcloud.yourexampledomain2.com ) before the config file gets edited, we will get a "trusted domain" error.

---
To check config file, from Admin Machine run:

kubectl get pods -n nexcloud2

POD_NAME=$(kubectl get pods -n nextcloud --no-headers | grep -v maria | awk '{print $1}' | head -n 1)

kubectl exec -it $POD_NAME -n nextcloud -- /bin/sh -c 'cat /var/www/html/config/config.php'


<Insert Screenshot of config file Here>

---
# If changing domainname, make sure to edit the value: yourexampledomain2.com on the following files:
5. (Nextcloud Deployment): LINE 143
6. (CERT): LINES 20 AND 22
7. (INGRESS): LINES 8 AND 20

This completes the setup for Nextcloud with persistent storage and a fully functioning K3s cluster. You should now be able to access it internally using https://nextcloud.yourexampledomain2.com