# edisyn
Synthesizer Patch Editor (Version 4)

By Sean Luke (sean@cs.gmu.edu)


## About

Edisyn is a synthesizer patch editor library written in pure Java.  It comes with two completed patch editors,
for the *Waldorf Blofeld* and the *Waldorf Microwave II* series (including the XT and XTk), but will ultimately 
have some more written for it as well as I get time.  Edisyn has no graphical interface editor system like 
Ctrlr, but it's designed to make the GUI pretty easy to write (well, for me anyway).

At present Edisyn *only runs on OS X* (well, I've only tried it on OS X, that's its target).  If you try running
it elsewhere, you're on your own.  We have confirmation that it runs fine under Linux.

This is Edisyn version 4, an early prerelease which doesn't feel very MacOS like yet, and which has lots of wires sticking out.  But it works!  Try it out on your synth!



## Installation

For the time being, to install Edisyn you need to do the following three things.

1. Install the Java JDK.
2. Place the file `libraries/coremidi4j-1.0.jar` in the directory `/Library/Java/Extensions`
3. Put file `jar/edisyn.4.jar` where you like (it's Edisyn's executable).


## Running

Double click on the file `edisyn.4.jar`

Edisyn should launch and either present you with a window asking what MIDI interface you want to use, or tell
you that there are no available MIDI interfaces, and that you'll need to work offline.


### The Editor Pane

The editor pane should be self-explanatory.  There are four tabs which together cover all of the parameters of 
the synthesizer.  The first tab, *Oscillators and Filters*, also contains an area called *Waldorf Blofeld* which
lets you set the patch name and category, bank, number, and Blofeld ID.   The bank, number, and ID are mostly
for saving out to sysex files: whenever you upload or download patch to/from the Blofeld, you'll be prompted
to revise those if necessary.

### The File Menu

* *New* creates a new editor pane, set to the default (the Blofeld's Init patch setting).  Note that only the frontmost editor pane will receive MIDI.  So if you for
some reason set up both editor panes with the same interface, then request MIDI from one pane, then quickly
switch to the other pane, you could in theory get the MIDI sent to the other pane.  So don't do that.

* *Load...* loads a sysex file into the existing editor pane.  It's called Load instead of Open because it 
doesn't open a new window.

* *Close Window* closes, well, you know, it closes, um, the window.

* *Save* and *Save As...* save to a sysex file.

* *Reset to Default* resets the window to display the Blofeld's Init patch setting.

* *Export Diff to Text...* saves out to a text file a line-by-line description of every parameter which is *different* 
from the default Init patch setting, plus the current value it's set to.  The patch value is either a string (in the
case of the Patch name) or a number from 0...127: this may not be that useful to you, but the parameter names might
be useful if you want to publish your patch on a sebsite and need to know which parameter settings to mention. 


### The MIDI Menu

Note that you can *send* a patch to the synthesizer and you can *write* a patch to the synthesizer.  The former
just temporarily updates the synth's current patch memory so you can play it.  The latter actually writes the 
patch to an address in the synth, replacing whatever is there.

* *Request Current Patch* asks the synthesizer to load the current patch memory into the editor.  Note that on
some machines (like the Waldorf Blofeld) when the patch is loaded, the bank and patch number are invalid and will
be reset to some defaults, which might be confusing!

* *Request Patch...* asks the synthesizer to load a specific patch into the editor.  If the synthesizer complies,
once the patch is loaded, Edisyn will then send the patch to the synthesizer.

* *Request Merge* asks the synthesizer to load a specific patch into the editor.  If the synthesizer complies,
then the patch is *merged* with the existing patch, meaning that some *percentage* of parameters in the existing
patch are replaced with the old patch.  Then Edisyn will then send the patch to the synthesizer.

* *Randomize* randomizes the editor's current patch, then sends it to the synthesizer.

* *Send Patch* sends the current patch to the synthesizer.  This isn't actually used much since other commands
send the patch automatically.

* *Write Patch...* writes the patch to a given location in the synthesizer.

* *Change MIDI* sets or updates the MIDI interface.

* *Disconnect MIDI* disconnects the MIDI interface.

* *Send All Sounds Off* sends the All Sounds Off message to all channels.

* *Send Test Note* sends a 1/2 second test note to the primary channel.


### The MIDI Interface

Edisyn makes up to three MIDI device connections.  The *Receiving Device* is the MIDI device from which we will accept
patches.  This is usually your synthesizer.  The *Sending Device* is the MIDI device to which we will send 
patches and parameter changes.  We'll also need a channel for the Sending Device so we can send test notes.

Optionally you can route your controller keyboard through Edisyn to play the sounds directly if you wish.  To do this,
the *Keyboard Device* is the MIDI Device of your controller keyboard.  You'll also specify an incoming keyboard
channel of course.  This can be set to "Any" for any channel (Omni).

### Sending and Recieving Parameters

If you change a widget in the editor, Edisyn will send the appropriate sysex command to the synthesizer to change it on
the synth as well.  Additionally, if you change a parameter on the synthesizer and it forwards a *sysex* command to Edisyn,
then Edisyn will update the appropriate widget in the editor.  At present Edisyn does't support CC commands from the
synthesizer (maybe later).  So (for example) on the Blofeld you'll need to change the machine to *send sysex* -- not CC only --
when changing parameters on the synth.

## Blofeld Bugs

These aren't Edisyn bugs: they're Waldorf synthesizer bugs that you should be aware of.

1. When you request the current patch, the synthesizer doesn't indicate which patch number or bank it was :-(.  So at present Edisyn
sets the default patch number and bank to A1.  Take note  of this before you accidentally overwrite your A1 patch with a write.


## Waldorf II/XT/XTk Bugs

These aren't Edisyn bugs: they're Waldorf synthesizer bugs that you should be aware of.

1. When you request the current patch, the synthesizer doesn't indicate which patch number or bank it was :-(.  So at present Edisyn
sets the default patch number and bank to A1.  Take note  of this before you accidentally overwrite your A1 patch with a write.

2. The XT (and likely the XTk and II) does not respond to nor does it send parameter changes for effect type.  So changing it on
the machine will not update Edisyn, and changing it in Edisyn will not update the machine (until you send or receive a patch).

3. Changing the arpeggiation triggers in Edisyn don't update then on the synth's scren, though they're
updated in memory internally.  Similarly, changing the name in Edisyn doesn't update the name in the
name edit screen on the synth, though it's changed internally.

4. The Overdrive Effect's Amp Drive parameter is weirdly biased towards "Stack" due to how it's encoded in Waldorf's sysex. 

## Caveats

To work around some bugs in OS X Midi and CoreMIDI4J, Edisyn's architecture at present does not let you
plug in new devices (or remove them) after Edisyn has been launched.  If you need to do so, restart Edisyn
(for now). 

Randomize isn't very useful right now.  I'm working on it.

Everything has to be sent via sysex for the moment: I don't have code written to make it easy to send CC or NRPN
if you wanted to build a patch which did that.

Popping up a new panel is slow.  Profiling suggests that the primary reason for this is that JComboBox construction
is slow.  So I can't get around it.

