---
title: "PSA Web ‐ Creating a New Client Instance"
layout: default
---

# PSA Web ‐ Creating a New Client Instance

This document outlines the multi-step process for setting up a new PSA instance in **Dev**, **QA**, or **Prod**. It includes configuring the Rev.io Billing instance, creating or using service principals, requesting a new PSA database, and updating Auth Service environment settings.

---

## Overview

1. **Rev.io Billing Instance**  
   - Identify or request a new Billing instance.  
   - Enable event hub system events.  
2. **PSA Database**  
   - Create a new service principal.  
   - Request a new PSA database.  
3. **Add Auth Service Environment Settings**  
   - Tie the Billing instance, PSA database, and PSA URL together in Auth Service.  
4. **Final Step**  
   - Verify the login flow.  
5. **(Optional) Migrate Existing Customer & Contact Data**  
   - Sync data into the PSA API database.

---

## 1. Rev.io Billing Instance

### 1.1 Client Code

Every PSA instance depends on a **Rev.io billing instance** with a unique *Client Code*.  
- If you’re using an **existing** Billing instance, find the CLEC (Client) Code in the instance URL (e.g., `billinglab.rev.io` → `BILLINGLAB`).  
- If you need a **new** Billing instance, submit a CA ticket to request one. Once created, note the assigned Client Code.  
- **Important**: As of December 2024, **Reseller instances are NOT supported**.

### 1.2 Enable Billing Events

1. Log in to the Billing instance UI.  
2. Navigate to **Admin** → **Settings** → **Add New**.  
3. Add the setting **Enable Event Hub System Events** and set it to `True`.  
4. **Restart the app service** assigned to the billing instance so the setting takes effect.

---

## 2. PSA Database

### 2.1 Create a New Service Principal

We use a dedicated **service principal** for PSA to connect to its database. To create one:

1. Use the automated **Service Principal Creation Tool** (via GitHub Actions):  
   [az-sp-automation/actions/create-service-principal.yml](https://github.com/Rev-io/az-sp-automation/actions/workflows/create-service-principal.yml)
2. Click **Run workflow**.  
3. Ensure **Use workflow from Branch: main** is selected.  
4. Fill in the name of the client using this convention: `psa-<clientname>` (e.g., `psa-billinglab`).  
5. Select the same environment (Dev, QA, Prod) in which the PSA database will be created.  
6. Click **Run workflow**.  

**Note**:  
- In **Prod**, a CA member must manually review and approve the service principal creation.  
- The resulting app registration will be named `<environment>-psa-<client>-sp` (e.g., `prod-psa-billinglab-sp`).  
- This name is required in the next step to request a new PSA database.

### 2.2 Request a New PSA Database

Submit a CA ticket to provision a **new database** for PSA. You can open a database ticket here:  
[Rev.io Cloud Database Requests](https://revio.sharepoint.com/sites/infra)

**Example Ticket**:  
[Azure DevOps Work Item Example](https://dev.azure.com/rev-io-ado/Cloud%20Database/_workitems/edit/27652)

**In your ticket**:
1. Request the **PSA Elastic Pool** for the corresponding environment:  
   - **Dev**: `dev-reviopsa-database-ep-002`  
   - **QA**: `qa-reviopsa-database-ep-001`  
   - **Prod**: `prod-reviopsa-database-ep-001`  
2. Ask for the **new service principal** (created above) to be added with permissions:  
   - `SELECT`, `INSERT`, `UPDATE`, `DELETE`  
3. Ask for the **Kamino** service principal to be added as **DB Owner** (Kamino = pipeline process for PSA schema migrations).  

When the **next deployment** runs, it will build out the PSA schemas/tables in this new database.

---

## 3. Add Auth Service Environment Settings

The **Auth Service** ties everything together: the Billing instance, the PSA database, and the PSA URL. For now, records are added manually via Postman.

### 3.1 Create an Auth Service OAuth Access Token

1. Use the **Authentication Postman Collection**:  
   [Auth Postman Collection](https://rev-io.postman.co/workspace/revio-psa-api~15dc25a9-9d21-4a42-a899-029eb29b4eb4/folder/10904624-f1d02bec-ddf8-4661-b48f-7a97135cb6a6?action=share&source=copy-link&creator=33994544&ctx=documentation)  
2. Go to the **Token** folder, set the environment (Dev, QA, or Prod - PSA).  
3. Submit the `POST` for **OAuth access_token**.  
4. A valid **OAuth access token** is now stored in your Postman environment.

### 3.2 Create a Record with the Key of the PSA URL

1. Switch to the **Application Environments** folder in Postman.  
2. Select the **POST** for **Environment Settings** (OAuth).  
3. Send a request to:  
   `https://<auth-service-base-url>/api/v1/authn-service/token/login/revio-app-setting`  
4. Example body:

```json
{
  "key": "*example.psarev.io*",
  "value": {
    "psaUrl": "https://example.psarev.io/auth",
    "psaPostLogoutUrl": "https://example.psarev.io",
    "loginUrl": "https://app.rev.io/idp/login?client=EXAMPLE",
    "logoutUrl": "https://app.rev.io/idp/logout?key=%2Aexample.psarev.io%2A",
    "billProfileId": null,
    "clientCode": "EXAMPLE",
    "databaseSPClientIdSecretName": "prod-example-sp-clientid",
    "databaseSPSecretKeySecretName": "prod-example-sp-secret",
    "databaseName": "example-database-name"
  },
  "json": true
}
