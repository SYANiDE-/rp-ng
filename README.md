# rp-ng

A wrapper around rp++, extends existing rp++ and adds additional functionalities.

* Interactive ncurses-based gadget selection tool by default (cpick)
* Interactive mode writes selected gadgets to an outfile at the end, also outputs selected gadgets at end of runtime
* text-based output (fallback) if ncurses is problematic or annoying, selectible with --interactive or -i, which disables interactive.
* Filter out lines containing badchars in addresses
* --addbase: capability to add a base address to all gadget addresses BEFORE filtering out badchars, but AFTER --va already rebased the addresses
* ability to --sort the gadget output by length of gadget line
* ability to filter down to --uniuqe gadgets
* ability to define your own arbitrary single idiom from the cli
* --file to perform a gadget search on a dll
* --gadgetfile to parse an existing rp++ output (lacks --va, --roplen functionality)

This tool works on the principle of IDIOM definitions.  What is an idiom in this context?  An idiom is simply a collection of regex patterns.  Key == line match pattern, Value = list of patterns to colorize for output (non-interactive, interactive end-output).

You can check out the existing IDIOMS definitions defined at the end of the script, and of course these are fully extensible.

There are two groups of idioms, one is strict (specific ), the other is much --loose er set of idioms (.*). You might think of it this way, the default set of idioms might get you a sufficient selection of gadgets, but if you're feeling desperate/brave or explorative, you might want to try using the --loose idioms

## How to install
Start with the dependencies, this requires rp++ already installed to path (--file mode only), aside from that, also requires colorama and cpick libraries:
```
python3 -m pip install colorama cpick
```
Doesn't hurt to have rp++-ng also installed to path

## Usage
```
┌──(notroot㉿Business-End)-[~/repos/rp++-ng]
└─$ rp++-ng -h

  rp++-ng?                           ^^           
             _________ _________      ___ __  __________ `
   .       |    _o___|    _o___ ++- |   \  |/   /_____/  !
          |___|\____|___|%%%%%     |____\_|\___\____.] 
  z        `BB' `BBB'`B'           `BBBBBBB' `BBBBBBBB' 
     ;                                    Chain faster
              [[                            $$$$$ $$$$$$      i
                    +                                    SYANiDE
        

usage: rp++-ng [-h] [--interactive] [--number] (--file FILE | --gadgetfile GADGETFILE) 
                [--va VA] [--addbase ADDBASE] [--roplen ROPLEN] [--badchars BADCHARS] 
                [--regex REGEX] [--sort] [--unique] [--loose] [--matches MATCHES [MATCHES ...]]

rp++ wrapper, gadget finder

options:
  -h, --help            show this help message and exit
  --file FILE, -f FILE  DLL to parse
  --gadgetfile GADGETFILE, -g GADGETFILE
                        rp++ gadgets output file as input
  --addbase ADDBASE, -a ADDBASE
                        Add a baseaddr to line addresses before searching for badchars. 
                        Note line output retains --va basing (or original basing if
                        read from existing file).
  --badchars BADCHARS, -b BADCHARS
                        Prune gadgets with instructions containing any [these] badchars
  --regex REGEX, -R REGEX
                        Arbitrary REGEX line pattern (one-off search)
  --sort, -s            Sort gadgets by length
  --unique, -u          remove duplicate gadgets
  --loose, -l           Go through the loose idioms (WARN: much wider net!!! 
                        Suggested to use a lower --roplen)
  --matches MATCHES [MATCHES ...], -m MATCHES [MATCHES ...]
                        Colorize --regex lines by --match patterns, space-separated list 
                        of pattern strings. If supplied, this MUST be the last
                        argument.

Interactivity options:
  --interactive, -i     DISABLE use of ncurses interactive gadget selector.
  --number, -n          ENABLE line numbering in ncurses interactive gadget selector.

rp++ exclusive options:
  --va VA               Virtual Address (rebase output)
  --roplen ROPLEN, -r ROPLEN
                        Limit gadget instruction count to (this) upper bound

```


## Find gadgets in a binary or DLL:
```
./rp++-ng -f msvcrt.dll -r 5 --va 0x0 
```

## Find gadgets in an existing rp++ output file:
```
./rp++-ng -g msvcrt_curatedgadgets.txt 
```

## rebase gadget addresses by address + --addbase before filtering out --badchars
```
--addbase 0x07310000 -b "\x00\x0a\x0d"
```

## Search for an arbitrary --regex gadget, color --matches (optional) highlight patterns on found gadgets
```
--regex ': sub ..., ... .* mov ..., ... .* ret' --matches 'sub ..., ...' 'mov ..., ...'
```

## for interactive mode (default):
There are plenty of keybindings for cpick (https://pypi.org/project/cpick/).  Probably the most useful in the rp++-ng context:

```
q       - quit select on current IDIOM (move to next IDIOM)
[esc]   - quit select on current IDIOM (move to next IDIOM)
/       - search items via wildcards, regex or range 
            (ability to perform additional regex on list of 
            resulted gadgets!) (vi-like)
n       - next search result
p       - previous search result
r       - reset search results or picks
t       - toggle item
[spc]   - toggle item and go down a line
[ret]   - pick an item
u       - undo last pick
v       - view picked items
?       - view extended help
```
