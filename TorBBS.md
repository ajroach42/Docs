# A Modern BBS

Supposed for a moment that you want a place in Cybrespace where you and your friends and maybe even some strangers can post updates about your lives, chat with one another, share images and articles and news, and even play games together. Today, you might reach for Facebook, right? But lets suppose again that the year is 1985. Then what do you do? 

You reach for a BBS, a Computerized Bulletin Board System. Either you find one running in your area code, plug in your modem and dial in, or you drop a few bills at the local computer store on some BBS software and you host it yourself. Attach a sufficiently sized disk, set up extra phone lines and hook in additional modems, offer cross country email and real time multiplayer games! Do all of this on what today would be considered a dinosaur, unfit for any purpose. 

For some reading this, the above is going to recall pleasant memories of late nights swapping warez, playing Legend of the Red Dragon or Tradewars, and for others it’s going to sound like some kind of revisionist retro-science fiction. It’s both and neither and other things, but that’s not why we’re here. 

## Then 

It used to be that, fundamentally, if you wanted to run a BBS you needed: 

- A phone line (which you were paying for anyway)
- A computer (one time purchase)
- A modem (one time purchase) 
- A piece of BBS software (often lovingly pirated from another BBS.) 
- … That’s it. That’s all you needed. 

Install and configure the software, plug in the computer, print up some flyers that advertise your Cool Name and your Hours of Operation and What kind of Games people can play, go drop some txt files on other local or almost local BBSs announcing your arrival. 

The important thing to note here is that once you had purchased or pirated the appropriate hardware and software, you didn’t have to pay anyone any rent or ask anyone for permission to run your board. (Unless you wanted additional phone lines, but that’s another story entirely.) 

## Now

The equivalent to a BBS these days would be an obscenely complicated web application. Something on the order of complexity of Facebook (certainly, the little fediverse node I run doesn’t have all the features a real BBS would), and even then you’re missing some key functionality. And in order to run something like that today, you need:

- Web hosting (monthly expense) 
- File storage (monthly expense)  
- A domain name (yearly expense) 
- Software to run 
- Security expertise (years of education or regular expense or much higher irregular expense.)
- Technical expertise 

For even a small social network, the monthly expenses are often on the order of $100 or more. And, I would argue most importantly, you need permission. Permission from your web host, permission from your DNS provider, permission from your CDN, permission from AWS. You’re renting your host, you’re renting your file storage, you’re renting your domain name, you’re probably using a CDN, you’re probably relying on Google and Amazon for at least part of your tech chain. 

Or you go the other obvious route, and you just use facebook. And it costs you your privacy, your digital freedom, your attention, your emotional agency, and possibly the future of politics in your country, while lining the very deep pockets of some very rich people. 

And, guess what? You still need permission! Permission to exist. Permission to be called the name of your choice. Permission to communicate. They’ll cut you off without recourse for myriad reasons, leaving you without an alternative, and sticking you in a world where you have been Othered by the world’s dominant communication platform, and then what will you do?

So, let me just say, *Fuck That*. There’s a better way. There are lots of better ways. This is the first in a series of articles exploring potential better ways. 


Today’s better way is … 

## Running an 80s style BBS in your own home without renting anything from anyone 

You need: 

- A raspberry pi 0w ($10) + storage ($5) and power ($5) or any other computer 
- An internet connection with wifi (for now, at least) 
- Some software 

That’s it! That’s all you need. $20 worth of electronics gizmos, an hour or so to set up the software, and maybe a few bucks to print up flyers to tell your friends (assuming you actually want locals to connect.) 

It’s easy! You can do it with no previous computer experience. Just follow the instructions bellow. 

### How is this possible? 

Well, magic mostly. 

The magic of community supported,  freedom respecting software, and the people that build it. To make this happen, we’ll be using Raspbian on a raspberry pi (or any other Debian derivative running on any other computer) The Onion Router (which will let us bypass the need to pay for a domain name, and will give us end-to-end encryption to boot), and a piece of vintage BBS software (in this case, I lean towards Synchronet, but the instructions will work just as well for Mystic or any other BBS you can get running.) 

