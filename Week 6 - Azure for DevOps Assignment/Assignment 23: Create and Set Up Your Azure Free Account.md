# Assignment 23: Create and Set Up Your Azure Free Account

## Objective

Set up a fully functional **Microsoft Azure Free Account** so you can complete all hands-on labs in this course. The Free Account includes **\$200 credit for 30 days**, **12 months of free services**, and **25+ always-free services** (e.g., Functions, App Service).

## What you will do

* Create (or sign in to) a Microsoft account
* Verify your identity (phone)
* Add a payment method (temporary \$1 authorization, refunded)
* Accept terms and finish sign-up
* Access the **Azure Portal** and confirm you can see key services

## Prerequisites

* A valid email (Gmail/Outlook/other)
* A phone number for verification (SMS/call)
* A debit/credit card (Visa/Mastercard/Amex) for verification
* Stable internet connection

## Estimated Time

15–25 minutes

---

## Instructions (Step-by-Step)

1. **Open the Free Account page**

   * Go to: [https://azure.microsoft.com/free](https://azure.microsoft.com/free)
   * Click **Start free**.

2. **Create/Sign in to your Microsoft account**

   * Use an existing email (Gmail works) or create a new Outlook address.
   * Complete email verification if prompted.

3. **Enter personal details**

   * Provide name, country/region, and contact number.
   * Complete phone verification via **SMS or call**.

4. **Payment verification**

   * Add a valid debit/credit card.
   * A **temporary \$1 authorization** may appear and will be reversed automatically.
   * **Important:** You won’t be charged unless you explicitly **upgrade** later.

5. **Accept terms**

   * Review and accept the Microsoft Agreement and Offer Terms.
   * Finish account creation.

6. **Access the Azure Portal**

   * Go to: [https://portal.azure.com](https://portal.azure.com)
   * On the home dashboard, locate **Resource groups**, **Virtual machines**, **Storage accounts**, **App Services**.
   * (Optional) Pin commonly used services to the dashboard.

7. **Confirm subscription status**

   * In the portal, open **Subscriptions** and verify **Subscription name** shows **Free Trial** (or equivalent free offer visible).
   * (Optional) Open **Cost Management + Billing** to see remaining credit.

8. **(Optional) Quick CLI sanity check**
   If you already use Azure CLI:

   ```bash
   az login
   az account show --query "{name:name, state:state, isDefault:isDefault}"
   ```

   * Ensure your subscription is **Enabled** and **isDefault: true**.

---

## Deliverables (What to Submit)

Upload **two items**:

1. **Screenshot 1 – Azure Portal Home**

   * Show the signed-in portal with tiles for **Resource groups**, **Virtual machines**, **Storage accounts**, **App Services**.
   * Mask any personal email/ID.

2. **Screenshot 2 – Subscription Overview**

   * Go to **Subscriptions** → select your subscription.
   * Capture the page showing **Subscription name** (Free Trial) and **State** (Enabled).
   * Mask subscription ID and personal info.


---

## Common Issues & Troubleshooting

* **Card rejected / \$1 hold fails**

  * Use a different card. Prepaid cards often fail. Ensure online transactions are enabled.

* **Phone verification not working**

  * Switch to the alternate method (SMS ↔ call). Try again after a few minutes.

* **Portal shows no subscription**

  * Sign out/in. Check **Directory + subscription** filter (top-right). Ensure you’re in the correct **Tenant/Directory**.

* **Accidentally upgraded to Pay-As-You-Go**

  * You can continue, but monitor costs. For this course, **do not upgrade** unless you explicitly choose to.


---

## Important Notes

* **Privacy:** Mask personal data (email, subscription ID, card digits) before uploading screenshots.
* **Costs:** Free credits expire after **30 days**; always-free services remain available with limits. You are only billed if you **upgrade** or exceed free limits.
* **Clean-up:** In future labs, delete unused **Resource groups** to avoid charges.
