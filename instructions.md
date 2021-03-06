# Configuring Microsoft Information Protection Solutions to Protect Your Sensitive Data

# Azure Information Protection HANDS ON LAB

### Introduction

Estimated time to complete this lab

120 minutes

### Objectives

After completing this lab, you will be able to:

- Configure Azure Information Protection labels
- Configure Azure Information Protection policies
- Classify and protect content with Azure Information Protection in Office applications
- Configure Exchange Online Mail Flow Rules for AIP
- Configure SharePoint IRM Libraries
- Configure the Azure Information Protection scanner to discover sensitive data
- Classify and Protect sensitive data discovered by the AIP Scanner

### Prerequisites

Before working on this lab, you must have:

- Familiarity using Windows 10
- Familiarity with PowerShell
- Familiarity with Office 2016 applications

### Lab machine technology

This lab was developed for use in a structured VM environment with the following characteristics:

- Windows Server 2016 Domain Controller (DC1) (Deployed with contoso.com root domain)
	- Provisioned On-Premises admin account named **Contoso\Install** with the password **Somepass1**
	- Provisioned On-Premises AIP Scanner account named **Contoso\AIPScanner** with the password **Somepass1**
	- DNS **CNAME** entry pointing **Fileserver**.contoso.com to **SP1**.contoso.com
- Member Server running SQL Server 2016+ (SQL1)
	- Azure Information Protection client (1.37.19.0) (Available at https://aka.ms/AIPClient)
	- Contoso\AIPScanner account should be **local admin** of this server
	- Contoso\AIPScanner should have **sysadmin** rights on the SQL server
- Member Server running SharePoint 2016 (SP1)
	- Demo PII content deployed to a document library at http://sp1/documents and in a fileshare shared as \\sp1\documents (\\fileserver\documents)
	- Test PII content is available at https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/PII.zip
- 3 Windows 10 Enterprise Clients (CLIENT1-3)
	- Office 365 ProPlus
	- Azure Information Protection client (1.37.19.0) (Available at https://aka.ms/AIPClient)

Microsoft 365 E5 Tenant credentials will be necessary to run through all of the exercises included in this lab.  If you have access to https://demos.microsoft.com, you may use a tenant provisioned there, or your own trial/POC Microsoft 365 Tenant. The users referenced in this guide are based on demo tenants provided at Microsoft events.  Please feel free to use any 3 users in your demo tenant to complete the exercises.

### Reporting Errors

This is a living document and was developed from lab content so it is possible that some of the screenshots or references are not perfectly translated.  **Additionally, the steps in this lab are designed around specific lab tenants so may not be applicable or may need to be altered in other test environments.  Please modify as necessary for your environment**.  

If you run into any issues in this document or the instructions do not work because of changes in the Azure interface or client software, please reach out to me by clicking on the link below. I will make every effort to ensure that the instructions contained in this document are up-to-date and relevant but constructive criticism is always appreciated.

https://aka.ms/AIPLabFeedback

# Exercise 1: Configuring Azure Information Protection Policy

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.

We will next configure AIP Global Policy to use the General sub-label as default, and finally, we will configure a scoped policy to use the new scoped label by default for Word, Excel, and PowerPoint while still using General as default for Outlook.

# Creating, Configuring, and Modifying Sub-Labels

In this task, we will configure a label protected for internal audiences that can be used to help secure sensitive data within your company.  By limiting the audience of a specific label to only internal employees, you can dramatically reduce the risk of unintentional disclosure of sensitive data and help reduce the risk of successful data exfiltration by bad actors.  However, there are times when external collaboration is required, so we will configure a label to match the name and functionality of the Do Not Forward button in Outlook.  This will allow users to more securely share sensitive information outside the company to any recipient.  By using the name Do Not Forward, the functionality will also be familiar to what previous users of AD RMS or Azure RMS may have used in the past.

1. Switch to the Client1 virtual machine.

1. Right-click on **Edge** in the taskbar and click on **New InPrivate window**.

	![jnblioyn.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jnblioyn.jpg)

1. In the InPrivate window, navigate to **https://portal.azure.com/**

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cznh7i2b.jpg)

1. Log in using **your Global Admin Username** and **your Global Admin Password**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/gerhxqeq.jpg)

1. After logging into the portal, type the word **info** into the **search bar** and press **Enter**, then click on **Azure Information Protection**.

	![2598c48n.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/2598c48n.jpg)

	> NOTE: If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> ![ny3fd3da.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ny3fd3da.jpg)

1. Under **classifications** in the left pane, click on **Labels** to load the Azure Information Protection – Labels blade.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/mhocvtih.jpg)

1. In the Azure Information Protection – Labels blade, right-click on **Confidential** and click **Add a sub-label**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/uktfuwuk.jpg)

1. In the Sub-label blade, type **Contoso Internal** for the **Label display name** and for **Description** enter text similar to **Confidential data that requires protection, which allows Contoso Internal employees full permissions. Data owners can track and revoke content.**

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4luorc0u.jpg)

1. Then, under **Set permissions for documents and emails containing this label**, click **Protect**, and under **Protection**, click on **Azure (cloud key)**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/tp97a19d.jpg)

1. In the Protection blade, click **+ Add Permissions**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/layb2pvo.jpg)

1. In the Add permissions blade, click on **+ Add contoso – All members** and click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/zc0iuoyz.jpg)

1. In the Protection blade, click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/u8jv46zo.jpg)

1. In the Sub-label blade, scroll down to the **Set visual marking (such as header or footer)** section and under **Documents with this label have a header**, click **On**.

	> Use the values in the table below to configure the Header.

	| Setting          | Value            |
	|:-----------------|:-----------------|
	| Header text      | **Contoso Internal** |
	| Header font size | **24**               |
	| Header color     | Purple           |
	| Header alignment | Center           |

	> NOTE: These are sample values to demonstrate marking possibilities and **NOT** a best practice.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/0vdoc6qb.jpg)

1. To complete creation of the new sub-label, click the **Save** button and then click **OK** in the Save settings dialog.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/89nk9deu.jpg)

1. In the Azure Information Protection - Labels blade, expand **Confidential** (if necessary) and then click on **Recipients Only**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/eiiw5zbg.jpg)

1. In the Label: Recipients Only blade, change the **Label display name** from **Recipients Only** to **Do Not Forward**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/v54vd4fq.jpg)

1. Next, in the **Set permissions for documents and emails containing this label** section, under **Protection**, click **Azure (cloud key): User defined**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/qwyranz0.jpg)

1. In the Protection blade, under **Set user-defined permissions (Preview)**, verify that only the box next to **In Outlook apply Do Not Forward** is checked, then click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/16.png)

	> INFO: Although there is no action added during this step, it is included to show that this label will only display in Outlook and not in Word, Excel, PowerPoint or File Explorer.

1. Click **Save** in the Label: Recipients Only blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/9spkl24i.jpg)

1.  Click the **X** in the upper right corner of the blade to close.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/98pvhwdv.jpg)



