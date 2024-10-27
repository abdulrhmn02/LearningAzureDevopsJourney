
# Jenkins Deployment on Azure VM

This guide provides step-by-step instructions for deploying Jenkins on an Azure Virtual Machine (VM).

## Prerequisites

- An Azure account
- Basic knowledge of command-line interface

## Step 1: Create an Azure Virtual Machine

1. **Log in to the Azure Portal**.
2. **Create a new VM**:
   - Click on "Create a resource" and select "Virtual Machine".
   - Choose your subscription and resource group.
   - Enter a name for your VM.
   - Select the region, availability options, and image (e.g., Ubuntu 20.04 LTS).
   - Choose a size based on your requirements (e.g., B1s for testing).
   - Configure authentication (username and SSH public key).
   - Review and create the VM.

3. **Connect to your VM using SSH**:
   - Open your terminal or command prompt.
   - Use the following command (replace `your_username` and `your_vm_ip_address`):
     ```bash
     ssh your_username@your_vm_ip_address
     ```

## Step 2: Install Java and Jenkins

1. **Update the package list**:
   ```bash
   sudo apt update
   ```

2. **Install Java**:
   ```bash
   sudo apt install openjdk-17-jre
   ```

3. **Verify Java Installation**:
   ```bash
   java -version
   ```

4. **Install Jenkins**:
   - Add the Jenkins repository key:
     ```bash
     curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
     ```
   - Add the Jenkins repository:
     ```bash
     echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
     ```
   - Update the package list again:
     ```bash
     sudo apt-get update
     ```
   - Install Jenkins:
     ```bash
     sudo apt-get install jenkins
     ```

## Step 3: Configure Network Settings

**Note:** By default, Jenkins will not be accessible externally due to inbound traffic restrictions.

1. Go to the **Networking** section of your VM.
2. Create an **inbound security rule**:
   - Destination port ranges: `8080`
   - Priority: `400`
   - Action: Allow
   - Protocol: TCP
   - Name: Allow-Jenkins-Access

## Step 4: Unlock Jenkins

1. **Get the initial admin password**:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

2. **Access Jenkins**:
   - Open your web browser and navigate to `http://your_vm_ip_address:8080`.
   - Enter the Administrator password from the previous step.

3. **Complete the setup**:
   - Click on **Install suggested plugins**.
   - Wait for the plugins to install.
   - Create the first admin user or skip this step (it's recommended to create an admin user for future use).

## Conclusion

Jenkins installation is successful. You can now start using Jenkins for your CI/CD processes!
