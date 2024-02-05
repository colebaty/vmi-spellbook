# binex

_bin_ary _ex_ploitation

this is a primer for anyone who's new to binary exploitation.  this is not
meant to be comprehensive - I'm just trying to teach you some of the notes, so
that you can go out and learn the music.

we'll be mostly following CryptoCat's tutorials:

- [YouTube playlist](https://github.com/Crypto-Cat/CTF/tree/main/pwn/binary_exploitation_101)
- [Github Repo](https://www.youtube.com/watch?v=wa3sMSdLyHw&list=PLHUKi1UlEgOIc07Rfk2Jgb5fZbxDPec94&pp=iAQB)



# requirements 

- python3 
- GNU Debugger aka `gdb`- should be in apt repositories `sudo apt install gdb`
- one of the following gdb enhancement suites:
    - `gef` (GDB enhanced features): [link to installation
      instructions](https://hugsy.github.io/gef/install/) \*\*\* recommended
    - `gdb-peda`: [link to installation instructions](https://www.kali.org/tools/gdb-peda/)
- [pwntools](https://docs.pwntools.com/en/stable/)
- [bpython](https://bpython-interpreter.org/downloads.html) - interactive
  python shell.  \*\*\* strongly recommended

## optional: python virtual environments (venv)

during CTFs, you may need to clone GitHub repos that contain instructions to
install specific python libraries.  you may find that these librarires conflict or
interfere with python projects you already have on your machine.

python virtual enironments are a way to work around these problems.  you can
create a per-project virtual environment using the `venv` library, and anything
you install into the virtual environment will not affect your 'host' system.

to install the `venv` module

```bash
# install venv module
sudo apt install python3-venv

# run these commadns in the root dir of your project
python3 -m venv .venv
source .venv/bin/activate
```

when you run the command `source .venv/bin/activate`, you'll see your prompt
change to include the name of the virtual environment:

```bash
┌──(kali㉿kali)-[~]
└─$ mkdir venv-test

┌──(kali㉿kali)-[~]
└─$ cd venv-test

┌──(kali㉿kali)-[~/venv-test]
└─$ python3 -m venv .venv

┌──(kali㉿kali)-[~/venv-test]
└─$ source .venv/bin/activate

┌──(.venv)─(kali㉿kali)-[~/venv-test] <=== prompt changed
└─$
```

to exit the virtual environment, enter the command `deactivate`

```bash
┌──(kali㉿kali)-[~/venv-test]
└─$ source .venv/bin/activate

┌──(.venv)─(kali㉿kali)-[~/venv-test]
└─$ deactivate

┌──(kali㉿kali)-[~/venv-test]
└─$
```


# additional resources

- [Braindead Buffer Overflow Guide to Pass the OSCP Blindfolded](https://boschko.ca/braindead-buffer-overflow-guide-to-pass-the-oscp-blindfolded/)
- The Hack the Box fortress Jet has a few different overflow types: the
  challenge called 'Overflown' is a basic stack overflow very much like the
  Braindead one

