## Part 1 – Enforce Domain Password Policy

### Goal

Use Group Policy to enforce stronger passwords for all domain users in **lab.local**, and verify the policy is actually applied on the Windows 10 client.

---

### 1. Configure password policy in the Default Domain Policy

On **DC01**:

1. Open **Group Policy Management** (`gpmc.msc`).
2. Expand **Forest → Domains → lab.local**.
3. Right-click **Default Domain Policy** → **Edit**.
4. Go to:  
   `Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`.
5. Configure for example:
   - **Enforce password history**: 5 passwords remembered  
   - **Maximum password age**: 30 days  
   - **Minimum password length**: 8 characters  
   - **Password must meet complexity requirements**: Enabled  

**Screenshot:** Default Domain Policy selected under `lab.local`.  
![Default Domain Policy](../images/01-gpmc-default-domain-policy.png)


**Screenshot:** Password Policy settings showing the values above.  
`02-password-policy-settings.png`

---          

### 2. Force Group Policy update on DC01

Still on **DC01**, open an elevated Command Prompt and run:

```cmd
gpupdate /force

