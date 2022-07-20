# Setting-up-a-VPN-using-OpenVPN-on-Raspberry-PI.

Creating a VPN on a raspberry pi to understand the technology better. Making a VPN server is not necessarily the best privacy solution, however playing with small tech in a safe manner is good for learning and when you learn new things you can be understand the technology better so you can make sure your privacy is protected. 

**PREFACE**

In 2019, I was travelling through Malaysian airport. I naively attempted to authenticate with the access point for their airport Wi-Fi, in a foreign country after a long flight I just wanted to see a familiar face or chat with someone back home. My attempt was unsuccessful so I went back to listening to music and waited for my flight.
This simple unsuccessful attempt at authentication was enough. Enough for PayPal to send me email notifications confirming there had been funds deposited to a blizzard account under my name. I tried but, I wasn’t able to log into my blizzard account. So I proceeded with forgot my password, luckily for me I had enabled MFA on the account, after gaining access back into the account I could see there were multiple transactions to a game on blizzard. On hindsight I was extremely close to losing my account completely, but I’m thankful for MFA. 
I reached out to PayPal and explained what had happened and they refunded my account. 
After the incident I thought to myself about things I could’ve done different to prevent this from happening again. I found VPN would’ve been a perfect solution. 
VPN services now days are sub services that allow you to connect to any of the service providers servers all across the world. Sometimes the ISP can cap your speed VPN can be used to bypass that, VPN also give you the freedom to evade geo-restriction. Few streaming services have implemented geo-restriction as common practice. 
Despite all this what is a VPN? How does it work? Let’s explore the topic and see if we can answer those questions.
Scope: Build a VPN Server using a Raspberry pi to understand the overall concept better.

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



