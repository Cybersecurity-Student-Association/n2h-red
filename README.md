# Welcome to N2H-Red!

This is the GitHub repository for Old Dominion University's student-led
Cyber Security Sudent Association (CS2A) Noobs-2-Hackers Red Team group.  This
is a place where we'll be using hands-on lab environments to practice some of
the fundamental skills used in red teaming, or penetration testing.

We communicate primarily over the \#n2h-red channel on the 
[CS2A Discord](https://discord.gg/aGG4Zjt). The next meeting on Discord will be
October 29th at 3pm EDT. See you there!

This is a "living document", so check back periodically for new lessons. The
goal is to have a new lab out every two weeks.  The next

# Some initial setup

Before starting with any of the labs, you'll need to complete some of the
initial setup steps below:

1. [VM setup](#vm-setup)
2. [Required and recommended software](#software)

# Next Step

From here, check out either [Introduction](Introduction.md), or jump straight
into the first lab, [Reverse Shells](01-Reverse\ Shells).


## VM setup

### Hypervisor

In order to run the VMs listed below, you'll need some sort of
[hypervisor](https://en.wikipedia.org/wiki/Hypervisor), or a program to manage
VMs.  

The simplest solution is to install the free 
[VirtualBox](https://www.virtualbox.org/wiki/Downloads) hypervisor. 

This is by no means a strict requiremtn - for example, if you have a home lab
virtual environment, you could certainly install the VMs there!  Totally up to
you.  The important part is installing the VMs.

### Kali VM

You'll need some sort of ready-built pentesting operating system. We will 
primarily use [Kali](https://kali.org/get-kali/#kali-virtual-machines), but any
other major pentesting OS should do.

To install Kali in VirtualBox, follow [these
instructions](https://kali.org/docs/virtualization/install-virtualbox-guest-vm/)

### SEED Ubuntu 20.04

Most of what we'll be doing will be on the [SEED
Labs](https://seedsecuritylabs.org/labsetup.html) (SEcurity EDucation). SEED
provides a ready-made Ubuntu 20.04 VM, which comes pre-installed with Docker.
Docker is a useful containerization/virtualization software package that will
enable us to quickly distribute and deploy the various lab environments.  

The intention is that, by having total control over the lab environment, we'll 
be able to learn not just _how_ to pull of a given technique, but _why_ this
technique works.


-[SEED Ubuntu 20.04 setup
instructions](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)

## Required and Recommended Software

### Required Software

This list will likely grow as we progress through the labs. For the [first
lab](01-Reverse\ Shells), you will need the following utilities installed on
your SEED Ubuntu 20.04 VM:

- `git`
- some sort of command-line text editor, e.g. `vim` or `nano`

To check whether these programs are already installed, try the commands shown
below. Substitute the program names as appropriate.

```bash
$ which git
/usr/bin/git


# your git version may not match exactly - this is fine
# as long as some version comes back, you're good to go!
$ git --version
git version 2.42.0
```

### Recommended Software

You may have heard that a penetration test is only as good as the report that
comes from it.  With that in mind, one of the "secondary" goals of this
endeavor is to build and enforce good habits, particularly note taking and
documentation.

To that end, what follows is a list of some popular applications used for note
taking and screenshotting.  

**Install these on your Kali VM.**

- note taking
    - [Obsidian Markdown Editor](https://obsidian.md) - install the `.deb`
      image
    - [Cherry Tree](https://www.kali.org/tools/cherrytree/) - installed by default on Kali. `sudo apt install cherrytree`
      to install if it's not present
- screenshot
    - [Flameshot](https://flameshot.org/docs/installation/installation-linux/)