# Configuring Global Policy

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

1. In the Azure Information Protection blade, under **classifications** on the left, click **Policies** then click the **Global** policy.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/24qjajs5.jpg)

1. In the Policy: Global blade, below the labels, click **Add or remove labels**.

1. In the Policy: Add or remove labels blade, verify that all of the labels are assigned to the policy by checking the boxes next to the label names and clicking **OK**.

	>NOTE: If you are creating your own tenant, you may need to create labels like the ones shown in the pictures (the ones we will use in this lab are the Confidential\Recipients Only, Confidential\Contoso Internal, and Highly Confidential\All Employees labels so you will need those at a minimum).

	![d0pxo2m6.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/d0pxo2m6.jpg)

1. In the Policy: Global blade, under the **Configure settings to display and apply on Information Protection end users** section, configure the policy to match the settings shown in the table and image below.

	| Setting | Value |
	|:--------|:------|
	| Select the default label | General |
	|All documents and emails must have a label…|On
	Users must provide justification to set a lower…|On
	For email messages with attachments, apply a label…|Automatic
	Add the Do Not Forward button to the Outlook ribbon|Off

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/mtqhe3sj.jpg)

1. Click **Save**, then **OK** to complete configuration of the Global policy.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/1p1q4pxe.jpg)

1. Click the **X** in the upper right corner to close the Policy: Global blade.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m6e4r2u2.jpg)



# Creating a Scoped Label and Policy

Now that you have learned how to work with global labels and policies, we will create a new scoped label and policy for the Legal team at Contoso.  (If you are using your own demo tenant you may need to create the users and described.)

1. Under **classifications** on the left, click **Labels**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/50joijwb.jpg)

1. In the Azure Information Protection – Labels blade, right-click on **Highly-Confidential** and click **Add a sub-label**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/tasz9t0i.jpg)

1. In the Sub-label blade, enter **Legal Only** for the **Label display name** and for **Description** enter **Data is classified and protected. Legal department staff can edit, forward and unprotect.**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/lpvruk49.jpg)

1. Then, under **Set permissions for documents and emails containing this label**, click **Protect** and under **Protection**, click **Azure (cloud key)**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6ood4jqu.jpg)

1. In the Protection blade, under **Protection settings**, click the **+ Add permissions** link.

	![ozzumi7l.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ozzumi7l.jpg)

1. In the Add permissions blade, click **+ Browse directory**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/2lvwim24.jpg)

1. In the AAD Users and Groups blade, **wait for the names to load**, then check the boxes next to **Alan Steiner** and **Amy Albers**, and click the **Select** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/uishk9yh.jpg)

	> NOTE: In a production environment, you will typically use a synced or Azure AD Group rather than choosing individuals.

1. In the Add permissions blade, click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/stvnaf4f.jpg)

1. In the Protection blade, under **Allow offline access**, reduce the **Number of days the content is available without an Internet connection** value to **3** and press **OK** .

	> INFO: This value determines how many days a user will have offline access from the time a document is opened, and an initial Use License is acquired.  While this provides convenience for users, it is recommended that this value be set appropriately based on the sensitivity of the content.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/j8masv1q.jpg)

1. Click **Save** in the Sub-label blade and **OK** to the Save settings prompt to complete the creation of the Legal Only sub-label.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/dfhoii1x.jpg)

1. In the Azure Information Protection blade, under **Classifications** on the left, click **Policies** then click the **+Add a new policy** link.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ospsddz6.jpg)

1. In the Policy blade, for Policy name, type **Legal Scoped Policy** and click on **Select which users or groups get this policy. Groups must be email-enabled.**

	![tosu2a6j.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/tosu2a6j.jpg)

1. In the AAD Users and Groups blade, click on **Users/Groups**.
1. Then in the second AAD Users and Groups blade, **wait for the names to load** and check the boxes next to **Alan Steiner** and **Amy Albers**.
1. Click the **Select** button.
1. Finally, click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/onne7won.jpg)

1. In the Policy blade, under the labels, click on **Add or remove labels** to add the scoped label.

	![b6e9nbui.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/b6e9nbui.jpg)

1. In the Policy: Add or remove labels blade, check the box next to **Legal Only** and click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/c2429kv9.jpg)

1. In the Policy blade, under **Configure settings to display and apply on Information Protection end users** section, under **Select the default label**, select **Highly Confidential \ Legal Only** as the default label for this scoped policy.

	![6sh1sfz5.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6sh1sfz5.jpg)

1. Click **Save**, then **OK** to complete creation of the Legal Scoped Policy.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/41jembjf.jpg)

1. Click on the **X** in the upper right-hand corner to close the policy.



# Configuring Advanced Policy Settings

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have a protected default label.  Because the Legal Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

1. In the Azure Information Protection blade, under **classifications** on the left, click on **Labels** and then click on the **General** label.

    ![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rvn4xorx.jpg)

1. In the Label: General blade, scroll to the bottom and copy the **Label ID** and close the blade using the **X** in the upper right-hand corner.

    ![8fi1wr4d.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/8fi1wr4d.jpg)

1. In the AIP Portal, under **classifications** on the left, click on **Policies**. Right-click on the **Legal Scoped Policy** and click on **Advanced settings**.

    ![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/2jo71ugb.jpg)

1. In the Advanced settings blade, in the textbox under **NAME**, type **OutlookDefaultLabel**.  In the textbox under **VALUE**, paste the **Label ID** for the **General** label you copied previously, then click **Save and close**.

    > WARNING: CAUTION: Please check to ensure that there are **no spaces** before or after the **Label ID** when pasting as this will cause the setting to not apply.

    ![ezt8sfs3.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ezt8sfs3.jpg)

	> NOTE: This and additional Advanced Policy Settings can be found at https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations



# Defining Recommended and Automatic Conditions

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. Under **classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jyw5vrit.jpg)
1. In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	![cws1ptfd.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cws1ptfd.jpg)
1. In the Condition blade, in the **Select information types** search box, type **credit** and check the box next to **Credit Card Number**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/9rozp61b.jpg)
1. Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/41o5ql2y.jpg)

	> INFO: By default the condition is set to Recommended and a policy tip is created with standardized text.
	>
	>  ![qdqjnhki.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/qdqjnhki.jpg)

1. Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rimezmh1.jpg)
1. Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/em124f66.jpg)
1. Next, expand **Highly Confidential** and click on the **All Employees** sub-label.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/2eh6ifj5.jpg)
1. In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/8cdmltcj.jpg)
1. In the Condition blade, click on **Custom** and enter **Password** for the **Name** and in the textbox below **Match exact phrase or pattern**, type **your user password**.

	![ra7dnyg6.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ra7dnyg6.jpg)
1. Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ie6g5kta.jpg)
1. In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

	![245lpjvk.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/245lpjvk.jpg)
	> NOTE: The policy tip is automatically updated when you switch the condition to Automatic.
1. Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/gek63ks8.jpg)
1. Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/wzwfc1l4.jpg)



# Exercise 2: Client and Exchange Configuration

