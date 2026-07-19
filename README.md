<p align="center">
  <img width="900" height="500" alt="image" src="https://github.com/user-attachments/assets/2c068a6c-d82b-4a43-ad32-d9fe559e1837" />
</p>

# Okta MFA and Adaptive Authentication

This lab covers configuring multi-factor authentication and adaptive authentication policies in Okta, including passwordless authentication with Okta Verify, network zone-based access rules, step-up authentication for sensitive applications, and authenticator enrollment policies.

---

## Prerequisites
This is the fifth lab of the [Okta IAM Lab Series](https://github.com/RyanKennon/Okta-Lab-Series/tree/main).
Complete all previous labs before starting this one.
The following should be in place before starting:
- Active Okta Integrator org with users, groups, and policies configured from previous labs
- Active Directory integration configured and synced from Lab 2
- RBAC group structure in place from Lab 3 (Finance, IT, Human Resources, Helpdesk, Cloud Engineer)
- SAML application configured from Lab 4
- Okta Verify installed on a mobile device

---

## Environments and Technologies Used
- Okta Identity Engine (Integrator Free Plan)
- Okta Verify (mobile authenticator app)
- Okta Authentication Policies (App Sign-In)
- Okta Global Session Policy
- Okta Network Zones
- Okta Authenticator Enrollment Policy
- Active Directory (Kennon Technologies Employees group)

---

## Table of Contents
1. [Enroll Okta Verify](#1-enroll-okta-verify)
2. [Passwordless Enrollment](#2-passwordless-enrollment)
3. [Network Zones](#3-network-zones)
4. [Risk-Based Adaptive MFA Policy](#4-risk-based-adaptive-mfa-policy)
5. [Step-Up Authentication](#5-step-up-authentication)
6. [Authenticator Enrollment Policy](#6-authenticator-enrollment-policy)

---

### 1) Enroll Okta Verify

1. Open the **Security** tab then go to **Authenticators**
2. Go to the **Setup** tab then to the right of **Okta Verify** confirm the **Status** says **Active**
3. If it says **Inactive** click the **Actions** dropdown and select **Activate**

<p align="center">
  <img width="1058" height="481" alt="image" src="https://github.com/user-attachments/assets/faa50418-e59f-4da4-9c29-eddcc4da9097" />
</p>

---

### 2) Passwordless Enrollment

1. Go to the **Security** tab and then **Authentication Policies**
2. Select **App Sign-In** then **Create Policy**
3. Create a policy with the following information and then press **Create Policy:**
   - **Name:** Passwordless Policy
   - **Description:** Allow User to Authenticate Without a Password Using Okta Verify
  
<p align="center">
  <img width="481" height="355" alt="image" src="https://github.com/user-attachments/assets/c07a3f4f-2300-4c4b-b877-5b02ccc5823d" />
</p>

4. In the new policy select **Add Rule**
5. Fill in the policy with the following information then **Save:**
   - **Rule Name:** Passwordless Rule
   - **Then Access Is:** Allowed after successful authentication
   - **And User Must Authenticate With:** Any 1 Factor Type
   - **And Authentication Methods:** Allow Specific Authentication Methods. **Check:** Okta Verify FastPass and Okta Verify - TOTP

<p align="center">
  <img width="1362" height="383" alt="image" src="https://github.com/user-attachments/assets/2088aef0-55dc-4ed7-befb-5331ecabca5c" />
</p>

6. Open the **Applications** tab then **select **Add App**
7. Next to **Okta Dashboard** select **Add** then **Add Anyway** then **Done**

<p align="center">
  <img width="898" height="408" alt="image" src="https://github.com/user-attachments/assets/237b3bf0-1fcf-45dd-ac85-4e951e077f14" />
</p>

8. In an **Incognito** browser attempt to sign in to the organization as **John Smith**
9. Confirm that **Okta Verify** is the only method requested during sign-in.

<p align="center">
  <img width="441" height="545" alt="image" src="https://github.com/user-attachments/assets/dbe012c5-3c71-41f2-851c-8c52b83daad3" />
</p>

> [!NOTE]
> When a user has no authenticators enrolled yet, Okta will first prompt for their password so they can complete initial Okta Verify enrollment (since there's no existing factor to satisfy the Passwordless Policy yet). Once Okta Verify is enrolled, subsequent logins will only prompt for Okta Verify — no password required. This is expected behavior, not a bug.

---

### 3) Network Zones

1. In the **Okta Administrator Console** got to the **Security** tab then **Networks**
2. Press the **Add Zone** dropdown then select **IP Zone**
3. Add an IP Zone with the following information the press **Save:**
   - **Zone Name:** Corporate Network
   - **Gateway IPs:** `<Your Public IP Address>/32`
  
<p align="center">
  <img width="868" height="764" alt="Untitled Diagram drawio" src="https://github.com/user-attachments/assets/fa139d32-ecf7-436a-9f38-670bd41ae37a" />
</p>

4. Select **Add Zone** again then **IP Zone**
5. Add an IP Zone with the following information then press **Save:**
   - **Zone Name:** Blocked Reigon
   - **Block Access from IPs Matching Conditions Listed in this Zone:** Check
   - **Gateway IPs:** 192.168.99.0/24
  
<p align="center">
  <img width="871" height="473" alt="image" src="https://github.com/user-attachments/assets/acb5db43-b987-4b55-82aa-1ada8528a17a" />
</p>

---

### 4) Risk-Based Adaptive MFA Policy

1. Go to the **Security** tab and then **Authentication Policies** then **App Sign-In**
2. Go to the **Standard Employee Policy** then select **Edit** then **Add Rule**
3. Create a rule with the following information then **Save:**
   - **Rule Name** Allow on Coporate Network
   - **And User's IP Is:** In Any Network Zone Defined in Okta
   - **Then Access Is:** Allowed After Successful Authentication
   - **And User Must Authenticate With:** Any 1 Factor Type
  
<p align="center">
  <img width="1206" height="332" alt="image" src="https://github.com/user-attachments/assets/85084422-fd00-446c-9c5c-cb056271f964" />
</p>

4. Create a second rule with the following information then **Save:**
   - **Rule Name** Require MFA Outside Network
   - **And User's IP Is:** Not In Any Network Zone Defined in Okta
   - **Then Access Is:** Allowed After Successful Authentication
   - **And User Must Authenticate With:** Password + Another Factor
  
<p align="center">
  <img width="1350" height="529" alt="image" src="https://github.com/user-attachments/assets/91aba024-251a-44cc-99c7-0e08ed47ea11" />
</p>

5. Open the **Application** tab then **Add App**
6. Select **Add** next to **Okta Dashboard**
7. Select **Add Anyway** then **Done**

<p align="center">
  <img width="1329" height="535" alt="image" src="https://github.com/user-attachments/assets/4511422d-81c3-44bf-8dcc-36a3b27cf014" />
</p>

> [!NOTE]
> An app can only be assigned to one Authentication Policy at a time. Assigning Okta Dashboard to Standard Employee Policy automatically removes it from Passwordless Policy — this happens without any extra action and is expected Okta behavior, not an error.

8. In an **Incognito** browser attempt to sign in to the organization as **John Smith**
9. Confirm that **Password** is the only method requested during sign-in.

<p align="center">
  <img width="432" height="572" alt="image" src="https://github.com/user-attachments/assets/b97616b8-98d9-487b-8087-c6962be5784c" />
</p>

---

### 5) Step-Up Authentication

1. Go to the **Security** tab and then **Authentication Policies**
2. Select **App Sign-In** then **Create Policy**
3. Enter the following information then **Create Policy:**
   - **Policy Name:** High Assurance Policy
   - **Description:** Requires Re-Authentication with a Strong Factor for Sensitive Applications
  
<p align="center">
  <img width="483" height="352" alt="image" src="https://github.com/user-attachments/assets/c0e08cd2-46e8-4f42-87f8-f30aa20b55ab" />
</p>

4. Select **Add Rule** and create a rule with the following information then press **Save** then **Save Anyway:**
   - **Rule Name:** Step-Up Rule
   - **If User's IP Is:** Any IP
   - **Then Access Is:** Allowed After Successful Authentication
   - **And User Must Authenticate With:** Password + Another Factor
   - **And Posession Factor Constraints Are:** Uncheck Phishing Resistant
   - **And Prompt for Password Authentication:** Every Time User Signs in to Resource
  
<p align="center">
  <img width="1305" height="475" alt="image" src="https://github.com/user-attachments/assets/fcef1473-0bd3-4403-96e8-d75b3d1454b5" />
</p>

5. Select the **Application** tab then select **Add App**
6. Find the **Okta Dashboard** app press **Add** then **Done**

<p align="center">
  <img width="1421" height="500" alt="image" src="https://github.com/user-attachments/assets/171caee1-02e8-42cf-bd45-d2252d8f5eea" />
</p>

7. In an **Incognito** browser attempt to sign in to the organization as **John Smith**
8. Confirm that **Password** is the first method requested during sign-in.

<p align="center">
  <img width="427" height="595" alt="image" src="https://github.com/user-attachments/assets/f5e7c4b1-348c-41a8-8662-4ae2b875570d" />
</p>

9. Confirm that **Okta Verify** is the second method requested during sign-in.

<p align="center">
  <img width="429" height="549" alt="image" src="https://github.com/user-attachments/assets/b9814c55-73f0-4b48-9ebd-3ae6b77f556f" />
</p>

---

### 6) Authenticator Enrollment Policy

1. Open the **Security** tab then **Authenticators** then open the **Enrollment** tab
2. Then in the **Actions** dropdown and select **Edit**
3. Update the rule set to say the following then **Update Policy:**
   - **Assign to Groups:** Kennon Technologies Employees
   - **Email:** Disabled
   - **Okta Verify:** Required
   - **Password:** Required

4. Open the **Actions** dropdown then select **Activate**

<p align="center">
  <img width="973" height="499" alt="image" src="https://github.com/user-attachments/assets/40ec5359-f7df-4029-9d3b-cdea9d974c80" />
</p>

> [!NOTE]
> To properly test this phase, use a test user who has not yet enrolled any authenticators. If the user already has Okta Verify set up from earlier testing, they won't see the enrollment prompt this policy is meant to trigger.

5. In an **Incognito** browser attempt to sign in to the organization as **Jane Doe**
6. Confirm that **Password** is the first method requested during sign-in.

<p align="center">
  <img width="426" height="589" alt="image" src="https://github.com/user-attachments/assets/a2edd9f8-a27b-4973-a582-81f39f7f5e9d" />
</p>

7. Confirm that **Set Up Okta Verify** is being required as the second method during sign-in.

<p align="center">
  <img width="423" height="548" alt="image" src="https://github.com/user-attachments/assets/920c53cc-4587-4042-9f35-f622b6abf5ed" />
</p>

---

> **Note:** This lab is intentionally left open. The MFA and adaptive authentication 
> policies configured here serve as the foundation for subsequent Okta labs. Continue 
> to the [Okta IAM Lab Series](https://github.com/RyanKennon/Okta-Lab-Series/tree/main) 
> for the next lab in the series.

---
<p align="left">
  <a href="https://github.com/RyanKennon/okta-enterpirse-sso/tree/main">⬅ Lab 4 — Enterprise SSO (SAML 2.0)</a>
</p>
<p align="right">
  <a href="link-to-lab-6-repo">Lab 6 — Okta Lifecycle Management & Provisioning ➡</a>
</p>
