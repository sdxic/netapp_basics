# NetApp OnTap Basics
In this lab you will setup a new NetApp Cluster and perform routine storage administrative duties. While the core cluster is already in place, your responsibilities include generating new storage virtual machines, volumes, and shares. This lab caters to individuals without prior NetApp experience, utilizing the System Manager web interface for task execution

<br><br>

# Lab Activities
* Part 1: Initial Setup Activities
* Part 2: SMB Storage Provisioning
* Part 3: NFS Storage Provisioning
* Part 4: Routine Storage Administrative Tasks

<br>

# Part 1: Initial Setup Activities
1. On the lab jumpbox, launch the NTAP shortcut.  This shortcut will take you to the NetApp System Manager web interface.
<br>![](images/part1_step1a.png)

2. You will get a "Your connection is not private" message, select advanced and then click Proceed.
<br>![](images/part1_step2a.png)

3. Input your credentials and click Sign In.
    <br>
    * Username - admin
    <br>
    * Password - SDNlab01@
    <br>
4. Upon successful login you will be directed to the System Manager Dashboard.  The Dashboard provides a overview of the cluster's overall health and configuration.  You will notice the controller model, in this case it's virtual, capacity, system performance and network details.  Currently these panels don't contain any information as we haven't started the setup process.
<br>![](images/part1_step4a.png)

5. One of the first tasks we will perform is Preparing Storage.  When we prepare the storage it is going to create aggregates/local tiers.  We will store virtual machine and user data in the local tiers.
    <br>
      -  A) Select Prepare Storage from the dashboard.
    <br>
      -  B) Within the Prepare Storage screen it will ask if you want to enable software encryption.  For the purposes of this lab we will not enable encryption.  Select Continue without a key manager and select Prepare storage.  This process could take a few minutes.
    <br>![](images/part1_step5b.png)
    <br>
      -  C) You will be redirected to the dashboard and you should be able to see available capacity in the capacity panel.
    <br>![](images/part1_step5c.png)
    <br><br>
# Part 2: SMB Storage Provisioning
1. We will create our first storage virtual machine/SVM.  The SVM is a logical storage container that runs within the cluster.  Each SVM has it's own set of network interfaces, volumes, LUNS and protocols.  By creating logical storage containers you are able to segment protocols, departments and active directory domains.
    <br>
      -  A) From the Dashboard select Storage, and then select Storage VMs.
    <br>
      -  B) From the Storage VMs screen we will select Add.
        <br>![](images/part2_step1b.png)
    <br>
      -  C) A new panel will open with the title of Add storage VM.  We will give the Storage VM a name and configure protocols.
    <br>
            * Storage VM Name - fileserver
    <br>
            * Access protocol - Enable SMB/CIFS
    <br>
      -  D) Once you select Enable SMB/CIFS a sub dialog box will open.  You will need to input your Active Directory information.  This SVM will be joined to the domain with a computer object.
    <br>
            * Administrator Name - Administrator
    <br>
            * Password - SDNlab01@
    <br>
            * Server Name - fileserver
    <br>
            * Active Directory Domain - This will be the domain your lab is configured with.  You can get this information by opening a command prompt and typing ipconfig /all.  The DNS Suffix Search List is the Active Directory Domain you will input. 
    <br>
        <br>![](images/part2_step1d1.png)
    <br>
            * We will leave the Organizational Unit defaulted to CN=Computers
    <br>
            * Domains - This will be the same Active Directory Domain name you gathered before
    <br>
            * Name Servers - You can get this IP address from the ipconfig /all output from before.  Copy of the value of DNS Servers.
    <br>
            * IP address - You will use the first 3 octets of the DNS server.  The last octect will be .62.
    <br>
            * Subnet Mask - Should auto populate, if not click off the IP address field.
    <br>
            * Gateway - Should auto populate, if not click off the IP address field.
    <br>
            * Broadcast Domain and Port - Default
        <br>![](images/part2_step1d2.png)
    <br>
      -  E) Verify all of the data is correct and select Save.  If everything was put in correctly the new fileserver will be created and it will take you back to the Storage VMs page.  At this point you've created a new SVM and joined it to the domain. This new SVM has been configured to serve the SMB/CIFS protocol.  
    <br>
2. Now you will create a new volume and network share that users can access and write files to. 
    <br>
      -  A) Within System Manager select Storage and then Volumes
    <br>
      -  B) Select the Add button to create a new volume with the following details
    <br>
            * Name - it_dept
    <br>
            * Size - 10 GiB
    <br>
            * Share via SMB/CIFS - Make sure this box is selected
    <br>
            * Select the Save button
    <br>
 <br>![](images/part2_step2b.png)
    <br>
            * Once the volume is created it you will be back at the volumes screen with the new it_dept volume.
    <br>
      -  C) Review the share that was automatically created for you by selecting Storage and then Shares from the System Manager interface.  Notice how it created a it_dept share for you? Click on the share and exam the details.
    <br>
 <br>![](images/part2_step2c.png)
    <br>
      -  D) Map a network drive to the share we created and write a file to it.
    <br>
            * Select the windows folder icon on the taskbar.
    <br>
            * On the top of the windows explorer window, select computer and then map network drive and select map network drive.
    <br>
            * In the Map Network Drive put in the following values.
    <br>        Drive - Z:
    <br>
                Folder - \\\fileserver\it_dept
    <br>
                Leave Reconnect at sign-in selected
    <br>
                Select Finish
 <br>![](images/part2_step2d1.png)
    <br>
            * A new windows explorer window will open and you should see the it_dept share we created.
    <br>
            * Right click anywhere in blank area of the share and create a new text document.  Input some random text and verify that you an save it.
    <br>
            * Go back to windows explorer, click on This PC on the left hand side.  Review the size of the drive we mapped.
    <br><br>
