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
- **Command:** az group create --name MyResourceGroup --location eastus
    - **Purpose:** A resource group is a logical container that organizes Azure resources, making management and cleanup easier.
- **Detailed Explanation:**
    - Open a terminal (e.g., Azure Cloud Shell, local PowerShell, or CLI after installing Azure CLI from [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)).
    - Log in with az login (a browser window will prompt authentication).
    - If you have multiple subscriptions, set the correct one with az account set --subscription <subscription-id> (find it via az account list --query "[].id" --output tsv).
    - -name MyResourceGroup defines a unique name for this project; adjust if you prefer (e.g., StaticSiteRG).
    - -location eastus selects East US for proximity or preference; use az account list-locations for other options.
    - This step introduces resource organization, a foundational governance concept.

```bash
az group create --name MyResourceGroup --location eastus
```

![Image](https://github.com/user-attachments/assets/f82bba77-a304-48a8-8280-034abf425656)

---

## Step 2: Create an Azure Storage Account

Create a storage account to host your static website:
- **Purpose:** Storage accounts provide scalable storage for blobs, files, and static websites; here, it hosts your site.
- **Command:** az storage account create --name mystorageaccount --resource-group MyResourceGroup --location eastus --sku Standard_LRS
- **Detailed Explanation:**
    - -name mystorageaccount must be globally unique (3-24 lowercase letters/numbers); try mystorage<your-initials><number> (e.g., mystoragejd123) if it fails.
    - -resource-group MyResourceGroup ties it to your group.
    - -location eastus keeps it co-located with the resource group.
    - -sku Standard_LRS uses Locally Redundant Storage (three copies in one data center), balancing cost and reliability for learning.
    - After creation, verify with az storage account list --resource-group MyResourceGroup --query "[].name" --output tsv.
    - This is a core Azure service you’ll encounter in AZ-900.

```bash
az storage account create \
  --name mystorageaccount \
  --resource-group MyResourceGroup \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2
```

![Image](https://github.com/user-attachments/assets/7fec084b-3970-4357-9310-c2de9f800d50)

---

## Step 3: Enable Static Website Hosting

Enable static website hosting on the storage account:
- **Purpose:** Configures the storage account to serve static content, turning it into a web host without server management.
- **Command:** az storage blob service-properties update --account-name mystorageaccount --static-website --index-document index.html
- **Detailed Explanation:**
    - -account-name mystorageaccount targets your specific account (replace with your unique name).
    - -static-website activates the feature, creating a $web container automatically.
    - -index-document index.html sets the default page users see (e.g., visiting the URL loads index.html).
    - This leverages Blob Storage’s PaaS capabilities, requiring no VM setup.
    - Check it worked with az storage account show --name mystorageaccount --query "staticWebsite.enabled" --output tsv (should return true).

```bash
az storage blob service-properties update \
  --account-name mystorageaccount \
  --static-website \
  --index-document index.html \
  --404-document 404.html
```

![Image](https://github.com/user-attachments/assets/867d0df2-b1fc-4a8f-885b-791ff7f2b232)

---

## Step 4: Upload Website Files to Azure Storage

Upload your website files to the `$web` container:
- **Purpose:** Adds your static content to make the site live.
- **Commands:**
    - Create locally: mkdir website && echo "<h1>My Azure Static Site</h1>" > website/index.html
    - Upload: az storage blob upload-batch --account-name mystorageaccount --source ./website --destination \$web
- **Detailed Explanation:**
    - On your local machine, mkdir website creates a folder, and the echo command writes a simple HTML file (edit with a text editor like VS Code for more content, e.g., <p>AZ-900 Project</p>).
    - Navigate to the parent directory (cd to where website is), then run the upload command.
    - -source ./website points to your folder; --destination \$web targets the static website container (use $web verbatim).
    - This uploads all files in the folder; verify with az storage blob list --account-name mystorageaccount --container-name \$web --query "[].name".
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
- **Purpose:** Retrieves the public endpoint to test your site.
- **Command:** az storage account show --name mystorageaccount --resource-group MyResourceGroup --query "primaryEndpoints.web" --output tsv
- **Detailed Explanation:**
    - -query "primaryEndpoints.web" extracts the web-specific URL (e.g., https://mystorageaccount.z20.web.core.windows.net/).
    - -output tsv gives a clean string without quotes.
    - Paste this URL into a browser; you should see "My Azure Static Site" if Steps 3-4 succeeded.
    - If it fails, double-check the $web container has index.html (Step 4).


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
- **Purpose:** Uses Azure CDN to cache your site globally, improving speed and reliability.
- **Commands:**
    - az cdn profile create --name MyCDNProfile --resource-group MyResourceGroup --sku Standard_Microsoft
    - az cdn endpoint create --name MyCDNEndpoint --profile-name MyCDNProfile --resource-group MyResourceGroup --origin mystorageaccount.z20.web.core.windows.net --origin-host-header mystorageaccount.z20.web.core.windows.net
- **Detailed Explanation:**
    - First command:
        - -name MyCDNProfile names the CDN profile (a container for endpoints).
        - -sku Standard_Microsoft picks a basic tier (others like Verizon cost more).
    - Second command:
        - -name MyCDNEndpoint must be unique (e.g., MyCDNEndpoint123 if taken).
        - -origin is your storage account’s web endpoint (from Step 5, without https://).
        - -origin-host-header ensures proper routing; match it to the origin.
        - After creation, get the CDN URL with az cdn endpoint show --name MyCDNEndpoint --profile-name MyCDNProfile --resource-group MyResourceGroup --query "hostName" --output tsv (e.g., mycdnendpoint.azureedge.net).
        - Test the CDN URL in a browser; it may take 5-15 minutes to propagate initially.
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
- **Purpose:** Tags categorize resources for tracking and governance.
- **Command:** az resource tag --tags Environment=Dev Project=StaticWebsite --ids /subscriptions/<subscription-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount
- **Detailed Explanation:**
    - Find your <subscription-id> with az account show --query "id" --output tsv.
    - -tags Environment=Dev Project=StaticWebsite adds metadata; customize keys/values (e.g., Owner=YourName).
    - -ids specifies the storage account’s resource ID (replace mystorageaccount with your name).
    - Verify tags with az resource show --ids <same-id> --query "tags" --output json.
```bash
echo "Website URL: http://MyCDNEndpoint.azureedge.net"
```

**Screenshot Placeholder**:  
![Test Website](path/to/screenshot7.png)  
*Replace `path/to/screenshot7.png` with the actual screenshot of the website in the browser.*

---

## Step 8: Clean Up Resources

To avoid unnecessary charges, delete the resource group and all associated resources:
- **Purpose:** Monitors spending, reinforcing cost management skills.
- **Steps:**
    - Log into the Azure portal (portal.azure.com).
    - Go to "Cost Management + Billing" > "Budgets."
    - Click "+ Add," name it StaticSiteBudget, set scope to MyResourceGroup, set a $5 monthly limit, and save.
- **Detailed Explanation:**
    - Use the free tier (200 USD credit for new accounts) to avoid costs; this step is manual for simplicity.
    - Optionally, add an alert at 80% via "Alerts" in the budget settings.
    - Check budget status later under "Budgets" to see usage.
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
