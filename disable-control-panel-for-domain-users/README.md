## Part 2 – Disable Control Panel for Domain Users

**Goal:** Use Group Policy to restrict access to Control Panel and PC settings for users in the `HQ_Users` OU.

### Prerequisites

- `HQ_Users` OU already created in Active Directory.
- At least one domain user account inside `HQ_Users`.
- Windows 10 client joined to `lab.local` and able to log in as an `HQ_Users` user.

---

### Step 1 – Create and Link a GPO to HQ_Users

1. On **DC01**, open **Group Policy Management** (`gpmc.msc`).
2. Expand:

   - **Forest: lab.local**
   - **Domains**
   - **lab.local**
   - **HQ_Users**

3. Right-click **HQ_Users** → **Create a GPO in this domain, and Link it here…**
4. Name the GPO: **`Disable Control Panel - HQ_Users`** → **OK**.

**Screenshot:**  
`04-gpmc-disable-control-panel-gpo.png` – GPMC showing `HQ_Users` selected with the `Disable Control Panel - HQ_Users` GPO linked.

---

### Step 2 – Configure the GPO to Prohibit Access to Control Panel

1. In **Group Policy Management**, right-click  
   **`Disable Control Panel - HQ_Users` → Edit…**
2. In **Group Policy Management Editor**, navigate:

   ```text
   User Configuration
     → Policies
       → Administrative Templates
         → Control Panel