# Part 3: NFS Storage Provisioning
1. We will create our second storage virtual machine/SVM and configure it with NFS.  We will create a volume on the SVM and present it as a datastore to VMware.
    <br>
      -  A) From the Dashboard select Storage, and then select Storage VMs.
    <br>
      -  B) From the Storage VMs screen we will select Add.
    <br>
      -  C) A new panel will open with the title of Add storage VM.  We will give the Storage VM a name and configure protocols.
    <br>
            * Storage VM Name - nfsserver
    <br>
            * Access protocol - Enable NFS
    <br>
            * Allow NFS client access - make sure this box is checked
    <br>
            * Export Policy Default - Under rules select Add
    <br>
            * A New Rule window will open
    <br>
            * Under Client Specification input  - 0.0.0.0/0
    <br>
            * Access Protocols - Deselect SMB/CIFS and FlexCache, leave NFS selected
    <br>
            * Access Details - Select all for Read-only access, Read/write access and Superuser access and select save
 <br>![](images/part3_step1c1.png)
    <br>
            * Default Language - Leave to the default of c.utf_8
    <br>
            * IP Address - You will use the first 3 octets of the DNS server.  The last octect will be .63
    <br>
            * Subnet Mask - Should auto populate, if not click off the IP address field
    <br>
            * Gateway - Should auto populate, if not click off the IP address field.
    <br>
            * Broadcast Domain and Port - Default
    <br>
      -  E) Verify all of the data is correct and select Save.  If everything was put in correctly the new nfs server will be created and it will take you back to the Storage VMs page.  At this point you've created a new SVM with the NFS protocol.
 <br>![](images/part3_step1c2.png)    
    <br>
2. Now you will create a new volume and network share that we will mount to VMware as an NFS datastore. 
    <br>
      -  A) Within System Manager select Storage and then Volumes
    <br>
      -  B) Select the Add button to create a new volume with the following details
    <br>
            * Name - datastore
    <br>
            * Size - 100 GiB
    <br>
            * Storage VM - nfsserver
    <br>
            * Export via NFS - Make sure this box is selected
    <br>
            * Select the Save button
    <br>
 <br>![](images/part3_step2b1.png)
    <br>
      -  C) Go back to the desktop on your jumpbox.  You will see a shortcut to vCenter.
    <br>
            * You will get a "Your connection is not private" message, select advanced and then click Proceed.
    <br>
            * Select Launch Vsphere Client (HTML5)
    <br>
            * Username - administrator@vsphere.local
    <br>
            * Password - SDNlab01@
    <br>
            * Select the Login button
    <br>
 <br>![](images/part3_step2c1.png)
    <br>
            * On the left hand side expand vcenter, expand DC and then expand CL.
    <br>
            * Right click on the cluster name of CL, select Storage and then select New Datastore.
 <br>![](images/part3_step2c2.png)
    <br>
            * In the New Datastore window we will select a type of NFS and select next.
 <br>![](images/part3_step2c3.png)
    <br>
            * NFS Version - NFS 3, select Next
    <br>
            * Name - datastore
    <br>
            * Folder - /datastore, remember the case is sensitive here.
    <br>
            * Server - X.X.X.63, You will use the first 3 octets of the DNS server. Select Next
 <br>![](images/part3_step2c4.png)
    <br>
            * Select both ESX hosts and select next.
    <br>
 <br>![](images/part3_step2c5.png)
    <br>
            * Validate the data you put in and select Finish.
    <br>
            * You should now see a datastore called datastore in your vCenter browser.  You can confirm this by clicking on the datastore icon in the top left.  Expand the DC datacenter and verify datastore shows below.
    <br>
 <br>![](images/part3_step2c6.png)
    <br>
            * This datastore would now be ready for productive use.  You could create virtual machines, folders and ISO's in this datastore.  We won't be creating any virtual machines in this lab.
    <br><br>
