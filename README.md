# Setting-up-a-VPN-using-OpenVPN-on-Raspberry-PI.

Creating a VPN on a raspberry pi to understand the technology better. Making a VPN server is not necessarily the best privacy solution, however playing with small tech in a safe manner is good for learning and when you learn new things you can be understand the technology better so you can make sure your privacy is protected. 

**PREFACE**

In 2019, I was travelling through Malaysian airport. I naively attempted to authenticate with the access point for their airport Wi-Fi, in a foreign country after a long flight I just wanted to see a familiar face or chat with someone back home. My attempt was unsuccessful so I went back to listening to music and waited for my flight.

This simple unsuccessful attempt at authentication was enough. Enough for PayPal to send me email notifications confirming there had been funds deposited to a blizzard account under my name. I tried but, I wasn’t able to log into my blizzard account. So I proceeded with forgot my password, luckily for me I had enabled MFA on the account, after gaining access back into the account I could see there were multiple transactions to a game on blizzard. On hindsight I was extremely close to losing my account completely, but I’m thankful for MFA. 

I reached out to PayPal and explained what had happened and they refunded my account. 
After the incident I thought to myself about things I could’ve done different to prevent this from happening again. I found VPN would’ve been a perfect solution. 
VPN services now days are sub services that allow you to connect to any of the service providers servers all across the world. Sometimes the ISP can cap your speed VPN can be used to bypass that, VPN also give you the freedom to evade geo-restriction. Few streaming services have implemented geo-restriction as common practice. 

Despite all this what is a VPN? How does it work? Let’s explore the topic and see if we can answer those questions.

**Scope:** Build a VPN Server using a Raspberry pi to understand the overall concept better.

**VPN**

Virtual Private Network allows you to access the internet with more privacy and adds another layer of security. It also allows you to get around geo-restriction, censorship, and content restrictions. 
There are a few explicit terms that are used technically when discussing VPN they might help illuminate what the whole technology a bit better:

- VPN Client – Software provided by the VPN service that is installed and used in the end users device to connect to the VPN servers.
- VPN Server – Other end of the VPN network. Where VPN client connects to can be another state or country.
- VPN Protocol – The communication method that is used by the Client and Server to encrypt traffic and communicate securely between each other.
- VPN Service – A service that allows end users to download VPN client software on their device and use their VPN servers across the world, it can be free or paid.



![Image](https://user-images.githubusercontent.com/107795542/179885049-028f150f-a7fb-40b1-94d1-3375736ca0fb.png)


 

Usage of VPN has been increasing rapidly around the world. Using a VPN has various pros mainly:
Security: Provides an extra layer of security when using public infrastructure, such as a café, restaurant, airport or other public Wi-Fi networks. These networks are riddled with malicious actors, therefore there is a high probability of user data getting stolen, intercepted or collected this is especially true for anyone using these infrastructure without encrypting their traffic.    
Privacy: VPN traffic is encrypted between end device and the server, this stops government bodies or corporations from collecting, or spying on end users internet usage. It also provides a degree of privacy from the ISP.

Unrestricted Access: As previously mentioned lots of paid streaming services provide different content on their platform but access to these contents are restricted based on geographic location of where the content is being accessed from. As VPN services have servers in many different locations around the world now days this allows for a greater flexibility and more freedom on what content streaming service costumers have access to. 
These days it’s possible to buy a cheap and reliable subscription to VPN services, but I find it easier to learn things if I personally do it myself. 

![image](https://user-images.githubusercontent.com/107795542/180112746-6c0e8d13-c9cb-461e-a4ff-424bbe754240.png)


**Hardware you need for the VPN**

1) Router that allows port forwarding
2) Raspberry pi 
3) SD card 16gb - Class 4+
4) Computer to download Raspberry pi OS and provision the SD card
5) Phone or Hotspot to test if the VPN works from a network outside your home network.

This is a sketch of what the whole infrastructure we'll be setting up.

