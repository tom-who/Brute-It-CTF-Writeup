# Brute-It-CTF-Writeup
A step by step walk through for the Brute It CTF on Tryhackme

### Link https://tryhackme.com/room/bruteit

### Enumeration

`
└─$ nmap 10.10.3.223 -sV -sC
`

`
PORT   STATE SERVICE VERSION
`


`
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
`


`
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
`

We are told there are 2 ports open and the version of the services the ports are running

The website is an apache template with no useful information so we will now try to scan for other potential directories using gobuster

![image](https://github.com/traveller404/Brute-It-CTF-Writeup/assets/92340426/2804ba66-7b85-467f-94ed-fd68da7a7b94)

As you can see we are brought to a subdomain of /admin this is a login page and inspecting the source code tells us that the username is "admin"

We will now launch a brute force attack on the webpage using hydra, but first we have to use burp suite to configure our hydra variables

Launching burp suite and setting up a proxy in the burp browser (chromium) and going to http://<machine_ip>/admin and finding out what variables are assigned to take input from the user in the login page

and assigning those variables to our hydra variables comes together to form out brute force attack

`hydra -l admin -P /usr/share/wordlists/rockyou.txt.gz <machine_ip> http-post-form "/admin/index.php:user=^USER^&pass=^PASS^:F=Username or password invalid" -V`

We now have valid credentials to proceed and log in to the website, once logged in, we are greeted with the web flag and a link to a RSA key *I have blurred out the flag*

![image](https://github.com/traveller404/Brute-It-CTF-Writeup/assets/92340426/ffe1da83-6d1b-46ec-9117-9b7291ddfbd7)

After using wget with the link to the key, it is not yet ready to be used to login as <username> on the ssh port, we need to make it a private file and we need to get the password for it 
`chmod 600 id_rsa` will make it private but we need to get the password, we can do this by using the tool johntheripper 

After using john to get the password to use this id_rsa we can now ssh to the machine

On ssh we have access to the user flag and now its time forrrrrrrr..................

### Privelege Escalation

To see what we can run as sudo we use the command `sudo -l` this is revealed to be cat
so we can do sudo cat /etc/shado to get roots password, take that output and use johntheripper once again to bruteforce that, giving us our root password
To get the flag we can either su to root or use `sudo cat` again to cat /root/root.txt

# Hope this helps!!!
