
# Azure VM and Storage Account Connection Setup

## Overview
This document outlines the steps to create an Azure Virtual Machine (VM) and connect it to an Azure Storage Account using managed identities for secure access.

## Prerequisites
- An Azure account with the necessary permissions to create resources.

## Steps

1. **Create a Resource Group**
   - Go to the Azure portal.
   - Create a new resource group.

2. **Create a Storage Account**
   - In the Azure portal, navigate to "Create a resource" > "Storage" > "Storage account".
   - Choose the resource group created in the previous step.
   - Configure the required settings and create the storage account.

3. **Create a Container**
   - Inside your storage account, go to the "Containers" section.
   - Create a new container and upload your `index.html` file.

4. **Create a Virtual Machine**
   - Go to "Create a resource" > "Compute" > "Virtual Machine".
   - Choose the same resource group and configure the VM settings.

5. **Enable Managed Identity**
   - After the VM is created, go to its settings.
   - Under "Identity", enable the system-assigned managed identity.

6. **Assign Role to VM**
   - Navigate to your Storage Account.
   - Go to "Access Control (IAM)" > "Role assignments".
   - Click on "+ Add role assignment".
     - Role: Select "Storage Blob Data Owner".
     - Assign access to: "Managed Identity".
     - Select your VM and click "Save".

7. **Connect to VM**
   - SSH into your VM using the terminal.
   - Run the following commands:
     ```bash
     sudo apt update
     sudo apt install jq
     ```

8. **Obtain Access Token**
   - Run the following command to get an access token:
     ```bash
     access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true | jq -r '.access_token')
     ```

9. **Access Uploaded File**
   - Use the following command to access the uploaded file:
     ```bash
     curl "https://<storage_account_name>.blob.core.windows.net/<container_name>/<blob_name>" -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer $access_token"
     ```
   - Replace `<storage_account_name>`, `<container_name>`, and `<blob_name>` with your actual values.



### Breakdown of Commands

1. **Access Token Command**
   ```bash
   access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true | jq -r '.access_token')
   ```
   - **Purpose:** This command retrieves an access token for Azure Storage using the VM's managed identity.
   - **Components:**
     - `curl 'http://169.254.169.254/metadata/identity/oauth2/token?...'`: This sends an HTTP GET request to the Azure Instance Metadata Service (IMDS), which is available at that IP address. It provides information about the VM's identity.
     - `api-version=2018-02-01`: Specifies the version of the API to use.
     - `resource=https%3A%2F%2Fstorage.azure.com%2F`: Specifies the resource you want access to—in this case, Azure Storage.
     - `-H Metadata:true`: This header is required to indicate that the request is coming from a VM.
     - `| jq -r '.access_token'`: The output of the curl command is piped to `jq`, which extracts the access token from the JSON response.

2. **Accessing the Blob Command**
   ```bash
   curl "https://$storage_account_name.blob.core.windows.net/$container_name/$blob_name" -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer $access_token"
   ```
   - **Purpose:** This command accesses a blob (file) in your Azure Storage container using the access token obtained in the previous command.
   - **Components:**
     - `curl "https://$storage_account_name.blob.core.windows.net/$container_name/$blob_name"`: Constructs the URL to access the specific blob.
     - `$storage_account_name`, `$container_name`, `$blob_name`: These variables should be replaced with the actual names of your storage account, container, and blob.
     - `-H "x-ms-version: 2017-11-09"`: Specifies the version of the Azure Storage service to use.
     - `-H "Authorization: Bearer $access_token"`: This header includes the Bearer token for authentication.

### Checking the Uploaded File

To check if the file you uploaded is accessible, you can run the access blob command (the second command above) from your VM. If you get a successful response (HTTP 200), it means your file is accessible.


## Conclusion
You have successfully created an Azure VM and connected it to a Storage Account using managed identities. This setup allows secure access to Azure resources without needing to manage credentials manually.