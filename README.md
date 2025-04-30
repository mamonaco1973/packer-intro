# Cross-Cloud Image Building with Packer  

In this series, we'll complete an end-to-end project using **HashiCorp Packer** to build and deploy custom VM images across **AWS**, **Azure**, and **Google Cloud Platform**.

We’ll build **two VM images** from scratch:

- An **Ubuntu 24.04** image that installs **Apache** and deploys classic **80's-style JavaScript/HTML games**.
- A **Windows Server 2022** image that applies the **latest Windows Updates** at build time and installs **Chrome** and **Firefox** for ready-to-go browser support.

We'll learn the following.

- Automate secure Linux and Windows image creation tailored for each cloud.
- Create a local `packer` account on the images and store the credentials as secrets.
- Handle **WinRM**, **Sysprep**, and firewall configuration for Windows.
- Deploy and test your images with **HTTP** access (Linux) and **RDP** access (Windows).

---

This is a **complete working solution** for building reusable cloud images — easy to adapt and extend for your own projects.

## Quick Links

1. [Packer in the Cloud](https://youtu.be/buLKqtuRzWc)
2. AWS Solution
   - [Packer in AWS](https://youtu.be/VXyYrJS6esk)
   - [GitHub Project](https://github.com/mamonaco1973/aws-packer/)
3. Azure Solution
   - [Packer in Azure](https://youtu.be/NMRtx4fbyNM)
   - [GitHub Project](https://github.com/mamonaco1973/azure-packer/)
4. GCP Solution
   - [Packer in GCP](https://youtu.be/4GTBJJRQi7Y)
   - [GitHub Project](https://github.com/mamonaco1973/gcp-packer/)

## AWS Image Build & Deployment

In the AWS solution, we use Packer to build both **Linux AMIs** and **Windows AMIs**.

- For **Linux**, we configure an Ubuntu-based image with Apache and deploy several retro HTML games.
- For **Windows**, we install Chrome and Firefox, apply the **latest Windows Updates**, and configure WinRM using a custom **user data script**.
- We use the built-in **EC2 Launch tool** to perform Sysprep on Windows images, ensuring a clean and reusable AMI every time.
- Both images are configured with **AWS Systems Manager (SSM)** support, allowing you to connect and debug directly from the AWS Console without needing SSH or RDP.
- The images are built inside a user-defined **VPC and subnet**, which is created beforehand.
- We test deployments by accessing the Linux instance over **HTTP (port 80)** and the Windows instance via **RDP** using a local `packer` account with a secure password.

![ami](./aws-packer.png)

## Azure Image Build & Deployment

In the Azure solution, we build and deploy custom **Ubuntu** and **Windows** images using Packer.

- For **Linux**, we create an Ubuntu image with Apache installed and deploy several 80’s-style HTML games.
- For **Windows**, we install Chrome and Firefox, apply the **latest Windows Updates**, and prepare the system using **Sysprep with PowerShell** commands.
- Azure doesn’t allow direct execution of PowerShell in `custom_data`, so we implement a workaround: we drop a PowerShell script as `CustomData.bin` and use a lightweight **VM extension** to execute it at boot.
- Networking is provisioned automatically by Packer using a temporary **resource group and VNet**, simplifying initial setup.
- We deploy an **Azure Bastion host** to securely interact with both Linux and Windows VMs from the Azure Portal — eliminating the need to expose public IPs or manage SSH/RDP keys manually.
- The Windows image supports **RDP** access using a local `packer` account, and the Linux image can be accessed via **HTTP** on port 80.

![azure](./azure-packer.png)

## GCP Image Build & Deployment

In the GCP solution, we use Packer to build and deploy custom **Linux** and **Windows** images to Compute Engine.

- For **Linux**, we create an Ubuntu-based image with Apache installed and deploy several retro-style HTML games.
- For **Windows**, we install Chrome and Firefox, apply the **latest Windows Updates**, and execute **Sysprep** using native PowerShell commands.
- GCP **automatically installs WinRM**, but additional setup is required — we use a **metadata startup script** to set the WinRM password and configure authentication.
- We define a **custom firewall rule** to allow WinRM traffic (TCP 5986) so that the Windows instance is accessible for remote management.
- Image builds run within a custom **VPC and subnet** that are pre-provisioned to support isolated, controlled access.
- The Windows image supports **RDP** access using a local `packer` account, and the Linux image is tested via **HTTP** on port 80.

![gcp](./gcp-packer.png)
