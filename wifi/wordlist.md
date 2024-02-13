# generating wordlists

## `cewl` - custom wordlist generator

```bash
$ cewl -d 1 -w <outfile> <url>
```
- `-d`: depth 1; default is 2
- `-w <outfile>`: save the output to a file
- `<url>`: the target webpage

### sample output

```bash
$ cewl -d 1 -w wiki -v https://en.wikipedia.org/wiki/Main_Page

$ head wiki
the
and
rft
Wikipedia
info
The
from
org
for
February

```

## `hashcat`

source: [ippsec](https://youtube.com/watch?v=80-73OYcrrk&t=3438)

```bash
$ hashcat --force <wordlist> -r /usr/share/hashcat/rules/best64.rule --stdout > <outfile>
```

rules make transformations on given words.