Now that we have defined some basic AIP Policies, we need to configure some clients to demonstrate the Discovery, Classification, and Protection capabilities of Azure Information Protection.  In this exercise, we will configure Office 365 Applications for 3 test users to demonstrate these policy actions.

Office 365 and the latest GA AIP Client (1.37.19.0) have already been installed on these systems to save time in this lab.  In your production environment, you will need to install the AIP Client manually for testing or using an Enterprise Deployment Tool like System Center Configuration Manager for widespread deployment.

We will also be disabling a mail flow rule in the Exchange Admin Center to allow mail to be sent outside the tenant.  This will allow us to test Do Not Forward and Office 365 Message Encryption scenarios.

# Configuring Applications

In this task, we will configure Word and Outlook for 3 test users.  These users are Alan Steiner (AlanS) and Amy Alberts (AmyA) who we have defined as members of the Legal group, and Eric Grimes (EricG).  This will allow us to demonstrate the differences between the global and scoped policy and demonstrate some of the protection features of Azure Information Protection in the next exercise.

1. On **CLIENT1**, minimize the Edge window and launch **Microsoft Word**.

	> INFO: When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> ![3gm9oeee.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/3gm9oeee.jpg)

2. In the Sign in set up Office dialog, click Sign in.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4yb3mnd1.jpg)
1. In the Activate Office dialog, enter **AlanS@yourtenant.onmicrosoft.com** and press **Next**.

1. In the Enter password dialog, enter **your user password** and click **Sign in**.

1. In the Use this account everywhere on your device dialog, click **Yes**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m1e7l6ei.jpg)

1. Finally, click **Done** to complete the setup.
1. Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. Next, start **Microsoft Outlook**.

	![vlu3sb64.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/vlu3sb64.jpg)
1. Click **Connect** and let Outlook configure.

	> INFO: Login details for **AlanS@yourtenant.onmicrosoft.com** should be automatically populated. If you still see **Install@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> ![13mp3hbw.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/13mp3hbw.jpg)

1. **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	![hjmvdzvv.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/hjmvdzvv.jpg)

1. Switch to **Client2** and and launch **Microsoft Word** (This may take a few seconds).

	> NOTE: If the automation does not work, start **Microsoft Word** by clicking on the icon in the taskbar.
	>
	>![pxyal6hb.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/pxyal6hb.jpg)

	> INFO: When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> ![3gm9oeee.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/3gm9oeee.jpg)

1. In the Sign in set up Office dialog, click Sign in.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4yb3mnd1.jpg)
1. In the Activate Office dialog, enter **AmyA@yourtenant.onmicrosoft.com** and press **Next**.

1. In the Enter password dialog, enter **your user password** and click **Sign in**.

1. In the Use this account everywhere on your device dialog, click **Yes**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m1e7l6ei.jpg)

1. Finally, click **Done** to complete the setup.
1. Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. Next, start **Microsoft Outlook**.

	![vlu3sb64.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/vlu3sb64.jpg)
1. Click **Connect** and let Outlook configure.

	> INFO: Login details for **AmyA@yourtenant.onmicrosoft.com** should be automatically populated. If you still see **Install@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> ![13mp3hbw.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/13mp3hbw.jpg)

1. **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/amepnw76.jpg)

1. Switch to **Client3** and launch **Microsoft Word** (This may take a few seconds).

	> NOTE: If the automation does not work, start **Microsoft Word** by clicking on the icon in the taskbar.
	>
	>![pxyal6hb.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/pxyal6hb.jpg)

	> INFO: When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> ![3gm9oeee.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/3gm9oeee.jpg)

1. In the Sign in set up Office dialog, click Sign in.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4yb3mnd1.jpg)
1. In the Activate Office dialog, enter **EricG@yourtenant.onmicrosoft.com** and press **Next**.

1. In the Enter password dialog, enter **your user password** and click **Sign in**.

1. In the Use this account everywhere on your device dialog, click **Yes**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m1e7l6ei.jpg)

1. Finally, click **Done** to complete the setup.
1. Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. Next, start **Microsoft Outlook**.

	![vlu3sb64.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/vlu3sb64.jpg)
1. Click **Connect** and let Outlook configure.

	> INFO: Login details for **EricG@yourtenant.onmicrosoft.com** should be automatically populated. If you still see **Install@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> ![13mp3hbw.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/13mp3hbw.jpg)

1. **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/hjmvdzvv.jpg)



# Configuring Exchange Online Mail Flow Rules

>NOTE: This task is unnecessary if using your own tenant but may be necessary using demos.microsoft.com tenants. If the rule does not exist, move to the next exercise.

1. Switch to Client1.

1. Right-click on **Edge** in the taskbar and click on **New InPrivate window**.

	![jnblioyn.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jnblioyn.jpg)
1. In the InPrivate window, navigate to **https://outlook.office365.com/ecp/**.

1. Log in using **your Global Admin Username** and **your Global Admin Password**.

1. In the Exchange admin center, click on **mail flow** on the left, then uncheck the box next to **Delete if sent outside the organization**.

	![kgoqkfrs.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/kgoqkfrs.jpg)
1. Wait for the view to update showing the disabled rule, then **minimize this window** and save it for when we add new rules in Exercise 4.



# Exercise 3: Testing AIP Policies

Now that you have 3 test systems with users being affected by different policies configured, we can start testing these policies.  This exercise will run through various scenarios to demonstrate the use of AIP global and scoped policies and show the functionality of recommended and automatic labeling.

# Testing User Defined Permissions

One of the most common use cases for AIP is the ability to send emails using User Defined Permissions (Do Not Forward). In this task, we will send an email using the Do Not Forward label to test that functionality.

1. Switch to Client3.
1. In Microsoft Outlook, click on the **New email** button.

	![6wan9me1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6wan9me1.jpg)

	> INFO: Note that the **Sensitivity** is set to **General** by default.
	>
	> ![5esnhwkw.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/5esnhwkw.jpg)

1. Send an email to Alan and Amy (**AlanS;AmyA**). You may optionally add an external email address (preferably from a major social provider like gmail, yahoo, or outlook.com) to test the external recipient experience. For the **Subject** and **Body** type **Test Do Not Forward Email**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/h0eh40nk.jpg)

1. In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	![901v6vpa.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/901v6vpa.jpg)

1. Click on **Confidential** and then the **Do Not Forward** sub-label and click **Send**.

	![w8j1w1lm.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/w8j1w1lm.jpg)

	> INFO: If you receive the error message below, click on the Confidential \ Contoso Internal sub-label to force the download of your AIP identity certificates, then follow the steps above to change the label to Confidential \ Do Not Forward.
	>
	> ![6v6duzbd.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6v6duzbd.jpg)

