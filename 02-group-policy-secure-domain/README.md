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

 
![Default Domain Policy](../images/01-gpmc-default-domain-policy.png)


**Screenshot:** Password Policy settings showing the values above.  
![Password Policy settings in Default Domain Policy](../images/02-password-policy-settings.png)


---          

### 2. Force Group Policy update on DC01

Still on **DC01**, open an elevated Command Prompt and run:

```cmd
gpupdate /force
```
### 3. Force Group Policy update on the Windows 10 client (and troubleshoot)

On the **Windows 10 client**, open **Command Prompt as Administrator** and run:

    gpupdate /force

Initially, this failed with a **time synchronization error**:

> Windows was unable to determine whether new Group Policy settings defined by a network administrator should be enforced for this user or computer because this computer’s clock is not synchronized with the clock of one of the domain controllers for the domain...

**Screenshot:** Initial `gpupdate /force` run showing the time-sync error.  
![gpupdate time error](../images/03-win10-gpupdate-time-error.png)

To fix this and get `gpupdate` working, I did the following:


#### 3.1 Fix time and time zone on DC01

On **DC01**:

- Open **Settings → Time & Language → Date & time**.
- Turn **off** **“Set time automatically”** (the lab has no internet time).
- Set the correct:
  - **Time zone** (e.g. `(UTC-05:00) Eastern Time (US & Canada)`)
  - **Date and time**

**Screenshot:** Date & time settings on DC01 with correct time and time zone.  
![DC01 correct time](../images/04-dc01-correct-date-time.png)

#### 3.2 Match the Windows 10 client clock to DC01

On the **Windows 10 client**:

- Right-click the clock → **Adjust date/time**.
- Turn **off** “Set time automatically”.
- Set:
  - The **same time zone** as DC01.
  - The **date and time** to match DC01 (within about a minute).

**Screenshot:** Client Date & time page showing same time zone and corrected time.  
![Win10 matching time](../images/05-win10-date-time-matching-dc01.png)

---

#### 3.3 Confirm the client is joined to `lab.local`

On the **Windows 10 client**:

- Right-click **This PC** → **Properties** → **Advanced system settings**.
- Go to the **Computer Name** tab.

Here I confirmed:

- **Full computer name:** `winbread.lab.local`  
- **Domain:** `lab.local`

**Screenshot:** System Properties → Computer Name tab showing `Domain: lab.local`.  
![Win10 domain membership](../images/06-win10-domain-membership.png)

#### 3.4 Log in with a domain user and re-run `gpupdate`

I realized I had been using a **local user**, not a domain user, earlier.

On the **Windows 10 client**:

1. Sign out.
2. On the login screen, choose **Other user**.
3. Log in as the domain user, for example:
   - **Username:** `LAB\ALopez`
   - **Password:** (domain password set in AD)

4. Open **Command Prompt** and verify:

```cmd
whoami
echo %logonserver%
```
Output confirms:

- `whoami` → `lab\ALopez`
- `%logonserver%` → `\\DC01`

**Screenshot:** `whoami` and `%logonserver%` showing domain user and DC01.  
![whoami and logonserver](../images/07-win10-domain-user-whoami-logonserver.png)

Finally, run:

```cmd
gpupdate /force
```
Now both policies complete successfully:

- `Computer Policy update has completed successfully.`
- `User Policy update has completed successfully.`

**Screenshot:** Successful `gpupdate /force` output on the Win10 client.  
![gpupdate success](../images/05-win10-gpupdate-success.png)







