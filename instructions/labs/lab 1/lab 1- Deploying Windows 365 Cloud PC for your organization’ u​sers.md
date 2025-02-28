# Lab 1 - Preparing the Lab Environment

## Task 1 - Create a new Forest and Domain for On-Premises setup

1.  Open the Edge Browser and login to **Azure
    Portal** [**https://portal.azure.com**](urn:gd:lg:a:send-vm-keys) using
    the Office 365 Tenant credentials from the home tab of Lab
    interface.

> ![A blue triangle with black text AI-generated content may be
> incorrect.](./media/image1.png)
>
> ![](./media/image2.png)

2.  Click on the **Portal Menu** then select **+ Create a resource**

> ![](./media/image3.png)

3.  In the search type [**template**](urn:gd:lg:a:send-vm-keys) and
    select **Template deployment**

![Screenshot](./media/image4.png)

4.  On the **Template deployment (deploy using custom templates)** page
    click on **Create**

![Screenshot](./media/image5.png)

5.  In the **Quickstart template** search **new-domain** and
    select **application-workloads/active-directory/active-directory-new-domain
    template**

![Screenshot](./media/image6.png)

6.  Click on **Select template**

![Screenshot](./media/image7.png)

7.  On the **Custom deployment** page provide the below details

a\. Subscription -- **Azure Pass -- Sponsorship**

b\. Resource group -- Create new
-- [**Rg4OnPremDC**](urn:gd:lg:a:send-vm-keys)

c\. Region -- **East US**

d\. Admin Username -- [**adminuser**](urn:gd:lg:a:send-vm-keys)

e\. Admin Password -- [**demo@pass123**](urn:gd:lg:a:send-vm-keys)

f\. Domain Name
-- [**cdomxxxxxx.local**](urn:gd:lg:a:send-vm-keys) \[substitute **xxxxxx** with
random number\]

g\. DNS prefix
- [**cdomxxxxxx**](urn:gd:lg:a:send-vm-keys) \[substitute **xxxxxx** with
random number as above\]

h\. Keep the remaining settings as default and then click on **Review +
create**

![Screenshot](./media/image8.png)

8.  Click on **Create** to start the deployment

![Screenshot](./media/image9.png)

9.  Wait for the deployment to complete, it will take around 12-15
    minutes to complete the deployment

10. Once the Deployment is competed click on **Go to resource group**

> ![](./media/image10.png)

11. It will open the Resource group page where all the resources have
    been created by the deployment

> ![](./media/image11.png)

## Task 2 -- Login to the Domain controller and create resources for testing

1.  From the **Rg4OnPremDC** Resource group page, click on
    the **adVM** virtual machine

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image12.png)

2.  On the **adVM** page click on Connect.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image13.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image14.png)

3.  Ensure to change the IP address to **Load balancer DNS name**, then
    click on **Download RDP file**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image15.png)
>
> ![](./media/image16.png)

4.  Click on **Open file** to connect to the VM, when prompted enter the
    password demo@pass123

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image17.png)

5.  The Server Manager will be launched automatically, click
    on **Tools**, then select **Active Directory Users and Computers**

> ![A computer screen shot of a computer AI-generated content may be
> incorrect.](./media/image18.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image19.png)

6.  We will create 2 Organizational Units (OUs)

> a. **Cloud PCs** -- **Required to the Azure Network connection for
> Hybrid Azure AD Join**
>
> b. **Cloud PC Users** -- Test accounts for testing Windows 365 Cloud
> PC

7.  **Right-click** on the Domain name then select **New**, then click
    on **Organizational Unit**

> ![](./media/image20.png)

8.  For the OU Name enter [**Cloud PCs**](urn:gd:lg:a:send-vm-keys) then
    click **OK**

> ![](./media/image21.png)

9.  **Right-click** on the Domain name then select **New**, then click
    on **Organizational Unit**

> ![](./media/image20.png)

10. For the OU Name enter **Cloud PC Users** then click **OK**

> ![](./media/image22.png)

11. Right-click on the newly create OU **Cloud PC Users**, then
    select **New** and then click on **User**