1. Switch over to Client1 or Client2 and review the email in Alan or Amy’s Outlook.  You will notice that the email is automatically shown in Outlook natively.

	![0xby56qt.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/0xby56qt.jpg)

	> NOTE: The **Do Not Forward** protection template will normally prevent the sharing of the screen and taking screenshots when protected documents or emails are loaded.  However, since this screenshot was taken within a VM, the operating system was unaware of the protected content and could not prevent the capture.
	>
	>It is important to understand that although we put controls in place to reduce risk, if a user has view access to a document or email they can take a picture with their smartphone or even retype the message. That said, if the user is not authorized to read the message then it will not even render and we will demonstrate that next.

	> INFO: If you elected to send a Do Not Forward message to an external email, you will have an experience similar to the images below.  These captures are included to demonstrate the functionality for those that chose not to send an external message.
	>
	> ![tzj04wi9.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/tzj04wi9.jpg)
	>
	> Here the user has received an email from Eric Grimes and they can click on the **Read the message** button.
	>
	>![wiefwcho.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/wiefwcho.jpg)
	>
	>Next, the user is given the option to either log in using the social identity provider (**Sign in with Google**, Yahoo, Microsoft Account), or to **sign in with a one-time passcode**.
	>
	>If they choose the social identity provider login, it should use the token previously cached by their browser and display the message directly.
	>
	>If they choose one-time passcode, they will receive an email like the one below with the one-time passcode.
	>
	>![m6voa9xi.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m6voa9xi.jpg)
	>
	>They can then use this code to authenticate to the Office 365 Message Encryption portal.
	>
	>![8pllxint.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/8pllxint.jpg)
	>
	>After using either of these authentication methods, the user will see a portal experience like the one shown below.
	>
	>![3zi4dlk9.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/3zi4dlk9.jpg)


# Testing Global Policy

In this task, we will create a document and send an email to demonstrate the functionality defined in the Global Policy.

1. Switch to Client3.
1. In Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6wan9me1.jpg)

1. Send an email to Alan, Amy, and yourself (**AlanS;AmyA;your email address**).  For the **Subject** and **Body** type **Test Contoso Internal Email**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/9gkqc9uy.jpg)

1. In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/901v6vpa.jpg)

1. Click on **Confidential** and then **Contoso Internal** and click **Send**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/yhokhtkv.jpg)
1. On Client1 or Client2, observe that you are able to open the email natively in the Outlook client. Also observe the **header text** that was defined in the label settings.

	![bxz190x2.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/bxz190x2.jpg)

	> INFO: In your email, note that you will be unable to open this message.  This experience will vary depending on the client you use (the image below is from Outlook 2016 for Mac) but they should have similar messages after presenting credentials. Since this is not the best experience for the recipient, in Exercise 4, we will configure Exchange Online Mail Flow Rules to prevent content classified with internal only labels from being sent to external users.

	>![52hpmj51.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/52hpmj51.jpg)



# Testing Scoped Policy

In this task, we will create a document and send an email from one of the users in the Legal group to demonstrate the functionality defined in the first exercise. We will also show the behavior of the No Default Label policy on documents.

1. Switch to Client1.
1. In Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ldjugk24.jpg)

1. Send an email to Amy and Eric (**AmyA;EricG**).  For the **Subject** and **Body** type **Test Highly Confidential Legal Email**.
1. In the Sensitivity Toolbar, click on **Highly Confidential** and the **Legal Only** sub-label, then click **Send**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ny1lwv0h.jpg)
1. Switch to Client2 and click on the email.  You should be able to open the message natively in the client as AmyA.

	![qeqtd2yr.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/qeqtd2yr.jpg)
1. Switch to Client3 and click on the email. You should be unable to open the message as EricG.

	![6y99u8cl.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6y99u8cl.jpg)

	> INFO: You may notice that the Office 365 Message Encryption wrapper message is displayed in the preview pane.  It is important to note that the content of the email is not displayed here.  The content of the message is contained within the encrypted message.rpmsg attachment and only authorized users will be able to decrypt this attachment.
	>
	>![w4npbt49.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/w4npbt49.jpg)
	>
	>If an unauthorized recipient clicks on **Read the message** to go to the OME portal, they will be presented with the same wrapper message.  Like the external recipient from the previous task, this is not an ideal experience. So, you may want to use a mail flow rule to manage scoped labels as well.
	>
	>![htjesqwe.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/htjesqwe.jpg)



# Testing Recommended and Automatic Classification

In this task, we will test the configured recommended and automatic conditions we defined in Exercise 1.  Recommended conditions can be used to help organically train your users to classify sensitive data appropriately and provides a method for testing the accuracy of your dectections prior to switching to automatic classification.  Automatic conditions should be used after thorough testing or with items you are certain need to be protected. Although the examples used here are fairly simple, in production these could be based on complex regex statements or only trigger when a specific quantity of sensitive data is present.

1. Switch to Client3 and launch **Microsoft Word**.
1. In Microsoft Word, create a new **Blank document** and type **My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368** and **save** the document.

	> NOTE: This card number is a fake number that was generated using the Credit Card Generator for Testing at https://developer.paypal.com/developer/creditCardGenerator/.  The Microsoft Classification Engine uses the Luhn Algorithm to prevent false positives so when testing, please make sure to use valid numbers.

1. Notice that you are prompted with a recommendation to change the classification to Confidential \ Contoso Internal. Click on **Change now** to set the classification and protect the document.

	![url9875r.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/url9875r.jpg)
	> INFO: Notice that, like the email in Task 2 of this exercise, the header value configured in the label is added to the document.
	>
	>![dcq31lz1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/dcq31lz1.jpg)
1. In Microsoft Word, create a new **Blank document** and type **my password is pass@word1** and **save** the document.

	>NOTE: Notice that the document is automatically classified and protected wioth the Highly Confidential \ All Employees label.
	>
	>![6vezzlnj.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6vezzlnj.jpg)
1. Next, in Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ldjugk24.jpg)

1. Draft an email to Amy and Alan (**AmyA;AlanS**).  For the **Subject** and **Body** type **Test Highly Confidential All Employees Automation**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4v3wrrop.jpg)
1. Attach the **second document you created** to the email.

	![823tzyfd.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/823tzyfd.jpg)

	> NOTE: Notice that the email was automatically classified as Highly Confidential \ All Employees.  This functionality is highly recommended because matching the email classification to attachments provides a much more cohesive user experience and helps to prevent inadvertent information disclosure in the body of sensitive emails.
	>
	>![yv0afeow.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/yv0afeow.jpg)

1. In the email, click **Send**.


# Exercise 4: Exchange Online IRM Capabilities

Exchange Online can work in conjunction with Azure Information Protection to provide advanced capabilities for protecting sensitive data being sent over email.  You can also manage the flow of classified content to ensure that it is not sent to unintended recipients.

# Configuring Exchange Online Mail Flow Rules

In this task, we will configure a mail flow rule to detect credit card information traversing the network in the clear and encrypt it using the Encrypt Only RMS Template.  We will also create a mail flow rule to prevent messages classified as Confidential \ Contoso Internal from being sent to external recipients.

1. Switch to Client1 and restore the **Exchange admin center** browser window.

	> NOTE: If you closed the window, open an Edge InPrivate window and navigate to **https://outlook.office365.com/ecp/** and log in using **your Global Admin Username** and **your Global Admin Password**.

