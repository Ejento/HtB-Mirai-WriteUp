## Welcome to my first WriteUp, which is for the Mirai Box.

## Breaking in.

First of all, we have to scan the server for ports. We know that the IP of the Mirai's box is 10.10.10.48, so we can scan for active ports using the **nmap**.

`nmap -sV -sC -oA output 10.10.10.48`

I used the next options
- -sV: Determine service / version information
- -sC: Use default scripts
- -oA: Output in the three major formats at once

![nmap](/images/nmap.png)

So we are scanning the machine for ports and we found out that the ports **22** (_ssh_), **53** (_dnsmasq_) and **80** (_http_) are active.

Next, let's open the browser and go to http://10.10.10.48:80

![empty page](/images/web1.png)

That's an empty page. Searching for interesting things in the the source code of the page, I found nothing. So let's try to enumerate the directories of the server.
I fire up **dirb** and I use the common wordlist for the directory scan.

`dirb http://10.10.10.48 -r -o mirai.dirb`

I used the next options
- -r: Because I don't want to do a recursive scan. (_who has time for that_).
- -o: I want to save the results in an output file.

![dirb](/images/dirb.png)

So I have the results, and I have a new directory that I can visit and that is, **/admin/**. I open the directory in my browser and I have the following page in front of me.

![pi-hole](/images/pihole.png)

So let's say that I don't know what **pi-hole** is, so my next move is to visit Google. 
The first result is the url [Pi-Hole](https://pi-hole.net) and if I open the page I can see that is a software for blocking advertisements in a network and we can run it in a raspberry pi.
My first thought was to see if I am actually in a raspberry pi, so I searched the web for the default SSH credentials (I knew them already because I have one, but let's say that I don't) and in the [documentation](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) I found that the default user is **pi** and the password is **raspberry**.

![documentation](/images/raspdoc.png)

With that knowledge I tried to connect to the SSH port.

`ssh pi@10.10.10.48`

![ssh](/images/ssh1.png)

...and I am in. The first step is done. **I am in**.

## Enumeration

As you can see, I am connected in the server as the user _pi_. But I don't know what my _uid_ is so I am executing the command `id`.

![uid](/images/id.png)

So I have the `uid 1000`. That's good. Let's see what commands I can run as root in the server. I am executing the next command `sudo -l`.

![sudo -l](/images/sudol.png)

And I can execute _ALL_ the commands as root. So let's fire up a login shell for the user _root_. The command is `sudo -i`. And now, I am the user **root** in the web server.

## Getting the Flags

Searching for the file _root.txt_ in the root's home directory, I got the next message.

![cat root.txt](/images/catRootTxt.png)

> I lost my original root.txt! I think I may have a backup on my USB stick.

So the information I got here is that it is worth a try to search for a USB stick connected to the server. I use the command `mount` and I have the next interesting results.

![mount image](/images/usbstick.png)

Let's visit the directory, using the command `cd /media/usbstick/` and open the next __odd__ text file that I saw there and that is __damnit.txt__.
I use `cat damnit.txt` and I got the next text.....

![catDamnit](/images/catDamnitTxt.png)

> Damnit! Sorry man I accidentally deleted your files off the USB stick.
> Do you know if there is any way to get them back?
> -James

**Damn you James. What are you doing with my files?!?!**, _I screamed to my screen_.

Anyway, let's take a moment and think about Unix OS. In Unix OS, we say that _**Everything is a file!**_. So what does that mean? It means that we can check the entire partition with a command like it is a file, for example `strings`. So I am executing `strings /dev/sdb` and I have the **root flag**, from the text that was inside the **root.txt** before it got deleted from James.

![root flag](/images/rootflag.png)

As you can see, in this run I forgot to find the **user flag**, so I jumped in again using the `ssh` and I searched for the **user.txt** file in the user pi's home directory.

![user flag](/images/userflag.png)

Now we got all the flags. We are done. You can jump in the next challenge and have fun with it.

## Happy hacking.

### by HtB's user: Akumu