# Part 4: Routine Storage Administrative Tasks
1. In this lab we will walk through some routine storage administrative tasks.  These are common tasks you can expect to perform while administering a NetApp Storage System.
    <br>
      -  A) One request that will come in is to increase space on a shared network drive.  In lab 2 we created a volume called it_dept and gave it 10GiB of capacity.  Let's walk through increasing the capacity on this volume/share.
    <br>
            * Open the NetApp System Manager web interface, you might have to log back in.
    <br>
            * Select Storage and then volumes.
    <br>
            * Hover over the it_dept volume and click the 3 dots to the far right.
    <br>
 <br>![](images/part4_step1a1.png)
    <br>
            * Select the Resize option.
    <br>
            * Change the Capacity to 20GiB and select Save.  Notice how it's reserving 5% of space for snapshots?  
    <br>
            * Confirm the Capacity has changed within the system manager interface.
    <br>
            * Let's confirm that the shared network drive is also showing the correct space.
    <br>
            * Open your windows file explorer and select This PC from the left hand menu.
    <br>
            * Verify that share it_dept under network locations is showing the correct size.
    <br>
 <br>![](images/part4_step1a2.png)
    <br>
      -  B) Another request that will come in is to restore a file from a snapshot.  Let's go back out to our network share and restore a file from a NetApp snapshot.
    <br>
            * Open the NetApp System Manager web interface.
    <br>
            * Select Storage and then volumes.
    <br>
            * Select the it_dept volume.  You will notice it brings up another tile in the left hand side.
    <br>
            * Select the Snapshot copies tab and review the contents.
    <br>
            * Click on Add in the snapshot copies tab.
    <br>
            * Under Snapshot Copy Name give it a name of test and select Add.
    <br>
 <br>![](images/part4_step1b1.png)
    <br>
            * Verify you see the new snapshot copy name of test.
    <br>
            * Now we are going to open windows file explorer.
    <br>
            * Click on This PC
    <br>
            * Double click the it_dept share we mapped earlier.  
    <br>
            * Right click the test.txt document we created earlier and delete it. On the delete file confirmation screen choose yes.
 <br>![](images/part4_step1b2.png)
    <br>
            * Let's restore the file we just deleted from a snapshot.  Right click on any open space in the it_dept network drive.
    <br>
            * Select Properties and then go to the previous versions tab.  Notice the snapshots listed in here?
    <br>
            * Let's select the latest snapshot, which should be the first one listed.  
    <br>
            * Towards the bottom of the screen you will select open.  A new window will open up showing the text.txt file we previously deleted.
    <br>
            * Drage the text.txt file from the restore directory back to the it_dept directory.
 <br>![](images/part4_step1b3.png)
    <br>
            * Verify you can open the file from the it_dept directory and make changes to it.
    <br>
      -  C) Another request that will come in is to create a new share.  For this purpose we are going to create a new share from the existing it_dept volume we created earlier.  
    <br>
            * Open windows file explorer.
    <br>
            * Click on This PC  
    <br>
            * Double click the it_dept share we mapped earlier.  
    <br>
            * Right click anywhere in the open space and choose New and then Folder
    <br>
            * Give the folder a name of new_share  
 <br>![](images/part4_step1c1.png)
    <br>
            * Open the NetApp System Manager web interface.
    <br>
            * Select Storage and then shares.
    <br>
            * Select Add and then select Share
    <br>
            * Share Name - New_Share
    <br>
            * Under the folder name click Browse
    <br>
            * Double click the it_dept folder and then select new_share, select Save
    <br>
            * Under Access Permission select +Add, leave the default of Everyone Full Control
    <br>
            * Select the Save button
 <br>![](images/part4_step1c2.png)
    <br>
            * Verify you can see the new share by opening windows file explorer.
    <br>
            * In the path put in \\\fileserver, verify that New_Share is visable.
 <br>![](images/part4_step1c3.png)
    <br>
            * Doubleclick the New_Share and verify you can write a new text document in it.
    <br>
      -  D) Now we are going to focus on the NFS datastore we provisioned for VMware.  In lab 3 we created a datastore for VMware that's 100Gib in size.  We have a VMware administrator asking us to increase the capacity on that datastore. 
    <br>
            * Open the VMware vCenter web interface, you might have to log back in.
    <br>
            * Select the datastore icon at top left of the screen, expand DC and select datastore.
    <br>
            * Verify that the current capacity shows as 100GiB.
 <br>![](images/part4_step1d1.png)
    <br>
            * Open the NetApp System Manager web interface, you might have to log back in.
    <br>
            * Select Storage and then volumes.
    <br>
            * Hover over the datastore volume and click the 3 dots to the far right.
    <br>
 <br>![](images/part4_step1d2.png)
    <br>
            * Select the Resize option.
    <br>
            * Change the Capacity to 200GiB and select Save.  Notice how it's reserving 5% of space for snapshots?  
    <br>
            * Confirm the Capacity has changed within the system manager interface.
    <br>
            * Let's confirm that the VMware datastore is seeing the new capacity.
    <br>
            * Open the VMware vCenter web interface.
    <br>
            * Select the datastore icon at top left of the screen, expand DC and select datastore.
    <br>
            * On the far righthand side of the screen click on Refresh under capacity.
    <br>
            * Verify that the datastore capacity now shows as 190Gib.
 <br>![](images/part4_step1d3.png)