2. Ensure you are under **mail flow** > **rules**, then click on the **plus icon** and click **Create a new rule...**

	![5mfzbjt1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/5mfzbjt1.jpg)
1. In the new rule window, for **Name** type **Sensitive Data Encrypt Only**.
1. Under **\*Apply this rule if...**, in the drop-down, click on **The recipient is located...**.
1. In the select recipient location dialog, in the drop-down, click **Outside the organization**, and click **OK**.

	![x2cj3zor.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/x2cj3zor.jpg)
1. Next, click on **More options...** to display th option to add additional conditions.

	![rpqtrf8m.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rpqtrf8m.jpg)
1. Click on **add condition**.

	![mjcrysfj.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/mjcrysfj.jpg)
1. Click the **Select one** drop-down and hover over **The message...**, and click on **contains any of these types of sensitive information**.

	![ezuengms.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ezuengms.jpg)
	![kq8wxegt.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/kq8wxegt.jpg)
1. In the Contains any of these sensitive information types dialog, click the **plus sign**.

	![53f6tfbi.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/53f6tfbi.jpg)
1. In the Sensitive information types window, scroll down and click on **Credit Card Number**, then click **Add** and **OK**.

	![dgmsayk6.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/dgmsayk6.jpg)
1. In the Contains any of these sensitive information types dialog, click **OK**.

	![dihs4zqr.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/dihs4zqr.jpg)
1. In the new rule window, under \*Do the following..., in the Select one drop-down, hover over **Modify the message security...** and click on **Apply Office 365 Message Encryption and rights protection**.

	![cu1bgeho.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cu1bgeho.jpg)
	![rwo2scwt.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rwo2scwt.jpg)
1. In the select RMS template dialog, in the RMS template drop-down, click on **Encrypt** and click **OK**.

	![c8r08bh7.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/c8r08bh7.jpg)
1. Review the settings in the new rule window and click **Save**.

	![wwlsz0p2.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/wwlsz0p2.jpg)
	> NOTE: Next, we need to capture the **Label ID** for the **Confidential \ Contoso Internal** label.

1. Switch to the Azure Portal and under **classifications** click on Labels, then expand **Confidential** and click on **Contoso Internal**.

	![w2w5c7xc.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/w2w5c7xc.jpg)

	> NOTE: If you closed the azure portal, open an Edge InPrivate window and navigate to **https://portal.azure.com**.

2. In the Label: Contoso Internal blade, scroll down to the Label ID and **copy** the value.

	![lypurcn5.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/lypurcn5.jpg)

	> WARNING: Make sure that there are no spaces before or after the Label ID as this will cause the mail flow rule to be ineffective.

1. Return the rules section of the Exchange admin center, click on the **plus icon** and click **Create a new rule...**

	![5mfzbjt1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/5mfzbjt1.jpg)
1. In the new rule window, for **Name** type **Block Contoso Internal to External Recipient**.
1. Under **\*Apply this rule if...**, in the drop-down, click on **The recipient is located...**.
1. In the select recipient location dialog, in the drop-down, click **Outside the organization**, and click **OK**.

	![on6fbr0d.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/on6fbr0d.jpg)
1. Next, click on **More options...** to display th option to add additional conditions.

	![rpqtrf8m.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rpqtrf8m.jpg)
1. Click on **add condition**.

	![cqlps27h.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cqlps27h.jpg)
1. Click the **Select one** drop-down and hover over **A message header...**, and click on **includes any of these words**.

	![zus8h1mn.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/zus8h1mn.jpg)
1. Click on **\*Enter text...**, and in the specify header name dialog, type **msip_labels** then click **OK**.

	![jrpy4k02.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jrpy4k02.jpg)
1. Next, click on **\*Enter words...**, and in the specify words or phrases dialog, type **MSIP_LABEL_**, **paste the Label ID value** for the Confidential \ Contoso Internal label, then type **_enabled\=True;**. Next, click the **plus sign**, and then click **OK**.

	![temiq3e1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/temiq3e1.jpg)
1. In the new rule window, under \*Do the following..., in the Select one drop-down, hover over **Block the message...**, and click **reject the message and include an explanation**.

	![04mlyfs3.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/04mlyfs3.jpg)
	![akeykn8a.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/akeykn8a.jpg)

1. In the **specify rejection reason**, type **Contoso Internal messages cannot be sent to external recipients.** and click **OK**.

	![4odbewl3.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4odbewl3.jpg)

1. Review the rule and make sure it looks similar to the picture below and click **Save**.

	![lgg2r4se.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/lgg2r4se.jpg)



# Demonstrating Exchange Online Mail Flow Rules

In this task, we will send emails to demonstrate the results of the Exchange Online mail flow rules we configured in the previous task.  This will demonstrate some ways to protect your sensitive data and ensure a positive user experience with the product.

> NOTE: Some of the steps in the exercise may be unnecessary if you do not have DLP rules configured for Credit Cards.  If that is the case, simply skip over those steps.

1. Switch to Client3.
1. In Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6wan9me1.jpg)

1. Send an email to Alan, Amy, and yourself (**AlanS;AmyA;your email address**).  For the **Subject**, type **Test Credit Card Email** and for the **Body**, type **My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368**, then click **Send**.

	> INFO: Notice that there is a policy tip that has popped up to inform you that there is a credit card number in the email and it is being shared outside the organization.  This type of policy tip can be defined with the Office 365 Security and Compliance center and was pre-staged in the demo tenants we are using.

1. Switch to Client1 and review the received email.

	![pidqfaa1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/pidqfaa1.jpg)

	> INFO: Note that there is no encryption applied to the message.  That is because we set up the rule to only apply to external recipients.  If you were to leave that condition out of the mail flow rule, internal recipients would also receive an encrypted copy of the message.  The image below shows the encrypted message that was received externally.
	>
	>![c5foyeji.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/c5foyeji.jpg)
	>
	>Below is another view of the same message received in Outlook Mobile on an iOS device.
	>
	>![599ljwfy.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/599ljwfy.jpg)

	>NOTE: Note that you have received a message from your DLP policy stating that the email was not sent to the external recipient because it contained a credit card number.

2. On Client 1, open an Edge InPrivate window to **https://protection.office.com**.
3. In the Security and Compliance Center, expand **Data loss prevention** and click on **Policy**.  Then, in the Policy blade, click on the **Default Office 365 DLP Policy**.

	![a2m7ryn4.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/a2m7ryn4.jpg)
1. In the Default Office 365 DLP Policy blade, next to Policy settings, click **Edit**.

	![jsdej5i4.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jsdej5i4.jpg)
1. In the Editing policy settings blade, disable the switch next to **Items containing 1-9 credit card numbers shared externally** and click **Save**.

![5y5gg696.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/5y5gg696.jpg)
1. Return to Client3.
1. In Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6wan9me1.jpg)

