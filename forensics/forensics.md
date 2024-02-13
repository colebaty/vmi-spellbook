# Forensics

Forensics - think "cop drama" where they're at a crime scene and all these people in hazmat suits are collecting samples and taking photographs.  That's the forensics team, and they're collecting forensic evidence.

Digital forensics is the same thing, just applied to digital devices.

The term 'forensic evidence' has a specific meaning, which can generally be thought of as "holding up in a court of law."  It takes special training to extract the evidence and prove it hasn't been tampered with so that it can be used in whatever court case.  This is no different for meatspace or for cyberspace.

Consequently, CTF forensics challenges are good training for anyone looking for a career in digital forensics, or a Security Operations Center.

# Forensics and CTFs

Generally, CTF forensics challenges break down into three broad categories:

- [Packet captures](#Packet%20captures)
- [Memory dumps](#Memory%20dumps)
- [Hard drive captures](#Hard%20drive%20captures)

# Resources for more practice

- Hack the Box: they have a relatively new type of cahllenge called "Sherlocks" that is focused on digital forensics/blue teaming skills.
- PicoCTF
- Any Jeopardy-style CTF platform with a "forensics" category

# Packet captures

## wireshark

### setup/quality of life suggestions

#### add source and destination port columns

![wireshark-columns-1](wireshark-columns-1.png)

![[wireshark-columns-2.png]]

![[wireshark-columsn-3.png]]

## approach

Depending on what specific info the challenge asks for, you may have to:

- 'carve' a file from the captured packets
- find a flag in a packet header, i.e. a specific packet
- extract data from a series of packets for further analysis

## low-hanging fruit

### file carving

![[wireshark-file-exports.png]]

Select _File -> Export Objects -> \<protocol\>_.  In this case, HTTP.

![[wireshark-file-carve-w.png]]

There's one file in this list with a funny-looking name, so it's worth investigating.  Select it (1), and save it (2) for further analysis.  Notice also that you can "save all".  

### specific packet info

#### filtering

![[wireshark-filtering.png]]

Wireshark has a very powerful filtering engine, but it can take a while to learn how to construct the filtering expressions.  There is some help - in the image above, notice that as I'm typing, there is a contextual menu to help me out.

There are also GUI options that help with filtering.

#### statstics

The _Statistics_ menu is also a good starting place.

![[wireshark-statistics-menu.png]]

Each of the three outlined menu items lets you quickly analyze the contents of the packet capture.

##### protocol hierarchy

![[protocol-statistics.png]]

The image above shows the protocol hierarchy statistics for some packet capture file.  Some things this capture reveals are (1) unusual or not-often-seen protocols and (2) protocols with a very small number of packets.  

From this window, we can easily filter just these protocols.

![[wireshark-protocols-filtering.png]]

![[wireshark-protocol-filtering.png]]

##### conversations

![[wireshark-conversation-filtering.png]]

Here you can filter by who's talking to who.  In the image, you can see two conversations, and you can filter based on a number of criteria - hopefully they're self explanatory.

Note the different protocols available:  Ethernet, IPv4, IPv6, TCP, UDP.

##### endpoints

![[wireshark-endpoints.png]]

![[wireshark-endpoints-1.png]]

### extract data from a series of packets

Sometimes you want to see the entire exchange between two endpoints.  

First, filter by protocol, then right-click on any packet and choose _Follow -> Follow \<protocol\> stream_.  In the image below, we're following the TCP stream for the REST protocol, which is associated with Redis.

![[wireshark-follow-stream-redis.png]]

![[wireshark-streams.png]]


## Zeek

Zeek is an alternative to Wireshark for packet capture analysis.  I don't personally have a lot of experience with it, but there are resources online. 

- Zeek official website - https://docs.zeek.org/en/master/install.html#installing-zeek
- Zeek Github repo - https://github.com/zeek/zeek
- Ippsec using Zeek on YouTube - https://youtu.be/Ano11xS7Yy0?si=Q8bqhiJVJt0HlBpG 


# Memory dumps

The [Volatility foundation](https://github.com/volatilityfoundation) offers the most common Linux tool used for volatile memory analysis (get it?).  There are a few options for getting it on your machine

- precompiled binaries available in package managers
- Github
	- precompiled binaries via releases
	- building from source
	- cloning and running with python
	- cloning and running via docker

Volatility3 is the most recent version, and is built on python3 libraries.  The plugins availabile with this version offer a lot of coverage for memory dump analysis, but not as much as Volatility2.

Volatility2 is built using python2 libraries, and I've run into some issues getting it working properly on systems where python3 is the main Python interpreter.  Fortunately, there's a solution.

## Volatility 3

Clone the [volatility3 GitHub repo](https://github.com/volatilityfoundation/volatility3). 

You'll need to install the Python libraries in the repo's `requriements.txt` file.  As a general rule, when I encounter Python projects that include a `requirements.txt` file, I tend to install them in a [[vmi-spellbook/binex/readme#optional python virtual environments (venv) | Python virtual environment ]].  

>[!note]
>While you can put your Python virtual environment anywhere, keep in mind that packages are installed into that virtual environment.  That means if you have two projects with `bpython` installed, your host system will have two copies of everything it needs to run `bpython`.
>
>If you find yourself installing the same packages across multiple projects, and space is a consideration, consider creating a single virtual environment rooted in your home directory.


Generally, you invoke volatility like this:

```bash
# general case
┌──(.venv)─(kali㉿kali)-[~/volatility3]
└─$ python vol.py -f /path/to/memory/dump <plugin name>

# get help, list of plugins
┌──(.venv)─(kali㉿kali)-[~/volatility3]
└─$ python vol.py --help

# get plugin help
┌──(.venv)─(kali㉿kali)-[~/volatility3]
└─$ python vol.py \
	-f ~/htb/sherlocks/recollection/recollection.bin \
	windows.pslist --help

# example of use with windows.pslist plugin
┌──(.venv)─(kali㉿kali)-[~/volatility3]
└─$ python vol.py -f ~/htb/sherlocks/recollection/recollection.bin windows.pslist

```


## Volatility 2 - Docker

This version requires Docker - see [these instructions](https://www.kali.org/docs/containers/installing-docker-on-kali/) to install it on Kali

In a browser, navigate to the [Blacktop Docker Volatility GitHub repo](https://github.com/blacktop/docker-volatility).  Follow the instructions for Installation and Getting Started.

- [Volatility2 command reference](https://github.com/volatilityfoundation/volatility/wiki/Command-Reference)

Here's how to run Volatility2 using this Docker build:

```bash
┌──(.venv)─(kali㉿kali)-[~]
└─$ docker run --rm -v $(pwd):/data:ro blacktop/volatility \
	-f recollection.bin \
	--profile=Win7SP0x64 \
	consoles
```

An explanation of the flags and commands:

- `--rm`: remove the container after it exits (docker)
- `-v $(pwd):/data:ro`: mount the current working directory on the host to the `/data` directory of the container as a read-only volume (`:ro`) (docker)
- `blacktop/volatility`: the docker image to use (docker)
- `-f recollection.bin`: the memory dump to analyze (volatility)
- `--profile=Win7SP0x64`: the 'profile' to use for volatility plugins -- requires some initial analysis of the memory dump (volatility)
- `consoles`: the volatility plugin to run against the memory dump (volatility)
### why would I need to run Volatility2? isn't Volatility3 newer?

Volatility2 has been a very popular, well-known memory analysis tool for a long time.  Since the announcement of Python2's end-of-life on January 1, 2020, work has been done to port Volatility to Python3, but it's not yet finished.

Consequently, the full functionality of Volatility2 is not yet available in Volatility3.  In fact, if you notice, when you clone Volatility3, you're on the `develop` branch.

As an example, there is a HTB Sherlocks challenge which asks

>After the attacker gained access to the machine, the attacker copied an obfuscated PowerShell command to the clipboard. What was the command?

This easily answered using Volatility2's `clipboard` plugin, but no such plugin yet exists for Volatiltiy3.  

### optional quality of life steps

Adjust your `PATH` environment variable to include the path to the directory containing the `vol.py` script.

```bash
export PATH=/path/to/volatility/dir:${PATH}
```

Now, until that shell dies, you can simply run Volatility like any other command line utility.

```bash
# example of use with windows.pslist plugin
┌──(.venv)─(kali㉿kali)-[~/volatility3]
└─$ vol.py \
	-f ~/htb/sherlocks/recollection/recollection.bin \
	windows.pslist
```

# Hard drive captures

Some challenges give you a cloned image of a hard drive and ask you for certain information contained in the image.

[Autopsy](https://www.autopsy.com/) is a digital forensics tool which comes preinstalled on Kali that can be used for challenges like this.

To get Autopsy up and running, follow [these instructions](https://www.geeksforgeeks.org/autopsy-cyber-forensic-browser-in-kali-linux/) to start a new "case" and add the image file to the case.

You can use the file browser to see almost everything on the drive, including the contents of deleted files (under most cases).


