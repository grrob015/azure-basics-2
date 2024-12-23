# azure-basics-2
Data Storage, Virtual Machines, NSGs (Network Security Groups), and Internet Protocols (Part 2).

## Connecting to our Virtual Machine

Connecting to a virtual machine for Remote Desktop (GUI over RDP 3389) will look different depending on what OS you're using. For Windows, the "Remote Desktop Connection" app should already be on your computer. On Mac, you will need to install "Microsoft Remote Desktop" from the app store. On linux, applications like RealNVC and XRDP exist. I am currently on Windows 10, so I will be using Remote Desktop Connection. Navigate to the resource of your Windows Virtual Machine and copy the **Public** IP address, putting it into your RDP connection application of choice, using the username we chose for it when we made it.

![1  connecting to vmwin](https://github.com/user-attachments/assets/c637cf84-ef03-451f-bd5f-2e6db7bb6d6d)

Input your password, accept the connection without a certificate, and click through the first-time-launch Windows options. Congratulations! You are now connected to a computer with no physical hardware exclusive to itself! Everything about it is being simulated by a much more powerful computer in one of Microsoft's server rooms.

![2  the remote desktop](https://github.com/user-attachments/assets/5e586f83-6a10-4ad8-a746-f7c240d09f79)

## Inspecting Network Traffic (ICMP/pinging)

To begin inspecting the network traffic going to and from our virtual machine, we will need to install Wireshark on our VM. The default options will be sufficient for our purposes, so just install with the wizard and then launch the application. (⚠️ **The Wireshark install might seem to "freeze".** When this happens, move the window tracking the install and accept the license agreement that popped up behind it.)

![3  installing wireshark](https://github.com/user-attachments/assets/267929c3-d14b-4e3f-93ae-0414a8f2e153)

Once you have Wireshark running, select the network capture with activity being registered by a line graph (most likely Ethernet) and then click the sharkfin icon in the upper left hand corner of the application.

![4  wireshark starting](https://github.com/user-attachments/assets/930cf34c-ac2e-4465-b1d0-0dac54dbb511)

Wireshark will start spitting data out very quickly at you. Each entry represents data being sent either to or from the virtual machine. That's a lot of things happening, and we're not even doing anything with the computer, just watching what's happening! In order to observe the traffic more slowly, let's see what happens when we ping the other VM in our virtual network. To set that up, add an "icmp" filter in Wireshark and apply it. The application should clear and stop sending in lots of packets too fast to read (but the "Packets" number in the very bottom bar should keep increasing). Open up a powershell next to it and find the **private** IP address of the Ubuntu virtual machine in your Azure resource (It will look something like 10.0.0.5, existing in a default private address space. You can look [here](https://datatracker.ietf.org/doc/html/rfc1918) (top of page 4) to see the official IETF documents about reserved private IP addresses.)

![5  ready to ping and observe](https://github.com/user-attachments/assets/87767b08-458b-450d-8a0f-98f72d59daf1)

Once you've gotten the private IP address, type `ping ip-address-here` and observe the traffic that Wireshark picks up on. You can click through the different individual packets and see the information sent, the source and destination IP addresses, the length, and the protocol used, amongst other things. We can see that the data being trasmitted from one computer to another is the payload `abcdefghijklmnopqrstuvwabcdefg hi`, since it's a meaningless ping and could be anything. Amusing!
<!-- The background changed between these two pictures because I had lunch and a very long walk and I couldn't find it again thanks to Windows 10 being weird. It's the same computer, on the same RDP session, even. -->

![6  after the ping](https://github.com/user-attachments/assets/286cbcb1-0018-4b44-a12f-59979a845f5f) 

## Inspecting Network Traffic (SSH)

Next, let's take a look at the traffic that Wireshark captures when we use SSH and operate our Ubuntu VM from inside our Windows VM. Change the filter on Wireshark to "ssh" and use type `ssh username@host_ip_address` into a PowerShell instance. "username" will be the username you chose for the user back when you created the virtual machine (⚠️ **NOT** the machine name that you see as the title of the resource in Azure), and "host_ip_address" will be the private IP address of the Ubuntu machine, since we're connecting from the same local network. If done correctly, the shell will ask for the password you chose, and you will be in the linux terminal once you input it!

![7  after sshing](https://github.com/user-attachments/assets/298e474c-6211-4e64-88d6-0a4934076d90)

Notice the "Displayed" number of packets at the bottom of the Wireshark window. If you just press one button in the PowerShell terminal, dozens of packets of information are sent across the virtual network to update the shell in the Ubuntu VM. Just pressing 'm' on my keyboard made my packets increase from 222 to 306 packets! That's a lot of information for just one letter. Unfortunately we can't do much with the Ubuntu VM, because it blocks all public internet traffic by default except for SSH, but things like your .bashrc file and PS1 variables can still be played with. Once you're ready to end the SSH, though, type `exit` into your terminal. You should find that you're back in the Windows shell.

## Adjusting Network Security Group Rules

Still logged into your Windows VM, change the Wireshark filter back to `icmp` and then type `ping -t ubuntu-private-ip-address` into your PowerShell, where "ubuntu-private-ip-address is the address that we used to ping it earlier. The `-t` flag makes the ping continuous at a constant rate, so we will have a consistent flow of traffic to manage with our network security group. Navigate to your Ubuntu VM in Azure and click "Network settings". 

![8  ubuntu network settings](https://github.com/user-attachments/assets/ea4acb80-bae3-4c82-bbeb-7863d78ede43)

Scroll down until you find the Network Security Group's rules.

![9  the network security group](https://github.com/user-attachments/assets/0aeb4a29-54d2-4376-a031-c504380b2261)

We're going to create a rule that denys all ICMP traffic. Click "Create port rule" and then "Inbound port rule" since we want to stop ICMP (pings) from reaching our Ubuntu machine. We want to deny ICMP traffic from any port.

![10  nsg blocking icmp](https://github.com/user-attachments/assets/094c38e5-ee0e-4970-a437-3fcd5988445b)

Going back to our Windows virtual machine, we can see from Wireshark that instead of data being sent back and forth from our two virtual machines, instead we have ping request after ping request timing out. All of the traffic is going one way and not being reciprocated.

![11  our requests time out](https://github.com/user-attachments/assets/36e3a9a4-9259-41c1-b7ad-2aa11652b5d4)

If we delete the network rule that we just created, we can see that the pattern of traffic changed back to what we saw before: a request and response of ping packets going back and forth.
<!-- Hopefully you know to click the trash can and don't need me to add another photo. -->

![12  on speaking terms again](https://github.com/user-attachments/assets/4b878d81-e69e-437b-8461-c508f4742715)
<!-- Funny file name :3 -->

That was a basic introduction to working with internet protocols, network security group rules, wireshark, virtual machines, and storage using Microsoft Azure! Thank you for reading this far.
<!-- And thanks for reading my silly comments. -->