1. Send an new email to Alan, Amy, and yourself (**AlanS;AmyA;your email address**).  For the **Subject**, type **Test Credit Card Email 2** and for the **Body**, type **My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368**, then click **Send**.
1. Wait a few moments for the DLP policy to recognize the credit card number and external email and then in the DLP Policy Tip, click **override**.

	![aezwvoir.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/aezwvoir.jpg)
1. In the Override dialog, type **Lab Demo** and click **Override**

	![7o8406n7.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/7o8406n7.jpg)
	>INFO: Notice that you do not receive the error messag this time.  Log into your personal email and you will see that the email has been encrypted in transit by the Exchange Online Mail Flow Rule defined in the previous exercise.
1. Next, in Microsoft Outlook, click on the **New email** button.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6wan9me1.jpg)
1. Send an email to Alan, Amy, and yourself (**AlanS;AmyA;your email address**).  For the **Subject** and **Body** type **Another Test Contoso Internal Email**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/d476fmpg.jpg)

1. In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/901v6vpa.jpg)

1. Click on **Confidential** and then **Contoso Internal** and click **Send**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/yhokhtkv.jpg)
1. In about a minute, you should receive an **Undeliverable** message from Exchange with the users that the message did not reach and the message you defined in the previous task.

	![kgjvy7ul.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/kgjvy7ul.jpg)

> NOTE: There are many other use cases for Exchange Online mail flow rules but this should give you a quick view into what is possible and how easy it is to improve the security of your sensitive data through the use of Exchange Online mail flow rules and Azure Information Protection.



# Exercise 5: SharePoint IRM Configuration

In this exercise, you will configure SharePoint Online Information Rights Management (IRM) and configure a document library with an IRM policy to protect documents that are downloaded from that library.


# Enable Information Rights Management in SharePoint Online

In this task, we will enable Information Rights Management in SharePoint Online.

1. Switch to **Client3**.

1. Launch an Edge InPrivate session to **https://admin.microsoft.com/AdminPortal/Home#/homepage**.

1. If needed, log in using **your Global Admin Username** and **your Global Admin Password**.

1. Hover over the **Admin centers** section of the bar on the left and choose **SharePoint**.

	![r5a21prc.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/r5a21prc.jpg)

1. In the SharePoint admin center click on **settings**.

1. Scroll down to the Information Rights Management (IRM) section and select the option button for **Use the IRM service specified in your configuration**.

1. Click the **Refresh IRM Settings** button.

	![1qv8p13n.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/1qv8p13n.jpg)

	>NOTE: After the browser refreshes, you can scroll down to the same section and you will see a message stating **We successfully refreshed your setings.**
	>
	>![daeglgk9.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/daeglgk9.jpg)

1. Keep the browser open and go to the next task.
 


# Site Creation and Information Rights Management Integration

In this task, we will create a new SharePoint site and enable Information Rights Management in a document library.

1. In the upper left-hand corner of the page, click on the **app launcher** and click on **SharePoint** in the list.

	![ahiylfbv.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ahiylfbv.jpg)

	![s5wj8fpe.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/s5wj8fpe.jpg)

1. Dismiss any introductory screens and, at the top of the page, click **+Create site**.

	![7v8wctu2.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/7v8wctu2.jpg)

1. On the Create a site page, click **Team site**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/406ah98f.jpg)

1. On the next page, type **IRM Demo** for **Site name** and for the **Site description**, type **This is a team site for demonstrating SharePoint IRM capabilities** and set the **Privacy settings** to **Public - anyone in the organization can access the site** and click **Next**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ug4tg8cl.jpg)

1. On the Add group members page, click **Finish**.
1. In the newly created site, on the left navigation bar, click **Documents**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/yh071obk.jpg)

1. In the upper right-hand corner, click the **Settings icon** and click **Library settings**.

	![1qo31rp6.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/1qo31rp6.jpg)

1. On the Documents > Settings page, under **Permissions and Management**, click **Information Rights Management**.

	![ie2rmsk2.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ie2rmsk2.jpg)

1. On the Settings > Information Rights Management Settings page, check the box next to Restrict permissions on this library on download and under **Create a permission policy title** type **Contoso IRM Policy**, and under **Add a permission policy description** type **This content contained within this file is for use by Contoso Corporation employees only.**

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m9v7v7ln.jpg)
1. Next, click on **SHOW OPTIONS** below the policy description and in the **Set additional IRM library settings** section, check the boxes next to **Do not allow users to upload documents that do not support IRM** and **Prevent opening documents in the browser for this Document Library**.

	![0m2qqtqn.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/0m2qqtqn.jpg)
	>INFO: These setting prevent the upload of documents that cannot be protected using Information Rights Managment (Azure RMS) and forces protected documents to be opened in the appropriate application rather than rendering in the SharePoint Online Viewer.

1. Next, under the **Configure document access rights** section, check the box next to **Allow viewers to run script and screen reader to function on downloaded documents**.

	![72fkz2ds.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/72fkz2ds.jpg)
	>NOTE: Although this setting may reduce the security of the document, this is typically provided for accessibility purposes.
1. Finally, in the **Configure document access rights** section, check the box next to  **Users must verify their credentials using this interval (days)** and type **7** in the text box.

	![tt1quq3f.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/tt1quq3f.jpg)
1. At the bottom of the page, click **OK** to complete the configuration of the protected document library.
1. On the Documents > Settings page, in the left-hand navigation pane, click on **Documents** to return to the document library. section.

1. Leave the browser open and continue to the next task.
 


# Uploading Content to the Document Library

Create an unprotected Word document, label it as Internal, and upload it to the document library.

1. launch **Microsoft Word**.
1. Create a new **Blank document**.

	>NOTE: Notice that by default the document is labeled as the unprotected classification **General**.

1. In the Document, type **This is a test document**.

1. **Save** the document and **close Microsoft Word**.
1. Return to the IRM Demo protected document library and click on **Upload > Files**.

	![m95ixvv1.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m95ixvv1.jpg)
1. Navigate to the location where you saved the document, select it and click **Open** to upload the file.

	>NOTE: Note that despite this document being labeled, the Sensitivity is not listed.

1. To resolve this, on the right-hand side of the document library, click on the **+ Add column** header and click on **More...**.

	![y8h1vf7o.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/y8h1vf7o.jpg)
1. In the Settings > Create Column page, under **Column name:**, type **Sensitivity**.  Verify that **The type of information in this column is:** is set to **Single line of text**, and click **OK**.

	![66hzke2b.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/66hzke2b.jpg)
1. In the document library, click on the **Show actions** button to the right of the uploaded document and click **Delete**.

	![gt7sjulo.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/gt7sjulo.jpg)
	>NOTE: This is only necessary to expedite the appearance of the Sensitivity metadata.  In production, this would be unnecessary.
1. Re-upload the document and you will see that the Sensitivity column is populated.

	![0yr96t56.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/0yr96t56.jpg)
