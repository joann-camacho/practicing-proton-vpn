<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Inspecting Traffic Between Azure Virtual Machines and Network Security Groups (NSGs) </h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark and experiment with Network Security Groups. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (22H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

 ***A- Prereq: Create a Windows VM and Ubuntu (Linux) VM. Then, remote login to Windows VM*** 
  
- Step 1: Install [Wireshark](https://www.wireshark.org) in the Windows VM to observe Packet tracing.
- Step 2: Attempt to ping Linux VM in Windows VM using Powershell. Observe the traffic in Wireshark
- Step 3: Verify the Windows VM MAC Address is the same.

 ***B- Configuring A Firewall Network Security Group***

- Step 1: Initiate a perpetual/non-stop ping from your Windows 10 VM to your Ubuntu VM
- Step 2: Open the Network Security Group your Ubuntu VM is using and disable incoming (inbound) ICMP traffic
- Step 3: Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity
- Step 4: Re-enable ICMP traffic for the Network Security Group your Ubuntu VM
- Step 5: Back in the Windows 10 VM, observe the ICMP traffic in WireShark and the command line Ping activity (should start working). After, stop the ping activity.

***C- SSH Network Protocol***

- Step 1: Start the Wireshark analyzer, in your Windows VM, and filter for SSH traffic only
- Step 2: From your Windows 10 VM, “SSH into” your Ubuntu Virtual Machine (via its private IP address)
- Step 3: 


<h2>Actions and Observations</h2>

<p>
Step 1: In the Windows VM, use the search engine, and visit [Wireshark](https://www.wireshark.org). Go to Downloads, and install Windows x64 Installer.
</p>
<p>
  
![image](https://github.com/user-attachments/assets/f004b1cf-c6b4-45ab-8775-9b223a2d65e0)
</p>

(After installing Wireshark licensing terms) Open Wireshark. Double-click on 'Ethernet' to view IP traffic. This traffic is the IP packets reciprocating in the background while using the computer. 
<p>
  
![image](https://github.com/user-attachments/assets/8a7ba9ea-4008-4fce-9bd1-5a96153ddd3c)  
</p>
<br />

<p>
Step 2: Obtain Ubuntu (linux) Private IP address from Azure. In Wireshark's search window, Type ICMP. Also, open Powershell and ping 10.1.0.5 (Ubuntu's Private IP address). Observe the connectivity between two separate operating systems, when inspecting this traffic notice the four requests and replies in the Powershell command and there are 8 requests and replies in the Wireshark analyzer. This is important to note when inspecting data packet units and sourcing their incoming and outgoing traffic.
</p>
<p>
  
![image](https://github.com/user-attachments/assets/6c90b2b5-70a6-4033-9307-de5dcaffcd57)
</p>
<br />

<p>
Step 3: In Wireshark, expand 'Ethernet II,Src' (Level 2 of the OSI model), and notice the MAC address given by Azure software. In Powershell type: 'ipconfig /all' to verify Windows VM MAC address are the same. This is important to analyze the source and destination MAC address it belongs to, which in this case Windows is the source.
</p>
<p>
  
![image](https://github.com/user-attachments/assets/e2fee6ee-7a54-4b28-ab0e-cfce5d2f8471)
  
</p>
<br />

**Configuring Firewall (NSG)**

 <p>
Step 1: (Continue on your Windows VM) In the Powershell command line, Type 'ping 10.1.0.5 -t'. To initiate a non-stop Ubuntu VM ping. Observe that the ping is perpetual or non-stop and you can also see in Wireshark the data is non-stop as well.
</p>
<p>
  
![image](https://github.com/user-attachments/assets/0fc2f695-d26e-4626-b6c6-5bd2c6ad63b4)
</p>

Step 2: In Azure select your Ubuntu VM. In the Overview section > Networking > Network Settings > Scroll down and Select: ubuntu-vm-linux-nsg.
![image](https://github.com/user-attachments/assets/130f4403-ea7c-4f00-bc51-ccf736c7ec9c)

  Select Settings on the left-hand side and select 'Inbound security rule'. I am attempting to set a rule to disable incoming (inbound) ICMP traffic. Select 'Add'. In 'Destination port range' change to an asterisk (*), since ICMP does not have a port number, the asterisk defaults to all ports. In 'Protocol' select ICMPv4. In 'Action' select Deny. In 'Priority' type: 290. This means that it will go before any other rules set for a higher number (for example SSH at 300).
<p>
  
![image](https://github.com/user-attachments/assets/dd05ff2e-0b6c-4a63-bb4a-11ad98cb8a5e)

![image](https://github.com/user-attachments/assets/76cb82c8-8e22-4fac-b0c1-92366be3905e)

notice back in the Windows VM - the ongoing ping timed out in Powershell AND only Request is shown in Wireshark. This means the NSG rule worked!

![image](https://github.com/user-attachments/assets/dc73c60a-bb90-4176-984d-63c5a6de4714)
</p>

Delete the NSG rule in Azure and notice the ongoing ping is now functioning again. 

![image](https://github.com/user-attachments/assets/603f320e-efa7-4298-a090-aad7eec8e56f)

![image](https://github.com/user-attachments/assets/e3cb8464-a5b8-43d8-b8e4-537b78754ae7)


</p>
<br />     
      
***C- SSH Network Protocol***

<p>
Step 1: In the Windows VM, start a packet capture using  [Wireshark](https://www.wireshark.org). Use the filter search and type: SSH.
</p>
<p>
 
![image](https://github.com/user-attachments/assets/723c962a-ae7d-4f8e-b7bc-865d9c3bfc83)  
</p>

(After installing Wireshark licensing terms) Open Wireshark. Double-click on 'Ethernet' to view IP traffic. This traffic is the IP packets reciprocating in the background while using the computer. 
<p>
  
![image](https://github.com/user-attachments/assets/8a7ba9ea-4008-4fce-9bd1-5a96153ddd3c)  
</p>
<br />

<p>
Step 2: In Powershell "SSH into" the Linux VM by typing: ssh labuser1@<private IP address>. (the private IP address must be the Linux VM, which is 10.1.0.5). Notice that communication (secured traffic) is observed on the Wireshark analyzer. Enter the Linux VM password you created. (When typing the password, no letter will appear for security reasons.) You will see that more traffic was analyzed in Wireshark. Once the password is validated and confirmed notice the green hostname is now the Linux VM name.
</p>
<p>
 
![image](https://github.com/user-attachments/assets/cffd25bf-8678-4188-b3ac-7b68ad2be7bb)
  
</p>
<br />

<p>
Step 3: Type: 'hostname', to verify the server. Type 'id', to verify the user name.  Type 'exit' to exit out of the Linus VM server. 
</p>
<p>
  
![image](https://github.com/user-attachments/assets/2e23ebd1-0da0-410c-accd-51af635320d4)
  
</p>
<br />

