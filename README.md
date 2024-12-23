# azure-basics-2
Data Storage, Virtual Machines, NSGs (Network Security Groups), and Internet Protocols (Part 2).

## Connecting to our Virtual Machine

Connecting to a virtual machine for Remote Desktop (GUI over RDP 3389) will look different depending on what OS you're using. For Windows, the "Remote Desktop Connection" app should already be on your computer. On Mac, you will need to install "Microsoft Remote Desktop" from the app store. On linux, applications like RealNVC and XRDP exist. I am currently on Windows 10, so I will be using Remote Desktop Connection. Navigate to the resource of your Windows Virtual Machine and copy the **Public** IP address, putting it into your RDP connection application of choice, using the username we chose for it when we made it.

![1  connecting to vmwin](https://github.com/user-attachments/assets/c637cf84-ef03-451f-bd5f-2e6db7bb6d6d)

Input your password, accept the connection without a certificate, and click through the first-time-launch Windows options. Congratulations! You are now connected to a computer with no physical hardware exclusive to itself! Everything about it is being simulated by a much more powerful computer in one of Microsoft's server rooms.

![2  the remote desktop](https://github.com/user-attachments/assets/5e586f83-6a10-4ad8-a746-f7c240d09f79)

## Inspecting Network Traffic

To begin inspecting the network traffic going to and from our virtual machine, we will need to install Wireshark on our VM. The default options will be sufficient for our purposes, so just install with the wizard and then launch the application. (⚠️ **The Wireshark install might seem to "freeze".** When this happens, move the window tracking the install and accept the license agreement that popped up behind it.)

![3  installing wireshark](https://github.com/user-attachments/assets/267929c3-d14b-4e3f-93ae-0414a8f2e153)

Once you have Wireshark running, select the network capture with activity being registered by a line graph (most likely Ethernet) and then click the sharkfin icon in the upper left hand corner of the application.

![4  wireshark starting](https://github.com/user-attachments/assets/930cf34c-ac2e-4465-b1d0-0dac54dbb511)

Wireshark will start spitting data out very quickly at you. Each entry represents data being sent either to or from the virtual machine. That's a lot of things happening, and we're not even doing anything with the computer, just watching what's happening! In order to observe the traffic more slowly, let's see what happens when we ping the other VM in our virtual network. To set that up, add an "icmp" filter in Wireshark and apply it. The application should clear and stop sending in lots of packets too fast to read (but the "Packets" number in the very bottom bar should keep increasing). Open up a powershell next to it and find the **private** IP address of the Ubuntu virtual machine in your Azure resource.

![5  ready to ping and observe](https://github.com/user-attachments/assets/87767b08-458b-450d-8a0f-98f72d59daf1)