> ![](./media/image23.png)

12. On the **New Object -- User** window provide the below details

> a\. First name -- [**Test**](urn:gd:lg:a:send-vm-keys)
>
> b\. Last name -- [**User1**](urn:gd:lg:a:send-vm-keys)
>
> c\. User logon name -- [**testuser1**](urn:gd:lg:a:send-vm-keys)
>
> d\. Click on **Next**
>
> ![](./media/image24.png)

13. On the Password details provide the below details

> a\. Password -- [**demo@pass123**](urn:gd:lg:a:send-vm-keys)
>
> b\. Confirm password - [**demo@pass123**](urn:gd:lg:a:send-vm-keys)
>
> c\. Only select the checkbox for **Password never expire**
>
> d\. Click on **Next**

![A computer screen with a login box and a window AI-generated content
may be incorrect.](./media/image25.png)

**Note** -- This Password never expire setting is only for Lab
environment, it is recommended to keep User must change password at next
logon.

14. Click on **Finish** to complete the User creation

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image26.png)

15. In the **Active Directory Users and Computers**, select
    the **View** menu and click on **Advanced features**

> ![](./media/image27.png)
>
> **Note** -- The Advanced features setting will enable us to see
> additional details for the all Objects in AD

16. Right-click on the **Cloud PCs** OU then select **Properties**

> ![](./media/image28.png)

17. Select the **Attribute Editor** tab, then open
    the **distinguishedName** attribute and copy the Value into
    a **Notepad** file as it would be required in Later.

> ![](./media/image29.png)

18. Save the information as **DNforOU.txt** on the Desktop

> ![](./media/image30.png)

## Task 3 -- Install Azure AD connect and setup PHS and Hybrid Azure AD Join

1.  Open Windows PowerShell (admin) and run the following script in the
    virtual machine

