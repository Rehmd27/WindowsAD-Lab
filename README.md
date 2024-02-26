# WindowsAD-Lab

Hello, my name is David and welcome to my very first project write up! The goal of this project is to build a Windows AD lab using Oracle VM VirtualBox. 
Our goals: Create DHCP
           Connect to DNS
           Utilize a Powershell Script to create users
           Create Windows 10 (Pro) VM
           Create a Domain Controller using two NIC's (internal & external)

Here is an image of the lab diagram. You can use this for reference when assigning IP's and to help keep you organized.
    ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/daa574f7-944e-4c91-bef6-fc296819253e)

Quick note about the 2 NIC's we will be using. The first NIC will connect to your home internet. The home router will handle the DHCP addressing while the internal NIC (172.16.0.1) you will assign yourself. We will cover this later in the write-up.

So....let's begin. I won't include videos about how to download Windows 2019 Server VM nor Windows 10 VM (there's enough videos on youtube just look it up real quick and follow their tutorial). The only note that I will include is install the VHD version and make sure that you select Windows 10 Pro. 

Now that you've set up your DC VM let's set it up. First thing that we will do is set up the Domain and Active Directory Domain Services. 
Click "Add roles and features" ->![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/91f3c3f7-cd87-4397-9fa0-d7c91b5a44cd)
 
next through everything -> 
You'll have only one server to select from -> 
Pick Active Directory Domain Services ->![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/e80d1947-d426-4d66-b244-1558acd8a104)

Click next 2 times until you have to select "Install" ->
Click the flag with the notification and select to promote the domain -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/9c78f3a8-1f19-405d-b74c-9227b5eda72e)
Fill out your window like the picture above and click next until you are left with only the install option


Next we will focus on building the Domain Admin account 
Go to Start and select Active Directory Users and Computers -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/ed6b3cda-e23d-4c85-8cd7-d6aa8dcf1b65)
Go to mydomain.com -> right click -> New Organizational Unit (make this for a place to put our new account)
After making the Organizational Unit, go to th Admin Folder and Click create new user -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/581cd6a4-a9af-478e-9be3-ecdf813f22b5)
Fill in the information -> Create Password -> Click Next and then Finish ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/f2decdd6-3b77-4541-882f-bea2cca1b806)
To make your new user admin, right click the user and go to properties ->
Go to the "member of" tab and click add, type "domain admins" in box click check names->
Click ok and apply
Sign out of the Windows Server VM and sign in again as "other user" and input your newly created admin user

Next we will add RAS (Remote Access Server)/NAT (Network Address Translation)
First, a RAS is defined as "a type of server that provides a suite of services to remotely connected users over a network or the internet." And a NAT is defined as "a technique to map an IP address space into another by modifying network address information in the IP header of packets while they are in transit across a traffic routing device." To keep it short and simple the RAS helps control stuff remotely while the NAT helps the private network communicate to the public network (think WAN). 

Now, to do this click "Add roles and features" -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/9d54890a-92e0-4d42-b1cc-c2fe9ee0a3a5)
Select the "Remote Access" -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/b93fe764-a068-495c-b1c2-bc9464332b3c)
Add routing (Direct Access and VPN will auto check themsleves after selecting to add routing) ->
Click next until you reach install and click install ->
After installation, click the tools at the top right and go to Routing and Remote Access on the drop down -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/697e062f-fa52-436e-bf5a-7945356a079f)
Go to configure and enable routing and remote access -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/341e3eb7-4f7c-4eee-afb2-d68d0aaf2fd1)
Pick NAT ->
Pick the internet option for NAT Internet connect ->
Finish through installation wizard and you'll end up with a screen like the one here -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/46fadd91-2295-47e1-976c-b75c462be493)


Next we will set up the DHCP server on DC and allow Windows client to get an IP address and browse the internet
First, click add roles from your main window on your Server 2019 VM ->
Next, click DHCP -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/e29592f2-25fd-4dc3-be8a-72cfc8efd2f2)
Click next until you get to install and finish the installation ->
Once installed, go to Tools again and pick the DHCP option from the dropdown -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/5d0ef335-d6fd-477f-a9b0-6229050dddce)
Select the IPv4, right click and pick "New Scope" -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/d6be03ee-d821-4bb4-a7d4-c2cc8769ea53)
Use the IP range from the diagram shown at the beginning for the scope name -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/53c8f437-9b2f-47f8-bc72-4632819a4049)
Fill out the IP address range like in the picture -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/252430fa-fd0b-40c6-9df0-69bee9773ae5)
Don't add exclusion, click next leave the lease duration set to default and click next on the following window
Use internal NIC from DC as your default gateway (router) -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/94c1bcc3-644d-409f-8a15-16817389b0d8)
Right click dc.mydomain and click "Authorize", right click again and select "refresh" ->
Now you should have green gumballs like in this picture -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/b12285c8-b164-4801-a34e-e9c64a0d9351)

It is now finally time for us to run our Powershell script to create new users...let's begin!
In your windows server VM, go to this page https://github.com/joshmadakor1/AD_PS/archive/master.zip ->
This link will download a file which you should save to your desktop for ease of use later ->
Open windows powershell ISE as admin -> 
Click "open" and navigate to the folder you just downloaded and click on the file "1_CREATE_USERS" -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/31c2b7e7-20b6-4892-8d2c-d2c4843291ec)
Type " Set-ExecutionPolicy Unrestricted" in your command line (this allows you to run the file, probably shouldn't do this in your work environment) ->
There'll be a pop up window asking if you want to allow this and select yes ->
Navigate from the command line to where the file is and go ahead an run the script -> ![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/fa9cbe11-4224-4754-8a14-1d07afbee655)
You'll see users being created on your command line. This helps you know that you did it correctly! -> 
You can verify that users were created if you go to your active directory folder and look at the users page ->

Now you can go ahead and create your Windows 10 VM. Like I said before, there's enough youtube videos out there you'll be able to go and get assistance if you require it.

Once you've started your Windows 10 VM go ahead and log in and confirm you're connected to the server. Just type "ipconfig" in the command line and you should see the default gateway reflect the same IP as the one listed in the diagram! You should also check for internet connectivity at this time as well so go and ping google.com for a quick test.

Last task, from your Server VM, go to DHCP -> IPv4 -> scope -> address lease and you'll be able to see your Windows machine. It should look something like this...![image](https://github.com/Rehmd27/WindowsAD-Lab/assets/158844780/4b73b615-eaec-4960-9a96-c60e802edd6d)

That concludes the lab! Thank you for following along and I hope this was a fun lab for you. Now you can practice using a windows server and implementing all sorts of documented best practices to help you become ready for a job. Follow me as I will continue posting tutorials.

















