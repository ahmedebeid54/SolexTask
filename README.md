# Northwind Products — SAP BTP Fiori Application

A List Report Fiori application built on SAP Business Technology Platform (BTP) that displays product inventory data from the Northwind OData service. Developed as part of the Solex Solution Experts SAP BTP Internship Technical Assessment.

## Architecture Overview

The application follows a standard SAP BTP HTML5 deployment architecture:

- **Destination Service**: Configured in the BTP Cockpit to point to `https://services.odata.org`, allowing the Fiori app to consume the Northwind OData service securely.
- **SAP Business Application Studio (BAS)**: Used to develop the Fiori application using the List Report floor plan template and SAP Fiori tools.
- **Cloud Foundry (HTML5 Repo Mode)**: The application is deployed as a static HTML5 app hosted in the SAP HTML5 Application Repository service (`html5-apps-repo`).
- **SAP Build Work Zone**: Serves as the central launchpad, integrating the deployed app with XSUAA-backed authentication.

## Setup Instructions

### Step 1 — BTP Trial & Universal ID
1. Registered for a free SAP BTP Trial at [account.hanatrial.ondemand.com](https://account.hanatrial.ondemand.com).
2. Verified the Cloud Foundry environment and created a space named `dev`.

### Step 2 — Service Subscriptions
1. Subscribed to **SAP Business Application Studio**.
2. Assigned the **Developer** role collection to the user to enable BAS access.
3. Subscribed to the **Destination Service** to manage OData connectivity.

### Step 3 — Configure Northwind Destination
1. Created a new Destination named `Northwind`.
2. URL: `https://services.odata.org` | Authentication: `NoAuthentication`.
3. Added properties: `WebIDEEnabled: true` and `WebIDEUsage: odata_gen`.
4. Verified connection status: **200 OK**.

### Step 4 — Build the Fiori App in BAS
1. Used the Fiori Application Generator with the **List Report Page** floor plan.
2. Connected to OData Service: `/V2/Northwind/Northwind.svc/`.
3. Main entity: **Products**.
4. Configured columns: `ProductID`, `ProductName`, `UnitPrice`, and `UnitsInStock`.

---

## Bonus Tasks Completed

| ID | Bonus Task Description | Status | Result / Observation |
|:---|:---|:---|:---|
| **B1** | **Deploy to Cloud Foundry** | ✅ | Deployed using `mbt build` and `cf deploy`. Verified via `cf html5-list`. |
| **B2** | **Test & Validate via Postman** | ✅ | Validated live endpoint with `$top`, `$filter`, `$select`, and `$orderby`. |
| **B3** | **XSUAA Authentication** | ✅ | Configured app protection; confirmed login prompt on deployed URL. |
| **B4** | **Fiori Object Page** | ⚠️ | Attempted; navigation logic established in BAS Page Map. |
| **B5** | **OData Write (POST)** | ✅ | Demonstrated successful POST request against a writable OData service. |
| **B6** | **SAP Build Work Zone** | ✅ | App successfully registered and visible on Launchpad site. |

---

## Challenges Faced & Solutions

### 1. Work Zone Subscription & Trust Configuration
**Challenge:** Manual subscription to SAP Build Work Zone via the Service Marketplace failed due to complex requirements for Identity Authentication (IAS) trust configuration, which is not easily accessible in Trial accounts.
**Solution:** Utilized the **SAP Build Apps Booster** (which includes Work Zone setup). This automated the entire subscription process, service instance creation, and established the necessary trust relationships automatically, allowing for a functional Launchpad site.

### 2. Deployment Verification (`cf apps` vs `cf html5-list`)
**Challenge:** After successful deployment, `cf apps` returned "No apps found," appearing as a failure.
**Solution:** Investigation confirmed that in `html5-repo` mode, the app is hosted by the managed repository, not as a standalone CF process. Running `cf html5-list` confirmed the app was active.

### 3. App Tile Visibility in Work Zone
**Challenge:** The app was registered in Content Manager but did not appear on the site.
**Solution:** This was identified as a lack of visualization metadata in the federated content. I manually created a **New App** entry in the Content Manager, defining the **Semantic Object** (`NorthwindProducts`), **Action** (`display`), and **Static Tile** visualization.

### 4. Postman Live Endpoint Testing
**Challenge:** Accessing the live BTP URL via Postman returned an HTML login page.
**Solution:** Configured **Basic Authentication** in Postman and set the `Accept: application/json` header to correctly receive OData results from the protected AppRouter.

### 5. Trial Quota Limits
**Challenge:** Encountered disk quota errors during redeployment.
**Solution:** Utilized the **Managed AppRouter** within Work Zone to serve the application, reducing the memory footprint by avoiding a dedicated AppRouter container.

---

## Postman API Validation (B2)
The following OData queries were demonstrated on the live deployed endpoint:
- **$top**: `.../Products?$top=5`
- **$select**: `.../Products?$select=ProductName,UnitPrice`
- **$filter**: `.../Products?$filter=UnitPrice gt 20`
- **$orderby**: `.../Products?$orderby=UnitPrice desc`

**Note to Reviewer:** All screenshots and the Postman collection export are available in the `/docs` folder.
