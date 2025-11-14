# Duck-Decoder

## Introduction

A while ago I went to recieve some classes to annother country lucky for me, I already had my duck! Often in my trip
I found myself near unlocked computers which I could dump lssas or backdoor depending the case, for this I started doing a whole bunch of different payloads and scripts to pwn all of these computers!

Sadly for me, since I wasnt using my own computer at the time (for security) I erased the whole account on the system except for my backed up source files.. or so I thought! I had mistakenly backed up only the *inject.bin* files and all of my thousands of lines of source code were gone now! I thought, if I just keep the .bins classified I could still reuse them to pwn computers until, a Win 8 computer comes up aviable to hack and none of my scripts work!

This project was the result of having to figure out what was inside all of those *inject.bin* files that I had saved over time for on-the-fly payload changing. This script solved my problem of often losing or accidentaly deleting the source code files of all my hacks.

## Fork additions (TheKester)

This repository is a fork maintained by thekester (GitHub: [thekester](https://github.com/thekester)). The `DuckDecoder.py` script was rewritten for Python 3, cleaned up, and extended so it is easier to use on modern systems. Highlights:

- Added `--layout/ -l` so dumps recorded on both QWERTY and AZERTY keyboards decode properly (no more guessing which layout an implant used).
- Modernized the CLI with argparse, optional `--output` file support, and friendlier help/usage text.
- Display mode now shows the raw key tokens (`<BSPACE>`, `<TAB>`, `<ENTER>`, arrow keys, etc.) while decode mode applies the edits/backspaces to reconstruct exactly what was typed.
- Refactored the key-map logic into readable tables so adding future layouts is straightforward.

Everything else—especially the original story, usage examples, and keyboard mapping instructions—remains true to the upstream work by @JPaulMora.

## How to use it

The fork targets modern Python 3 (tested with 3.10+). The quickest way to get started is:

```
python3 DuckDecoder.py <display | decode> /path/to/inject.bin [--layout qwerty|azerty] [-o decoded.txt]
```

Run it without arguments to display the help menu. The program still has the original two modes, but their meaning is now explicit:

- `display` shows the raw sequence of HID tokens so you can see when backspace, enter, arrows, etc. were triggered.
- `decode` applies those tokens (removing characters when backspaces appear, inserting newlines on enter, etc.) so the recovered text mirrors what the victim actually saw.

To explain the difference, let’s say I encode this payload:

```
 DELAY 500
 STRING Hello!!
 BACKSPACE
 ENTER
 STRING This is a test!!
```

`display` keeps the tokens inlined so the editing keys stay visible:

```
Hello!!<BSPACE><ENTER>This is a test!!
```

`decode` applies the edits while respecting the keyboard layout you selected, which produces the same text the duck typed on the target system:

```
 Hello!
 This is a test!!
```

## Want support for a different keyboard?

The program finds the position of the duck hex code and translates it by finding its mirror position in another
list with the letters. To submint a keyboard mapping, make two lists pressing the keys in this order:

![keyboard_mapping] (https://raw.githubusercontent.com/JPaulMora/Duck-Decoder/master/mapping.png) { width: 400px; }

The lists should look like this, one for shift and one for plain characters:

```
 Letters = ["a","b","c","d","e","f","g","h","i","j","k","l","m","n","o","p","q","r","s","t","u","v","w","x","y","z"etc..]
 CapLetters = ["A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z"etc..]
```

In this fork each layout lives in its own helper (see `_build_qwerty_layout` and `_build_azerty_layout` inside `DuckDecoder.py`). Once you have both lists for your keyboard, build a similar dictionary of HID codes to characters and register it inside the `LAYOUTS` map so the `--layout` flag immediately recognizes it.