1. Next, minimize the browser window and right-click on the desktop. Hover over **New >** and click on **Microsoft Access Database**. Name the database **BadFile**.

	![e3nxt4a2.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/e3nxt4a2.jpg)
1. Return to the document library and attempt to upload the file.

	>INFO: Notice that you are unable to upload the file because it cannot be protected.
	>
	>![432hu3pi.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/432hu3pi.jpg)


# SharePoint IRM Functionality

Files that are uploaded to a SharePoint IRM protected document library are protected upon download based on the user's access rights to the document library.  In this task, we will share a document with Amy Alberts and review the access rights provided.

1. Select the uploaded document and click **Share** in the action bar.

	![1u2jsod7.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/1u2jsod7.jpg)
1. In the Send Link dialog, type **AmyA** and click on **Amy Alberts** then **Send**.

	![j6w1v4z9.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/j6w1v4z9.jpg)
1. Switch to Client2.
1. Open Outlook and click on the email from CIE Administrator, then click on the **Open** link.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/v39ez284.jpg)
1. This will launch the IRM Demo document library.  Click on the document to open it in Microsoft Word.

	![xmv9dmvk.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/xmv9dmvk.jpg)
1. After the document opens, you will be able to observer that it is protected.  Click on the View Permissions button to review the restrictions set on the document.

	![4uya6mro.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/4uya6mro.jpg)
	>NOTE: These permissions are based on the level of access that they user has to the document library.  In a production environment most users would likely have less rights than shown in this example.


 
# Exercise 6: Azure Information Protection Scanner

The Azure Information Protection scanner allows you to discover and optionally classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.

In this exercise, you will use PowerShell Scripts to create an account for scanner in your tenant, install the AIP scanner, and acquire an authentication token for the AIP scanner to use. Once the scanner is prepared, you will run a full discovery and then finally classify and protect identified sensitive data.

> NOTE: This exercise is based on the September GA version of the AIP scanner (1.37.19.0). The AIP client and Azure AD PowerShell cmdlets have been installed on SQL1 to save time.

![7fs9fx6b.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/7fs9fx6b.jpg)


# Connecting to Azure AD and Creating Service Account

In this task, we will connect to Azure AD Powershell using the provided tenant credentials and create a cloud based service account for use with the Azure Information Protection scanner.

1. Switch to SQL1 and (if necessary) log in using the username **Contoso\Install** and the password **Somepass1**.
1. Right-click on the **PowerShell** icon in the taskbar and click on **Run as Administrator**.

	![7to6p334.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/7to6p334.jpg)
1. In PowerShell, run **Connect-AzureAD** and use the username and password below.

	**your Global Admin Username**

	**your Global Admin Password**

1. Next, we must build the PasswordProfile object for use when creating the cloud service account.  This is done by running the commands below.

    **$PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile**

    **$PasswordProfile.Password = "Somepass1"**

    **$PasswordProfile.ForceChangePasswordNextLogin = $false**

1. Now, we can create the cloud service account using the command below.

	>WARNING: Ensure you replace **yourtenant.onmicrosoft.com** with valid tenant information or this command **WILL** fail.

    **New-AzureADUser -AccountEnabled $True -DisplayName "AIP Scanner Cloud Service" -PasswordProfile $PasswordProfile -MailNickName "AIPScanner" -UserPrincipalName "AIPScanner@yourtenant.onmicrosoft.com"**



# Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. At the PowerShell prompt, type **$SQL = "SQL1"**
2. Next, type **Install-AIPScanner -SQLServerInstance $SQL** and press **Enter**.
3. When prompted, provide the credentials for the AIP scanner service account.

	**Contoso\AIPScanner**

	**Somepass1**

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/pc9myg9x.jpg)

	> INFO: You should see a success message like the one below.
	>
	>![w7goqgop.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/w7goqgop.jpg)

1. Right-click on the **Windows** button in the lower left-hand corner and click on **Run**.
1. In the Run dialog, type **Services.msc** and click **OK**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/h0ys0h4u.jpg)
1. In the Services console, double-click on the **Azure Information Protection Scanner** service.
1. On the **Log On** tab of the Azure Information Protection Scanner Service Properties, verify that **Log on as:** is set to the **Contoso\AIPScanner** service account.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/ek9jsd0a.jpg)



# Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended.

1. At the PowerShell prompt, **type the commands below** and press **Enter** to create a new Web App Registration and Service Principal in Azure AD.

   ```
   New-AzureADApplication -DisplayName AIPOnBehalfOf -ReplyUrls http://localhost
   $WebApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPOnBehalfOf'"
   New-AzureADServicePrincipal -AppId $WebApp.AppId
   $WebAppKey = New-Guid
   $Date = Get-Date
   New-AzureADApplicationPasswordCredential -ObjectId $WebApp.ObjectID -startDate $Date -endDate $Date.AddYears(1) -Value $WebAppKey.Guid -CustomKeyIdentifier "AIPClient"
	```
1. Next, we must build the permissions object for the Native App Registration.  This is done using the commands below.

   ```
   $AIPServicePrincipal = Get-AzureADServicePrincipal -All $true | ? {$_.DisplayName -eq 'AIPOnBehalfOf'}
   $AIPPermissions = $AIPServicePrincipal | select -expand Oauth2Permissions
   $Scope = New-Object -TypeName "Microsoft.Open.AzureAD.Model.ResourceAccess" -ArgumentList $AIPPermissions.Id,"Scope"
   $Access = New-Object -TypeName "Microsoft.Open.AzureAD.Model.RequiredResourceAccess"
   $Access.ResourceAppId = $WebApp.AppId
   $Access.ResourceAccess = $Scope
	```
1. Next, we will use the object created above to create the Native App Registration.

   ```
   New-AzureADApplication -DisplayName AIPClient -ReplyURLs http://localhost -RequiredResourceAccess $Access -PublicClient $true
   $NativeApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPClient'"
   New-AzureADServicePrincipal -AppId $NativeApp.AppId
	```

1. Finally, we will output the Set-AIPAuthentication command by running the command below.

   ```
   "Set-AIPAuthentication -WebAppID " + $WebApp.AppId + " -WebAppKey " + $WebAppKey.Guid + " -NativeAppID " + $NativeApp.AppId | Out-File C:\Scripts\Set-AIPAuthentication.txt
	```
1. Type **Start c:\Scripts\Set-AIPAuthentication.txt** and press **Enter**, then **copy the command** to the clipboard.
1. Click on the Start menu and type **PowerShell**, right-click on the PowerShell program, and click **Run as a different user**.

	![zgt5ikxl.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/zgt5ikxl.jpg)
1. When prompted, enter the username **Contoso\AIPScanner** and password **Somepass1** and click **OK**.
1. Paste the copied Set-AIPAuthentication command into this window and run it.

	>WARNING: Ensure you replace **yourtenant.onmicrosoft.com** with valid tenant information or this command **WILL** fail.