This tutorial will cover basic installation and setup, up to the point that other people can connect to the BBS. 

In a future post, we’ll cover advanced Synchronet configuration, file sharing, games, and federation. These are easy too, but not anything I’m ready to cover today. 

### Instructions: 

**Install your Operating System.**
If you’re using Raspbian for Raspberry Pi [follow the basic instructions for installation on the raspberry pi site.](https://www.raspberrypi.org/documentation/installation/installing-images/) The short version: download an image on a computer,  write the image to the SD card using the appropriate tool for your OS of choice

**Enable Wifi and SSH**

Before booting the pi 0, open the Boot partition on the SD card and do the following:

- Enable SSH: Make a file in the boot partition called ssh. It should be empty and have no file extension. On Mac and Linux you can do this by navigating there in a terminal and typing `touch ssh` on windows, you’ll have to make it an empty text file, and then remove the .txt extension 
- Enable wifi: make a text file called wpa_supplicant.conf on the SD card. Fill it with the following:   ```network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_PASSWORD"
    key_mgmt=WPA-PSK
}```

The boot partition will be the only visible part of the drive on Windows and Mac. On linux, there will be two partitions. You want the smaller FAT partition, not the larger EXT partition. 


**Connect to the machine** 

Connect to the machine over SSH. 

Use the username and address pi@raspberrypi and the password raspberry. 

On linux or Mac, from a terminal just type `ssh pi@raspberrypi`. On windows, you’ll want to download and install Putty, and use that to make the SSH connection.  

**Change your password** 

Seriously, change the password as soon as you turn on the machine. Do not leave the machine powered on with wifi and SSH enabled without changing the password. 

You change the password by typing `passwd pi` and then following the prompts. 


**Install Tor**

This part is super easy, just: `sudo apt install tor` and then enter your password. Sudo means Super User Do, and it’s a way of telling the computer that you *Really* want to do something, and that you have permission to do it. 

**configure Tor**
Type `sudo nano /etc/tor/torrc` and then find the section of that file that mentions hidden or onion services. 

In that section, add something like this: 

```
HiddenServiceDir  /var/lib/tor/sbbs/
HiddenServicePort 23 127.0.0.1:23
```

Save the file (press Ctrl+X, then follow the prompts.) and then restart tor so that it picks up those configuration changes 

`sudo systemctl restart tor` 

**Find your tor address** 

Navigate to /var/lib/tor/sbbs and read the file. If you don’t know how to do that, the following command will work:   ` cat /var/lib/tor/sbbs/hostname `

That should give you a string of gibberish that ends in `.onion`. Copy that somewhere and save it. That’s how you’ll connect to your BBS. 

If you don’t see that, you’ve done something wrong. That’s okay! Just retrace your steps. If you can’t figure out what you’ve done wrong, you can always reach out to me! I’ll be glad to help troubleshoot as I am able. 

**enable Tor**

Once you have your onion address, we’ll want to enable tor so that it’s available each time the computer restarts. For that, type 
`sudo systemctl enable tor`. 

**Install your BBS** 
If you’re going to use synchronet, follow the [synchronet installation instructions for Raspberry Pi](http://wiki.synchro.net/howto:raspbian_install). (Don’t worry, they’re easy.) 

The first thing it wants you to do is add a user, so 

`adduser sbbs`

Then grant your new user the ability to sudo with 

`sudo adduser sbbs sudo`

(This adds the user sbbs to the group sudo, granting them the ability to execute commands as the sudo user.) 

Now you’ll need to reboot the pi, and then log in as the sbbs user. 

`sudo reboot` 

You’ll be disconnected. Wait a few minutes and then reconnect `ssh sbbs@raspberrypi`   From there, just follow the instructions. The “make install” step is going to take a few hours. On my 0w, at least two hours. That’s okay. Just walk away from it and let it run. 

*Configure your BBS* 

First we need to tell SBBS where it’s config data is. We do that by running:   `export SBBSCTRL=~/sbbs/ctrl`

Make note of this command, you’ll probably end up running it a couple of times by hand, before we set it up to happen automatically. 

Then we can launch the interactive config program 

`~/sbbs/exec/scfg`

There’s a lot to configure here, feel free to poke around the menus. We’ll revisit this program in the future, but for now just make sure it starts, set your name and any other config settings you know you want to change. 

When you’re done, exit that program and start the BBS. 

*start the BBS* 

The first time we start it, we’re going to start it by hand. Later we’ll automate this. 

To start the BBS, run the following command: 

`sudo -E ~/sbbs/exec/sbbs`

For now, you’ll need to run the above export and execute commands each time the machine reboots. In our next article, we’ll talk about some options for doing this automatically. 

*That was a lot!*

Well yeah, it was. There’s more though, but that gets the hard part out of the way. At this point, all thats left are the fun bits! Well, the fun bits and teaching people how to connect, but that’s okay. It’s easy too. 

So now you have Synchronet BBS running over Tor and people can connect to it! Let’s talk about how to connect to it. 


### Connecting to an 80s Style BBS over Tor 

You need: 
- A PC or Android device running a modern OS 
- Torsocks (for Mac, linux), Tor browser and putty (for windows) or orbot and termux (for Android) 
- Uhh, I guess that’s it. This is really easy to do. 

*Windows instructions* 
Establishing your first connection from Windows is a little more complicated than Mac or Linux, but it’s still something you can manage without a ton of previous experience. 

- First, download and install the Tor Browser Bundle. 
- Then, download and install Putty 
- Next, start tor browser and putty,
- Now, from the putty window, click on Proxy. In the menu choose Socks 5, specify the address as localhost and the port as 9150. Towards the bottom of the window, also click “yes” under “do DNS name lookup at proxy end. 
- And Finally, click “session” enter the onion address as the hostname, 23 as the port, and either “save” the session for future use, or just click “open” to get started. 

This will open a Telnet connection to the BBS over Tor. 


*Mac instructions* 
Install torsocks. I use brew. If you don’t have brew, also install brew. ‘brew install torsocks` 
 
Start a terminal with torsocks enabled `. torsocks on`

Telnet to your onion address from above. `telnet [yourgibberish].onion`


*linux instructions* 
Install torsocks. Open a terminal and then `sudo apt install torsocks` on debian or ubuntu. On other platforms, this will be different. 

Start a terminal with torsocks enabled `. torsocks on`

Telnet to your onion address from above. `telnet [yourgibberish].onion`


*android instructions* 
Install orbot and termux 
Open the orbot settings and tell orbot to proxy connections for termux
Launch termux and then `telnet [yourgibberish].onion 

### Is this safe? 

Yep! 

Under normal circumstances Telnet over a public network is Very Unsafe. This way of doing things, connecting to an onion service, is Very Safe. 

You’ll see a lot of instructions on the internet claiming that telnet over tor isn’t safe because the exit node can see what you’re typing in plaintext. This would be true, if there was an exit node. There isn’t an exist node, because you’re connecting directly to an onion service. 


The BBS is the exit node, and all traffic is encrypted from the time it leaves your machine until it gets to the BBS. 

Connecting this way is safe. 


### Is this legal? 

Yep! Not only legal, but prudent. It protects you from advertisers snooping on your traffic. It protects you 

### Woah, I thought tor was for the dark web? 

Some people do erroneously refer to things hosted at onion address on tor as being “on the dark web” because they are encrypted, and only accessible through tor. 

In reality, you can use tor to access the regular internet and onion sites. You can use it because it makes your anonymous, or you can use it because it allows you to host your BBS without asking for anyone else’s permission. 

As far as I’m concerned, this is the way of the future. 


## Next Steps 

So Many! In the future we’ll explore: 
- Advanced configuration for synchronet
- Games! 
- Connecting your BBS to other BBSs to share messages 
- Setting up a friendly name, so you don’t have to remember that gibberish each time you want to connect 
- How and why Tor keeps you safe
- Full disk encryption for your BBS
- Other things you can run on the same box 
- And a lot more. 

But, for now, you have a message board that you can host in your own home for $20, and I think that’s pretty cool. 
