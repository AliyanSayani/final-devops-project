# 🚀 One-Click Jenkins Pipeline Deployment

## 🎯 Objective

This project implements a fully automated DevOps CI/CD pipeline using Jenkins, Docker, Terraform, and Ansible. It provisions a Virtual Machine (VM) on Azure, installs a web server, and deploys a static website — all from a single Jenkins pipeline.

---

## 🛠️ Technology Stack

| Tool        | Purpose                                |
|-------------|-----------------------------------------|
| Docker      | Containerize Jenkins for automation     |
| Jenkins     | Automate the DevOps workflow            |
| Terraform   | Provision Azure infrastructure          |
| Ansible     | Configure server and deploy web app     |
| Azure       | Host the virtual machine (Ubuntu)       |
| Git         | Version control and source code hosting |

---

## 📁 Project Structure

project/
├── terraform/
│ ├── main.tf # Terraform infrastructure code
│ └── variables.tf # Terraform variables
├── ansible/
│ └── install_web.yml # Ansible playbook for Apache setup
├── app/
│ └── index.html # Static website content
├── Jenkinsfile # Jenkins pipeline script
└── README.md # Project documentation


---

## ⚙️ Jenkins Setup (in Docker)

Run the following command to launch Jenkins in Docker with all required tools mounted:

```bash
docker run -d \
--name jenkins \
-p 8080:8080 -p 50000:50000 \
-v jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which terraform):/usr/local/bin/terraform \
-v $(which ansible-playbook):/usr/local/bin/ansible-playbook \
-v $(pwd):/workspace \
jenkins/jenkins:lts

🔌 Jenkins Post-Install Configuration
Install required plugins:

Git

Pipeline

SSH Agent

Add SSH Credentials:

Go to Manage Jenkins → Credentials → Global → Add Credentials

Choose SSH Username with private key

Use azureuser (or whatever user you use in Terraform) and paste your private key

Set ID as azure-key (or match what's used in Jenkinsfile)

☁️ Terraform: Azure Infrastructure Provisioning
Terraform provisions the following resources on Azure:

Resource Group

Virtual Network and Subnet

Public IP Address

Network Interface

Ubuntu Virtual Machine with SSH access

Outputs the public IP of the VM to be used by Ansible.

Example Terraform Commands (automated by Jenkins):

terraform init
terraform apply -auto-approve
🔧 Ansible: Configuration & Deployment
Ansible is used to:

Install Apache2 on the Azure VM

Deploy the static website (index.html) to /var/www/html

Dynamic Inventory
The public IP is extracted from Terraform output and used directly for the host.

Example Ansible Command (automated by Jenkins):
ansible-playbook -i '<public_ip>,' ansible/install_web.yml \
  --private-key ~/.ssh/id_rsa \
  -u azureuser
📜 Jenkins Pipeline (Jenkinsfile)
The pipeline includes the following stages:

Checkout – Clones the repository

Terraform Init & Apply – Provisions Azure VM

Ansible Deploy – Installs web server & deploys app

Verify – Uses curl to check deployment success

If everything runs correctly, you should be able to access the static site using the public IP.
✅ Output Verification
Visit the deployed website:

cpp
Copy
Edit
http://<public_ip>

 Cleanup Resources
To destroy the infrastructure manually (or add as a Jenkins stage):

bash
Copy
Edit
terraform destroy -auto-approve
