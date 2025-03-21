# Project 1: Deploy a Static Website with Azure Storage and CDN

This project demonstrates how to deploy a static website using Azure Storage and Azure Content Delivery Network (CDN). The steps below guide you through the entire process using Azure CLI commands. This project is ideal for showcasing your cloud infrastructure skills, particularly in Azure.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Step 1: Create a Resource Group](#step-1-create-a-resource-group)
3. [Step 2: Create an Azure Storage Account](#step-2-create-an-azure-storage-account)
4. [Step 3: Enable Static Website Hosting](#step-3-enable-static-website-hosting)
5. [Step 4: Upload Website Files to Azure Storage](#step-4-upload-website-files-to-azure-storage)
6. [Step 5: Create an Azure CDN Profile](#step-5-create-an-azure-cdn-profile)
7. [Step 6: Configure CDN Endpoint for the Static Website](#step-6-configure-cdn-endpoint-for-the-static-website)
8. [Step 7: Test the Website](#step-7-test-the-website)
9. [Step 8: Clean Up Resources](#step-8-clean-up-resources)
10. [Conclusion](#conclusion)

---

## Prerequisites

Before starting, ensure you have the following:
- An active Azure subscription.
- Azure CLI installed on your local machine. If not, follow the [official installation guide](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli).
- A static website (HTML, CSS, JS files) ready to deploy.

---

## Step 1: Create a Resource Group

A resource group is a container for Azure resources. Create one using the following command:

```bash
az group create --name MyResourceGroup --location eastus
```

**Screenshot Placeholder**:  
![Resource Group Creation](path/to/screenshot1.png)  
*Replace `path/to/screenshot1.png` with the actual screenshot of the resource group creation.*

---

## Step 2: Create an Azure Storage Account

Create a storage account to host your static website:

```bash
az storage account create \
  --name mystorageaccount \
  --resource-group MyResourceGroup \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
```

**Screenshot Placeholder**:  
![Storage Account Creation](path/to/screenshot2.png)  
*Replace `path/to/screenshot2.png` with the actual screenshot of the storage account creation.*

---

## Step 3: Enable Static Website Hosting

Enable static website hosting on the storage account:

```bash
az storage blob service-properties update \
  --account-name mystorageaccount \
  --static-website \
  --index-document index.html \
  --404-document 404.html
```

**Screenshot Placeholder**:  
![Enable Static Website](path/to/screenshot3.png)  
*Replace `path/to/screenshot3.png` with the actual screenshot of enabling static website hosting.*

---

## Step 4: Upload Website Files to Azure Storage

Upload your website files to the `$web` container:

```bash
az storage blob upload-batch \
  --account-name mystorageaccount \
  --source ./website-files \
  --destination \$web
```

**Screenshot Placeholder**:  
![Upload Website Files](path/to/screenshot4.png)  
*Replace `path/to/screenshot4.png` with the actual screenshot of the file upload process.*

---

## Step 5: Create an Azure CDN Profile

Create a CDN profile to optimize content delivery:

```bash
az cdn profile create \
  --name MyCDNProfile \
  --resource-group MyResourceGroup \
  --sku Standard_Microsoft
```

**Screenshot Placeholder**:  
![Create CDN Profile](path/to/screenshot5.png)  
*Replace `path/to/screenshot5.png` with the actual screenshot of the CDN profile creation.*

---

## Step 6: Configure CDN Endpoint for the Static Website

Create a CDN endpoint to serve your static website:

```bash
az cdn endpoint create \
  --name MyCDNEndpoint \
  --profile-name MyCDNProfile \
  --resource-group MyResourceGroup \
  --origin mystorageaccount.z6.web.core.windows.net \
  --origin-host-header mystorageaccount.z6.web.core.windows.net
```

**Screenshot Placeholder**:  
![Create CDN Endpoint](path/to/screenshot6.png)  
*Replace `path/to/screenshot6.png` with the actual screenshot of the CDN endpoint creation.*

---

## Step 7: Test the Website

Once the CDN endpoint is provisioned, test your website by navigating to the CDN URL:

```bash
echo "Website URL: http://MyCDNEndpoint.azureedge.net"
```

**Screenshot Placeholder**:  
![Test Website](path/to/screenshot7.png)  
*Replace `path/to/screenshot7.png` with the actual screenshot of the website in the browser.*

---

## Step 8: Clean Up Resources

To avoid unnecessary charges, delete the resource group and all associated resources:

```bash
az group delete --name MyResourceGroup --yes
```

**Screenshot Placeholder**:  
![Clean Up Resources](path/to/screenshot8.png)  
*Replace `path/to/screenshot8.png` with the actual screenshot of the resource group deletion.*

---

## Conclusion

This project demonstrates how to deploy a static website using Azure Storage and CDN. By following these steps, you can efficiently host and deliver static content globally. This project showcases your ability to work with Azure CLI, Azure Storage, and Azure CDN, making it a valuable addition to your portfolio.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

Feel free to replace the placeholder text and screenshots with your actual content. This README is designed to be professional and easy to follow, making it ideal for showcasing your skills to potential employers.