1. When prompted, enter the user **AIPScanner@yourtenant.onmicrosoft.com** and the password **Somepass1**

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/qfxn64vb.jpg)
1. In the Permissions requested window, click **Accept**.

   ![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/nucv27wb.jpg)

	>INFO: You will a message like the one below in the PowerShell window once complete.
	>
	>![y2bgsabe.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/y2bgsabe.jpg)
	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/y2bgsabe.jpg)
1. In the PowerShell window, type **Restart-Service AIPScanner** and press **Enter**.



# Reviewing Initial Document State

In this task, we will review documents in SharePoint and a file share to show that there is sensitive information sitting unprotected on the network.

1. Switch to Client3 and (if necessary) log in using the Install account with a password of **Somepass1**.

1. Navigate to http://sp1/documents. Provide the credentials **Install** and **Somepass1** if prompted.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/hipavcx6.jpg)
1. Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets. If you get a SharePoint error, please click **refresh** in the browser.



	> NOTE: Observe that although there is Credit Card or SSN data in these documents, they are still unprotected and classified as General. You can also see these same documents on a file share at \\fileserver\documents in their unprotected state.  Close any opened documents **without saving**.
	>
	>![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/htd29fpf.jpg)



# Reviewing AIP Scanner Status

Now that the AIP Scanner has been fully installed and authenticated, we can take a look at the initial state of the Scanner.

1. Switch to SQL1.
1. In the open PowerShell window from the previous task, type **Get-AIPScannerStatus** and press **Enter**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jltma5e0.jpg)

	> NOTE: Notice that the scanner starts in an **Idle** state.
1. Next, type **Get-AIPScannerConfiguration** and **Enter**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/bso8342f.jpg)

	> NOTE: Review the settings here.  Note that **Enforce** is **Off** by default, the **Schedule** is set to **Manual**, and **DiscoverInformationTypes** is set to **PolicyOnly**.
	>
	>This means that once I configure repositories, if I run`Start-AIPScan`the scanner would **run once**, would **identify sensitive data based only on Automatic conditions I have configured** in the portal, and if it found those, **it would not classify or protect those files**.  Over the next several tasks we will modify these settings to change the functionality of the scanner.



# Configuring Repositories

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional

> NOTE: SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. In the PowerShell window on SQL1 run the commands below

    ```
    Add-AIPScannerRepository -Path http://sp1/documents -SetDefaultLabel Off
	```
	```
	Add-AIPScannerRepository -Path \\fileserver\documents -SetDefaultLabel Off
    ```
	>INFO: Notice that we added the **-SetDefaultLabel Off** switch to each of these repositories.  This is necessary because our Global policy has a Default label of **General**.  If we did not add this switch, any file that did not match a condition would be labeled as General when we do the enforced scan.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/00niixfd.jpg)
1. To verify the repositories configured, run the command below.

    ```
    Get-AIPScannerRepository
    ```
	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/n5hj5e7j.jpg)



# Running Sensitive Data Discovery

1. Run the commands below to run a discovery cycle.

    ```
	Set-AIPScannerConfiguration -DiscoverInformationTypes All
	```
	```
	Start-AIPScan
    ```

	> INFO: Note that we used the DiscoverInformationTypes -All switch before starting the scan.  This causes the scanner to use any custom conditions that you have specified for labels in the Azure Information Protection policy, and the list of information types that are available to specify for labels in the Azure Information Protection policy.  Although the scanner will discover documents to classify, it will not do so because the default configuration for the scanner is Discover only mode.

1. Right-click on the **Windows** button in the lower left-hand corner and click on **Event Viewer**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cjvmhaf0.jpg)
1. Expand **Application and Services Logs** and click on **Azure Information Protection**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/dy6mnnpv.jpg)

	>NOTE: You will see an event like the one below when the scanner completes the cycle.
	>
	>![o46iabfu.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/o46iabfu.jpg)

2. Next, switch to Client3 and browse to \\\sql1\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports and review the summary txt and detailed csv files available there.

	>NOTE: Since there are no Automatic conditions configured yet, the scanner found no matches for the 100 files scanned despite 96 of them having sensitive data.
	>
	>![m79emvr8.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/m79emvr8.jpg)
	>
	>The details contained in the DetailedReport.csv can be used to identify the types of sensitive data you need to create AIP rules for in the Azure Portal.
	>
	>![9y52ab7u.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/9y52ab7u.jpg)



# Configuring Automatic Conditions

Now that we know what types of sensitive data we need to protect, we will configure some automatic conditions (rules) that the scanner can use to classify and protect content.

1. Switch back to SQL1 and open the browser that is logged into the Azure Portal.

1. Under **classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/jyw5vrit.jpg)
1. In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	![cws1ptfd.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/cws1ptfd.jpg)
1. In the Condition blade, in the **Select information types** search box, type **SSN** and check the box next to the 3 **Social Security Number** entries.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/a6dfnuyz.jpg)
1. Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/41o5ql2y.jpg)
1. In the Label : Contoso Internal blade, under **Select how this label is applied: automatically or recommended to user**, click **Automatic**.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/1ifaer4l.jpg)

1. Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/rimezmh1.jpg)
1. Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.



# Enforcing Configured Rules

In this task, we will se the AIP scanner to enforce the conditions we set up in the previous task and have it rerun on all files using the Start-AIPScan -Reset command.

1. Run the commands below to run an enforced scan using defined policy.

    ```
	Set-AIPScannerConfiguration -Enforce On -DiscoverInformationTypes PolicyOnly
	```
	```
	Start-AIPScan -Reset
    ```

	> NOTE: Note that this time we used the DiscoverInformationTypes -PolicyOnly switch before starting the scan. This will have the scanner only evaluate the conditions we have explicitly defined in conditions.  This increases the effeciency of the scanner and thus is much faster.  After reviewing the event log we will see the result of the enforced scan.
	>
	>![k3rox8ew.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/k3rox8ew.jpg)
	>
	>If we switch back to Client3 and look in the reports directory we opened previously, you will notice that the old scan reports are zipped in the directory and only the most recent results aare showing.
	>
	>![s8mn092f.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/s8mn092f.jpg)
	>
	>Also, the DetailedReport.csv now shows the files that were protected.
	>
	>
	>![6waou5x3.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/6waou5x3.jpg)
	>
	>![Open Fullscreen](6waou5x3.jpg)



# Reviewing Protected Documents

Now that we have Classified and Protected documents using the scanner, we can review the documents we looked at previously to see their change in status.

1. Switch to SQL1 and (if necessary) log in using the Install account with a password of **Somepass1**.

1. Navigate to ***http://sp1/documents***. Provide the credentials **Install** and **Somepass1** if prompted.

	![Open Screenshot](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/hipavcx6.jpg)
1. Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets.



	> NOTE: Observe that the same document is now classified as Confidential \ Contoso Internal. You can also see these same documents on a file share at \\fileserver\documents in their new protected state.
	>
	>![s1okfpwu.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/s1okfpwu.jpg)




# CONGRATULATIONS!

![kt7yaogd.jpg](https://github.com/kemckinnmsft/AIPLAB/blob/master/Content/kt7yaogd.jpg)