![image](https://user-images.githubusercontent.com/107795542/180113237-c604efae-994d-4ee1-bf5e-fe511577b455.png)

This is a technical diagram of the same idea. 

![image](https://user-images.githubusercontent.com/107795542/180113384-2b0ab7cd-23ac-4efe-8e30-3a09adb51b87.png)
!!CRED DAN HENRICK!!


**Overview** 

Alright, here it is the down and dirty of how it all works.

The project is very unconventional in the sense that you'd never build this as a proper solution or implementation, as the pi wouldnt be able to handle substantial amount of throughput, and running a server yourself means youre relying on the public infrastructure such as your ISP and electric grid to function 100% of the time while your solution is up. (kinda unrealistic in terms of large scale implementation). 

It's just a little bit of configuration for a few different devices, all of these working together will allow us to establish our VPN and use it. 

We are building a WireGuard VPN server, it uses the OPENVPN protocol.

Most of the time ISP give us a dynamic IP (because of allocation maybe), static public is available to purchase but I believe they cost a bit more (I'll let you do your research on it).  We'll have our Raspberry Pi running a server and another additional service on the device these are essential to run because of our situation with ISP IP allocation.

When the server is established it spits out a QR code that we can use to connect to the server using an WireGuard app. 

However, the reason we need that extra service running on the RaspberryPi called DDClient is because it's a free Dynamic DNS resolution service. ISP might change your public address randomly but the Pi will know what the IP is which can communicate wiht the DDClient service and auto forward all the packets to the new IP address so our VPN connection from VPN client doesn't get severed. 
This is a rough sketch of the idea. (I like visuals to explain things it's easier)

![image](https://user-images.githubusercontent.com/107795542/180115088-9cbb10f5-074d-41f0-8d7c-5bf0aa3bbb0c.png)


**Router Configuration**

I have a DMZ functionality on my router but you maynot, which is fine it's just an extra layer of security. I've decided to use it, I feel like it's necessary but you be your own judge. 

Port forwarding means exactly what it sounds like, it's the router forwarding packets from a certain port to another port. And we use a UDP protocol as it's low latency, has smaller headers so it can create a tunnel with a higher bandwidth. TCP could theoritically work but it would be very expensive and unstable on the public infrastructure. 

Using routers port forwarding functionality we can forward all the packets on the specified port to another specified port which is in the DMZ so it goes to the Pi and nothing else. Thats why I optd for it. 

Here is a screen shot of what my router page looks like. Yours might look different.

![image](https://user-images.githubusercontent.com/107795542/180116786-b4dde332-3482-4073-8aff-fd65e7aea833.png)

After the routers DMZ and Port forward are configured, we need to configure our DNS domain online to deal with the pesky dynamic IP. (It's not like this in IRL we just have to do it because of the scale and solution we're setting up)

**SUBDomains**

As I mentioned previously ISP will change your IP whenever they want unless you pay for a static IP. Therefore we need a way to reach our PI from anywhere even if the IP address changes. So DDClient.

Let's talk about how that works with a free domain service I found. It's called freedomain.afraid, raspberry pi is running a service called DDclient which communicates to this free domain service online, and DDclient can allocate IP to the domain dynamically. This means we're not scared of the ISP changing the IP address. 

We need to make an account on the website and create a subdomain on freedomain, you can call it whatever I called mine Airbuffalo.mooo.com. 

![image](https://user-images.githubusercontent.com/107795542/180118874-b5424a13-f3dc-48bb-9d94-d25565a8a1ba.png)

So we have our subdomain, what does that actually mean?
Well the website/service freedomain is always up and its letting us host a free domain which is a part of its subdomain. This subdomain communicates to our Pi via the ddclient service so we can set its IP address dynamically to what we want. Which is our Public IP address everytime it changes or is changed by the ISP. So when we go to log into our VPN client app that reaches out to freedomain, gets our now new public ip from the subdomain and connects to the router which forwards the packets to raspberry pi.
 
That's why we need to configure so many things. Now that the two main steps are done we can move onto another relatively easy step. It's just provisioning the SD card with pi image so the Pi can boot off it and run.

**PIBooter**

I used rufus, I have been using it for years it's a free software you can download from here. https://rufus.ie/en/ 
It allows you to image a drive, all we need is a pi image from here. https://www.raspberrypi.com/software/

Provisioning a dirve is fairly simple just select the image you want loaded on to the disk/drive you want and let it do it's thing. I recommended using class 4+ SD card as it's faster and more reliable so might cut the provisioning time down and you wont have issues with Pi stabilty. I've faced those issues before when I had a generic SD card I ordered from Amazon. You have a huge range to choose from just make sure its above 9GB because I think thats how big the Pi image is.


**Software configuration**

Once the PI boots up make sure to run 

SUDO apt update && SUDO apt upgrade

To make sure there is no updates for the Pi.

Now install ddclient using

Sudo apt install ddclient

![image](https://user-images.githubusercontent.com/107795542/180121686-0dcdae2e-5cc7-48d2-aa3e-3148b1777c45.png)

Sudo systemctl start ddclient

and 

sudo systemctl enable ddclient 

to make sure that it starts and will start everytime the Pi boots up.

If you want to check if the service is running run this command

Sudo systemctl status ddclient

![image](https://user-images.githubusercontent.com/107795542/180121912-2a2b05ed-0575-4690-9170-98bc256dc979.png)


After thats done we can install the WireGuard Server 

wget https://git.io/wireguard -O wireguard-install.sh && sudo bash wireguard-install.sh

![image](https://user-images.githubusercontent.com/107795542/180122080-fd553d3e-10f0-4c82-8e57-9b45e8ac26df.png)

That command downloads the WireGuard files, installs them and runs it for you in one line. After the process is complete you will get a QR code such as this one which you can scan on your app that you download on your phone from:
Wireguard : https://play.google.com/store/apps/details?id=com.wireguard.android OR Wireguard: https://apps.apple.com/us/app/wireguard/id1441195209

Once that's done you can connect to the VPN and test it.

![image](https://user-images.githubusercontent.com/107795542/180122265-d960c889-bd02-469a-9e90-7d417ad407b7.png)










