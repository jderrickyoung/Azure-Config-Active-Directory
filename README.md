<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1> Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of  Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1
- Step 2
- Step 3
- Step 4

---

## **Stage 1: Configuring for Active Directory**

### **Step 1: Create Virtual Machines**
- Create two VMs in the Azure Portal: one for the domain controller and another for the client computer.
  
#### Domain Controller VM:
- Create a new Windows Virtual Machine, Resource Group, and Virtual Network.
- Select **Windows Server 2022** and name your domain controller.

![VM Creation](https://i.imgur.com/gUBMF2I.png)

- After deployment, set the domain controller NIC Private IP address to static:
  - Go to **Network settings** → **Network interface** → **IP configuration**.

![Network Settings](https://i.imgur.com/PRh1cy0.png)

  - Select **ipconfig1** and change settings from dynamic to static.

![Dynamic to Static](https://i.imgur.com/Rm4cQFz.png) ![Static IP](https://i.imgur.com/Tp8ySfb.png)

#### Disable Firewall:
- Log in to the VM, right-click the **Start Menu**, and select **Run**. Type `wf.msc` and press Enter.

![Run Firewall](https://i.imgur.com/WjFux83.png) ![Firewall Settings](https://i.imgur.com/ivN2AOH.png)

- In the **Windows Defender Firewall Properties**, turn off the firewall for all profiles.

![Firewall Profiles](https://i.imgur.com/y26iV6B.png) ![Firewall Off](https://i.imgur.com/sRJucp4.png)

#### Client VM:
- Create another Windows Virtual Machine using the same Resource Group and Virtual Network.
- Select **Windows Pro 10** and name your client computer.

![Client VM](https://i.imgur.com/pn1K3Qx.png) ![Windows 10](https://i.imgur.com/HB3ov46.png)

- Set the client VM’s DNS settings to the domain controller’s Private IP:
  - Go to **Network settings** → **Network interface** → **IP configuration** → **DNS servers**.
  - Add a custom DNS server using the domain controller’s Private IP. Restart the VM.

![DNS Configuration](https://i.imgur.com/Ed4jw3Z.png)

#### Verify Connectivity:
- Log in to the client VM, open PowerShell, and ping the domain controller’s Private IP.

![Ping](https://i.imgur.com/UAqIyhx.png)
- Use `ipconfig /all` to verify the DNS Server.

---

## **Stage 2: Installing Active Directory**

### **Install Active Directory Domain Services**
- From the domain controller, open **Server Manager** and select **Add roles and features**.

![Add Roles](https://i.imgur.com/6vmdEgc.png) ![Select Features](https://i.imgur.com/mVrCuLF.png)

- Select **Role-based installation** and choose the domain controller.
- Check **Active Directory Domain Services**.

![Active Directory](https://i.imgur.com/wQJF0Xp.png) ![Features](https://i.imgur.com/1vZUWzk.png) ![Confirmation](https://i.imgur.com/yMVUQ3D.png)

- Enable automatic restart, click **Install**, and close once complete.

![Install Progress](https://i.imgur.com/le6skVk.png)

---

## **Stage 3: Promoting as a Domain Controller**

- In **Server Manager**, click the flag in the top-right corner and select **Promote this server to a domain controller**.

![Promote](https://i.imgur.com/oNp7pjl.png)

- Select **Add new forest**, specify your domain name, and set a password.

![New Forest](https://i.imgur.com/4XDgMDX.png) ![Password](https://i.imgur.com/k5rvrAF.png)

- Proceed through the setup and install.

![Prerequisites](https://i.imgur.com/NiOA8lc.png)

- After the restart, log in as `yourdomain.com\username`.

![Domain Login](https://i.imgur.com/6ZrvpWA.png)

---

## **Stage 4: Creating a Domain Admin User**

- Open **Active Directory Users and Computers** on the domain controller.

![Active Directory](https://i.imgur.com/Zm6Ru3q.png)

- Create organizational units: `_EMPLOYEES` and `_ADMINS`.

![Organizational Units](https://i.imgur.com/5ARWfq4.png) ![Admin Folder](https://i.imgur.com/PIPlJht.png)

- Add a user (e.g., `joan_admin`) and set a password.

![Create User](https://i.imgur.com/dDuHRVA.png) ![User Properties](https://i.imgur.com/1D1QRwN.png)

- Add `joan_admin` to the **Domain Admins Security Group**:
  - Right-click the user → **Properties** → **Member of** → **Add**.

![Add to Group](https://i.imgur.com/cRrFdtb.png) ![Domain Admin](https://i.imgur.com/GjeLQzK.png)

- Log out and back in as `yourdomain.com\joan_admin`.

![Admin Login](https://i.imgur.com/5rgFd7n.png)

---

## **Stage 5: Joining the Client VM to the Domain**

- On the client VM, go to **System Properties** → **Rename this PC** → **Change**.

![Rename PC](https://i.imgur.com/V5rCo30.png) ![Change Domain](https://i.imgur.com/LyfjELm.png)

- Enter the domain name and use `joan_admin` credentials.

![Join Domain](https://i.imgur.com/E75FnTt.png) ![Credentials](https://i.imgur.com/NukZLJ0.png)

- Restart the client VM.

![Restart](https://i.imgur.com/CGDrcjU.png)

- Verify the client appears in **Active Directory Users and Computers** under **_CLIENTS**.

![Client Folder](https://i.imgur.com/LetmfSj.png) ![Move Client](https://i.imgur.com/8NmKIOG.png)

---

## **Stage 6: Adding Non-Administrative Users**

- Log into the client VM as `joan_admin`. Go to **System Properties** → **Remote Desktop** → **Select users**.

![Remote Desktop](https://i.imgur.com/gzUr3gM.png) ![Select Users](https://i.imgur.com/5AjaGNO.png)

- Add **Domain Users**.

![Add Users](https://i.imgur.com/WMfqWGG.png) ![Domain Users](https://i.imgur.com/UlsmvJB.png)

---

## **Stage 7: Creating Bulk Users with PowerShell**

- On the domain controller, open **PowerShell ISE** as an administrator.

![PowerShell ISE](https://i.imgur.com/CTUQ4AZ.png)

- Paste the following [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) and run it to create users.

![Script](https://i.imgur.com/fzZmMgv.png) ![Running Script](https://i.imgur.com/rVeC3TJ.png)

- Verify accounts in `_EMPLOYEES`.

![Users](https://i.imgur.com/sYVQkus.png)

- Log in to the client VM with a script-generated user.

![User Login](https://i.imgur.com/6rkZHo8.png)

---

Congratulations! You have successfully implemented Active Directory within Azure Virtual Machines.

