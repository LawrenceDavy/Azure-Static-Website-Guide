# Deploy a Static Website with Azure Storage and CDN

[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Azure DevOps](https://img.shields.io/azure/build/your-organization/your-project/your-pipeline-name?branch=main)](YOUR_AZURE_DEVOPS_BUILD_LINK)

This guide walks you through the process of deploying a static website using Azure Blob Storage and Azure Content Delivery Network (CDN). This setup offers a cost-effective and scalable solution for hosting static content like HTML, CSS, JavaScript, and image files.

## Table of Contents

1.  [Prerequisites](#prerequisites)
2.  [Step 1: Create an Azure Storage Account](#step-1-create-an-azure-storage-account)
3.  [Step 2: Configure Static Website Hosting](#step-2-configure-static-website-hosting)
4.  [Step 3: Upload Website Files to Blob Storage](#step-3-upload-website-files-to-blob-storage)
5.  [Step 4: Access Your Static Website](#step-4-access-your-static-website)
6.  [Step 5: Create an Azure CDN Profile and Endpoint](#step-5-create-an-azure-cdn-profile-and-endpoint)
7.  [Step 6: Configure CDN Endpoint to Use Custom Domain (Optional)](#step-6-configure-cdn-endpoint-to-use-custom-domain-optional)
8.  [Step 7: Verify CDN Access](#step-7-verify-cdn-access)
9.  [Step 8: Clean Up Resources (Optional)](#step-8-clean-up-resources-optional)
10. [Troubleshooting](#troubleshooting)
11. [Contributing](#contributing)
12. [License](#license)

## Prerequisites

Before you begin, ensure you have the following:

* An active Azure subscription. If you don't have one, you can [create a free Azure account](https://azure.microsoft.com/free/).
* A static website consisting of HTML, CSS, JavaScript, and other static assets.
* (Optional) A custom domain name if you want to access your website through a personalized URL.

## Step 1: Create an Azure Storage Account

Azure Storage accounts provide a unique namespace for your Azure Storage data.

1.  Navigate to the [Azure portal](https://portal.azure.com/).
2.  Click on **"+ Create a resource"** in the left-hand menu.
3.  Search for **"Storage account"** and select it.
4.  Click **"Create"**.

    ![Screenshot Placeholder]

5.  On the **"Basics"** tab:
    * **Subscription:** Choose your Azure subscription.
    * **Resource group:** Create a new resource group or select an existing one to organize your resources.
    * **Storage account name:** Enter a globally unique name for your storage account (e.g., `youruniquewebsitename`).
    * **Region:** Select the Azure region closest to your target audience.
    * **Performance:** Choose **"Standard"** for most static website scenarios.
    * **Redundancy:** Select an appropriate redundancy option based on your requirements (e.g., **"Locally-redundant storage (LRS)"** for basic scenarios).

    ![Screenshot Placeholder]

6.  Click **"Review + create"**.
7.  Review the configuration and click **"Create"**.
8.  Once the deployment is complete, click **"Go to resource"**.

    ![Screenshot Placeholder]

## Step 2: Configure Static Website Hosting

Enable the static website hosting feature for your storage account.

1.  In your storage account, navigate to the **"Data management"** section in the left-hand menu.
2.  Click on **"Static website"**.

    ![Screenshot Placeholder]

3.  Toggle the **"Static website"** setting to **"Enabled"**.

    ![Screenshot Placeholder]

4.  In the **"Index document name"** field, enter the name of your website's main page (usually `index.html`).
5.  (Optional) In the **"Error document path"** field, enter the path to a custom error page (e.g., `error.html`). If you don't have one, you can leave this blank.

    ![Screenshot Placeholder]

6.  Click **"Save"**.
7.  Note the **"Primary endpoint"** URL displayed. This is the initial URL for your static website.

    ![Screenshot Placeholder]

## Step 3: Upload Website Files to Blob Storage

Upload your website's files to the `$web` container in your storage account.

1.  In your storage account, navigate to the **"Data storage"** section in the left-hand menu.
2.  Click on **"Containers"**.

    ![Screenshot Placeholder]

3.  You should see a container named **"$web"**. Click on it.

    ![Screenshot Placeholder]

4.  Click the **"Upload"** button.

    ![Screenshot Placeholder]

5.  Select all the files and folders of your static website. You can either drag and drop them or browse for files.
6.  Click **"Upload"**.

    ![Screenshot Placeholder]

## Step 4: Access Your Static Website

You can now access your static website using the primary endpoint URL noted in Step 2.

1.  Open a web browser.
2.  Enter the **"Primary endpoint"** URL.
3.  Your static website should now be visible.

## Step 5: Create an Azure CDN Profile and Endpoint

Azure CDN helps deliver your website content faster to users worldwide through a global network of servers.

1.  Navigate back to the [Azure portal](https://portal.azure.com/).
2.  Click on **"+ Create a resource"** in the left-hand menu.
3.  Search for **"CDN profile"** and select it.
4.  Click **"Create"**.

    ![Screenshot Placeholder]

5.  On the **"Basics"** tab:
    * **Subscription:** Choose your Azure subscription.
    * **Resource group:** Select the same resource group as your storage account or a different one.
    * **Profile name:** Enter a name for your CDN profile (e.g., `yourwebsitecdnprofile`).
    * **Pricing tier:** Choose a pricing tier that suits your needs. **"Standard Microsoft"** is a good starting point.

    ![Screenshot Placeholder]

6.  Click **"Review + create"**.
7.  Review the configuration and click **"Create"**.
8.  Once the deployment is complete, click **"Go to resource"**.

    ![Screenshot Placeholder]

9.  In your CDN profile, click **"+ Endpoint"**.

    ![Screenshot Placeholder]

10. On the **"Add endpoint"** page:
    * **Endpoint name:** Enter a name for your CDN endpoint (e.g., `yourwebsitecdn`).
    * **Origin type:** Select **"Storage"**.
    * **Origin hostname:** Select the storage account you created earlier from the dropdown list. The **"Origin path"** will automatically populate with `$web`.
    * **Protocol:** Choose **"HTTP and HTTPS"**.
    * **Port:** Leave the default values (80 for HTTP, 443 for HTTPS).

    ![Screenshot Placeholder]

11. Click **"Add"**.
12. It may take a few minutes for the CDN endpoint to be provisioned. Once it's ready, note the **"Endpoint hostname"**. This is the URL you will use to access your website through the CDN.

    ![Screenshot Placeholder]

## Step 6: Configure CDN Endpoint to Use Custom Domain (Optional)

If you have a custom domain name, you can associate it with your CDN endpoint.

1.  In your CDN endpoint, navigate to **"Custom domains"** in the left-hand menu.

    ![Screenshot Placeholder]

2.  Click **"+ Add custom domain"**.

    ![Screenshot Placeholder]

3.  In the **"Hostname"** field, enter your custom domain name (e.g., `www.yourdomain.com`).
4.  Azure will prompt you to verify ownership of your domain by adding a CNAME record to your DNS settings. Follow the instructions provided in the Azure portal to create this CNAME record with your domain registrar. The CNAME should point from your custom domain to the CDN endpoint hostname (e.g., `yourwebsitecdn.azureedge.net`).

    ![Screenshot Placeholder]

5.  Once the CNAME record is configured, click **"Add"** in the Azure portal.
6.  It may take some time for the DNS changes to propagate. Once the custom domain is successfully added, you can access your website using your custom domain name.

    ![Screenshot Placeholder]

## Step 7: Verify CDN Access

Access your website using the CDN endpoint URL (or your custom domain if configured) to ensure the content is being served through the CDN.

1.  Open a web browser.
2.  Enter the **"Endpoint hostname"** URL of your CDN or your custom domain.
3.  Verify that your website loads correctly. You can use browser developer tools to check the `Server` header in the HTTP response. It should indicate that the content is being served by the Azure CDN.

## Step 8: Clean Up Resources (Optional)

If you want to remove the resources created in this guide, you can delete the resource group.

1.  Navigate to the resource group you created in the [Azure portal](https://portal.azure.com/).
2.  Click **"Delete resource group"**.

    ![Screenshot Placeholder]

3.  Confirm the deletion by typing the resource group name and clicking **"Delete"**.

## Troubleshooting

* **Website not loading:** Ensure the `$web` container is enabled for static website hosting and that your `index.html` file is uploaded correctly. Check the primary endpoint URL of the storage account.
* **CDN not reflecting changes:** It might take some time for changes to propagate through the CDN. You can try purging the CDN endpoint cache in the Azure portal.
* **Custom domain not working:** Double-check the CNAME record configuration with your domain registrar and ensure it points to the correct CDN endpoint hostname.

