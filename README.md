<p align="center">
  <img width="900" height="500" alt="image" src="https://github.com/user-attachments/assets/2c068a6c-d82b-4a43-ad32-d9fe559e1837" />
</p>

# okta-mfa-adaptive-authentication

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
   - **And Authentication Methods:** Allow Specific Authentication Methods. **Check:** Okta Verify FastPass and Okta Verify - TOTP**

<p align="center">
  <img width="1362" height="383" alt="image" src="https://github.com/user-attachments/assets/2088aef0-55dc-4ed7-befb-5331ecabca5c" />
</p>

6. Open the **Applications** tab then **select **Add App**
7. Next to **Okta Dashboard** select **Add** then **Add Anyway** then **Done**

<p align="center">
  <img width="898" height="408" alt="image" src="https://github.com/user-attachments/assets/237b3bf0-1fcf-45dd-ac85-4e951e077f14" />
</p>

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

4. Select **Add Rule** and create a rule with the following information then press **Save:**
   - **Rule Name:** Step-Up Rule
   - **If User's IP Is:** Any IP
   - **Then Access Is:** Allowed After Successful Authentication
   - **And User Must Authenticate With:** Password + Another Factor
   - **And Prompt for Password Authentication:** Every Time User Signs in to Resource
  
<p align="center">
  <img width="1365" height="545" alt="image" src="https://github.com/user-attachments/assets/f48ec99d-8be0-4f7d-8589-59d173b160cb" />
</p>

5. Select the **Application** tab then select **Add App**
6. Find the **Slack SP - Kennon Technologies** app press **Add** then **Done**

<p align="center">
  <img width="1001" height="510" alt="image" src="https://github.com/user-attachments/assets/ae059b1e-dfed-4ef8-8d08-1277a3c735ed" />
</p>

---

### 6) Authenticator Enrollment Policy

1. Open the **Security** tab then **Authenticators** then open the **Enrollment** tab
2. Make sure the **Employee Enrollment Policy** is active by selecting the **Actions** dropdown and selecting **Activate**
3. Then in the **Actions** dropdown and select **Edit**
4. Update the rule set to say the following then **Update Policy:**
   - **Assign to Groups:** Kennon Technologies Employees
   - **Email:** Optional
   - **Okta Verify:** Required
   - **Password:** Required
  
<p align="center">
  <img width="716" height="494" alt="image" src="https://github.com/user-attachments/assets/2bc37399-4e8d-4845-8158-c61ed2f8c983" />
</p>

### 7) MFA Testing and Validation

1. Open an **Incognito** browser and sign in using **John Smith**
2. Verify **Okta Verify** is the only method asked for when logging in

<p align="center">
  <img width="398" height="505" alt="image" src="https://github.com/user-attachments/assets/65ca28c4-be78-4972-a396-fb61cf10fe59" />
</p>

3. 
