# Showwin-Script
Linux script for quick switching active window using hot keys.

Bind some hot keys in your linux desktop environment to this script with window description (e.g. part of window name) and action for case when no suitable window is found. Switch to specified window using hot key. 

# Prerequisites
Console tools *wmctrl* and *xdotool* are required. In Ubuntu they can be installed using apt:
```
sudo apt-get install wmctrl xdotool
```

Both scripts *showwin* and *showwinDetach* should be in your *$PATH* dir. Clone repository somewhere and add symlinks to scripts into *~/bin* directory.

# Usage
Generic usage of script *showwin* is the following:
```
showwin "$WINDOW_NAME" "$OPEN_WINDOW_COMMAND"
```
Script will search for a windows with *$WINDOW_NAME* in title and make it active. If no appropriate window is found script will run command *$OPEN_WINDOW_COMMAND*.

## Which *$WINDOW_NAME* to use
List of opened windows can be got using command *wmctrl -l -x*:
```
$ wmctrl -l -x
...
0x04400010  0 Navigator.Firefox        masyamandev-UX360CA Options - Mozilla Firefox
0x04400267  0 Navigator.Firefox        masyamandev-UX360CA readme.md code - Google - Mozilla Firefox (Private Browsing)
```
Parameter *$WINDOW_NAME* is a *grep* regex. It's desirable to use expressions which can describe only one window.

## Use case 1: switch to a specific program
Let's bind hot key *Alt+F* to command:
```
showwin " - Mozilla Firefox$" "firefox"
```
This will search for a window with title " - Mozilla Firefox[end_of_string]" and switch to it. If no appropriate window is found then script will launch Firefox.
This example will switch to private window (or open new one) in Firefox:
```
showwin " - Mozilla Firefox (Private Browsing)$" "firefox -private-window"
```
If this script is binded to some other hot key (e.g. *Alt+P*) then it will be easily to switch to Firefox windows even if Firefox has not been opened yet.

## Use case 2: when more than one window is fit to regular expressions
Let's continue previous example with binded hot keys *Alt+F* and *Alt+P*. Assuming user is pressing *Alt+F*. If multiple windows of Firefox is open, then script will not have possibility to define which window is needed to switch to:
```
$ wmctrl -l -x
...
0x04400010  0 Navigator.Firefox        masyamandev-UX360CA Options - Mozilla Firefox
0x044002b9  0 Navigator.Firefox        masyamandev-UX360CA Ubuntu Start Page - Mozilla Firefox
```
By default window with smaller window id will be used. This window id is stored and will be used to switch to the same window unless it's closed.
It's possible to attach another matching window to the same hot key. To do it hot key command *showwinDetach* should be called before pressing the hot key. It will unassign window id from a hot key and assign active window. It's recommended to bind script *showwinDetach* to some hot key, e.g. *Alt+Backspace*.

Here is an example of usage of setup above:
1. Assuming that no Firefox application is run at the moment.
2. Pressing hot key *Alt+F* will open new Firefox window.
3. While working with other applications pressing *Alt+F* will switch to Firefox.
4. If second window of Firefox is open, pressing *Alt+F* will activate first window of Firefox.
5. To assign another Firefox window to hot key *Alt+F* you have to activate 2nd window, press *Alt+Backspace* and then *Alt+F*.
6. Pressing hot key *Alt+F* will open 2nd Firefox window.

## Use case 3: customizable attaching
It's possible to bind special hot keys which will be assignable to any window. To do this hot key should be bind to script *showwin* without second *$OPEN_WINDOW_COMMAND* argument. In this case parameter *$WINDOW_NAME* will work as hot key identifier. Here is an example of binding customizable hot keys:
```
Alt+1 -> showwin "CustomKey1"
Alt+2 -> showwin "CustomKey2"
...
Alt+0 -> showwin "CustomKey0"
Alt+Backspace -> showwinDetach
```
This will make hot keys *Alt+1* - *Alt+0* fully customizable, any window can be selected and attached to a hot key by pressing a hot key. Further pressing of a hot key will switch to attached window. 
To attach another window to a hot key is done in three steps:
1. Activate desired window.
2. Activate re-attaching script *showwinDetach* (*Alt+Backspace*). 
3. Assign a new window by pressing a hot key (*Alt+1* - *Alt+0*).


