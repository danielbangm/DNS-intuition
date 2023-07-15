<p align="center">
<img src="https://rb.gy/so3qd" />
</p>

<h1>Domain Name Service - Building Intuition for DNS</h1>

In this tutorial I will use 2 Virtual Machines to do a few excercises with DNS, hopefully we build my intuition around how certain things with DNS work. Basically DNS just convert name(such as www.google.com) into IP addresses(8.8.8.8)

<h2>Objectives</h2>

-  More Experience with Azure (I will Create the environment here)
-  Inspect DNS A-records on the server(hostnames to IP addresses mapping)
-  Touch on "CNAME" records(Mapping one name to another name)
-  Gain a better understanding of Domain Name System.

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Virtual Machine(DC-1)
- Virtual Machine(client-1)
- Remote Desktop
- Active Directory

<h2>Operating Systems Used</h2>

-  Windows Server 2022
-  Windows 10 pro

<h2>List of Prerequisites</h2>

-  All you need for this lab is the free Azure Tenant and Subscription and do the previous <a href="https://github.com/danielbangm/Users-ad">Lab. </a>

<h2>Installation steps</h2>

-  Step 1: A-Record Exercise
For this lab, I will continue using the same Virtual Machines I built in the Active Directory lab. I will just log into both DC-1 and Client-1 as "jane_admin". From Client-1, I try to ping "mainframe" and it fails...
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/3c44038d-8696-41b2-a45d-11920c99e93a)

Explanation: When Client-1 tries to ping "mainframe" it first check its local CACHE to see if it has any record on that(obviously no) then send the request to the DNS(DC-1) IP address 10.0.0.4 and the result is still NO because "mainframe doesn't exist I just made it up. I also try to nslookup "mainframe" and there's no DNS record for that name.
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/93e7a91b-9ca1-4be6-8232-ee163691d3eb)

Now I Create a DNS A-record on DC-1 for “mainframe” and have it point to DC-1’s Private IP address 10.0.0.4. To do this, I go to Start -> Server Manager -> Tools -> DNS -> DC-1 ->Foward Lookup zone -> daniel.com  I manually create anothe A-record for the host named "mainframe" and set its IP address to 10.0.0.4
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/8e675636-4d96-4790-b5ef-d72af27ca676)

I go back to Client-1 and I am able tp ping "mainframe" successfully
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/5851acde-335a-4259-8f31-e8f8165ba62f)

I try nslookup mainframe and The dns DC-1.internal.cloudapp.net resolve that name(mainframe) into an IP address 10.0.0.4
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/1b675a54-b225-4c8f-901a-5bf2b3ff1ac0)

-  Setp 2: Local DNS Cache Exercise

To understand how the Local DNS Cache works, I go back to DC-1 and change mainframe’s record address to 8.8.8.8. Remember that previously its address was set to 10.0.0.4 Now I go back to Client-1 and ping “mainframe” again. Observe that it still pings the old address because the local cache still has the address 10.0.0.4 even though I assigned a new address(8.8.8.8). I do the command "Ipconfig /displaydns" and I can see that "mainframe still has the old address
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/7b525b78-de70-4a9b-8adf-867b5c258722)
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/4ee13b7f-eabc-404d-b087-bd1564875ebc)

To fix this issue, I just flush the local DNS cache using "ipconfig /flushdns" and i need to run this as an administrator
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/e2fba3ff-580b-4013-8491-2a78a10a1df1)

After flushing the dns, I ping "mainframe" again and now it successful with the correct address. Simple put, Client-1 cheched its local cache and its was empty because we just flushed it and asked the DNS for the latest record
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/e32bbb40-0adb-431e-8bc5-bbc01fe15ff3)

-  Step 3: C-NAME Records Exercise

I go back to DC-1 and create a CNAME record that points the host “search” to “www.google.com”
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/8fd2c075-7fa1-4e31-9a2c-dab2db36b9ad)

I go back to Client-1 and attempt to ping “search”, and nslookup search. Now observe the results of the CNAME record. I notice that "search" resolves to "www.google.com" which has this Ip address 172.253.115.104
![image](https://github.com/danielbangm/DNS-intuition/assets/22795502/8b7e2f86-e64b-4317-806d-68f099eaa64b)