> If (-Not (Test-Path
> 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\\NETFramework\v4.0.30319'))
>
> {
>
> New-Item
> 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\\NETFramework\v4.0.30319' -Force
> | Out-Null
>
> }
>
> New-ItemProperty -Path
> 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\\NETFramework\v4.0.30319' -Name
> 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> New-ItemProperty -Path
> 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\\NETFramework\v4.0.30319' -Name
> 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> If (-Not (Test-Path
> 'HKLM:\SOFTWARE\Microsoft\\NETFramework\v4.0.30319'))
>
> {
>
> New-Item 'HKLM:\SOFTWARE\Microsoft\\NETFramework\v4.0.30319' -Force |
> Out-Null
>
> }
>
> New-ItemProperty -Path
> 'HKLM:\SOFTWARE\Microsoft\\NETFramework\v4.0.30319' -Name
> 'SystemDefaultTlsVersions' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> New-ItemProperty -Path
> 'HKLM:\SOFTWARE\Microsoft\\NETFramework\v4.0.30319' -Name
> 'SchUseStrongCrypto' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> If (-Not (Test-Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Server'))
>
> {
>
> New-Item
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Server' -Force | Out-Null
>
> }
>
> New-ItemProperty -Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Server' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> New-ItemProperty -Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Server' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord'
> -Force | Out-Null
>
> If (-Not (Test-Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Client'))
>
> {
>
> New-Item
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Client' -Force | Out-Null
>
> }
>
> New-ItemProperty -Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Client' -Name 'Enabled' -Value '1' -PropertyType 'DWord' -Force |
> Out-Null
>
> New-ItemProperty -Path
> 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS
> 1.2\Client' -Name 'DisabledByDefault' -Value '0' -PropertyType 'DWord'
> -Force | Out-Null
>
> Write-Host 'TLS 1.2 has been enabled. You must restart the Windows
> Server for the changes to take affect.' -ForegroundColor Cyan
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image31.png)
>
> ![](./media/image32.png)

2.  Open the browser on the **adVM** and download **Azure AD
    connect** from the
    link [**https://www.microsoft.com/en-ie/download/details.aspx?id=47594**](https://www.microsoft.com/en-ie/download/details.aspx?id=47594)

3.  Click **Download** and run the application.

> ![](./media/image33.png)

4.  Once installed, the AD Connect configuration will start. Enable the
    checkbox for **I agree to the license terms and privacy notice** and
    click **Continue**.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image34.png)

5.  On the Express Settings page click **Customize**.

> ![](./media/image35.png)

6.  On the Install required components page click **Install**.

> ![](./media/image36.png)

7.  At the User sign-in page, choose **Password Hash
    Synchronization** as the Sign On method and enable the checkbox
    for **Enable single sign-on** then click on **Next**.

> ![](./media/image37.png)

8.  On the **Connect to Microsoft Entra ID** page, enter the **Office
    365 Tenant credentials** from the home tab of Lab interface.

> ![](./media/image38.png)
>
> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image39.png)

9.  On the connect your directories page click **Add Directory**.

> ![](./media/image40.png)

10. On the **AD Forest account** window click Create new account and
    enter the following credentials and click OK:

    - Domain
      Username: [**CDOMxxxxxx\adminuser**](urn:gd:lg:a:send-vm-keys) \[xxxxxx
      will be as per the domain specified by you in task 1\]

    - Password: [**demo@pass123**](urn:gd:lg:a:send-vm-keys)

11. Click on **Next** once the CONFIGURED DIRECTORIES is populated.

> ![](./media/image41.png)

12. On the **Azure AD sign in configuration** page click **Continue
    without matching all UPN suffixes to verified domains** check box
    and click **Next**.

> ![](./media/image42.png)

13. On the **Domain and OU filtering** page click on **Sync selected
    domains and OUs**, then choose only **Cloud PC Users** and **Cloud
    PCs**, then click on **Next**.

> ![](./media/image43.png)

14. On the **Uniquely identifying your users** page click **Next**.

> ![](./media/image44.png)

15. On the **Filter users and devices** page click **Next**.

> ![](./media/image45.png)

16. On the **Optional features** page click **Next**.

> ![](./media/image46.png)

17. On the **Enable single sign-on** page, click on **Enter
    credentials**

> ![](./media/image47.png)

18. On the **Forest credentials** prompt enter the below credentials and
    click **OK**

    - Username -- [**adminuser**](urn:gd:lg:a:send-vm-keys)

    - Password -- [**demo@pass123**](urn:gd:lg:a:send-vm-keys)

> ![](./media/image48.png)

19. Click on **Next**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image49.png)

20. On the **Ready to configure** page click **Install**. This process
    will take around 5-8 minutes to complete.

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image50.png)

21. Once the installation is complete click **Exit**.

> ![](./media/image51.png)

22. Launch the **Azure AD connect** from the Desktop Icon again.

> ![](./media/image52.png)

23. On the Welcome screen, click on Configure

> ![](./media/image53.png)

24. On the **Additional tasks** page, select **Configure device
    options** then click on **Next**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image54.png)

25. On the **Overview** page click on **Next**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image55.png)

26. On the **Connect to Microsoft Entra ID** page, enter the **Office
    365 Tenant credentials** from the home tab of Lab interface and
    click on **Next**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image56.png)

27. On the **Device options** page, select **Configure Hybrid Microsoft
    Entra ID join** radio button and then click on **Next**

> ![](./media/image57.png)

28. On the **Device operating systems** page, enable both the checkboxes
    and then click on **Next**

> ![A screenshot of a computer AI-generated content may be
> incorrect.](./media/image58.png)

29. On the **SCP configuration** page, enable the check box for the
    Forest, then select **Microsoft Entra ID** then click on
    the **Add** button

> ![](./media/image59.png)

30. When prompted for Enterprise Admin Credentials, enter the below
    credentials and then click OK

    - Username -- [**adminuser**](urn:gd:lg:a:send-vm-keys)

    - Passwor\`d -- [**demo@pass123**](urn:gd:lg:a:send-vm-keys)

> ![](./media/image60.png)

31. Click on **Next**

> ![](./media/image61.png)

32. On the **Ready to configure** page click on the **Configure** button

> ![](./media/image62.png)

33. On the **Configuration complete** page, click on the link to review
    the article, then click on **Exit** button

> ![](./media/image63.png)
>
> End this Lab
