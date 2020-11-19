# reaper-ableton-importer

This is a fork of the Ableton Live Project importer for Reaper, originally written by n997 and uploaded to cockos.com (https://forum.cockos.com/showthread.php?t=234839)

Original comments/instructions are as follows (and are subject to change along with this script) - those are copied explicitly from the original thread created by n997:

I have to convert a large number of Ableton Live projects to REAPER. They're plugin-based (MIDI and automation, not much audio, so AATranslator can't help) with data entirely in arrangement (nothing in Live's session view).

I'm aware of following manual way to transfer plugin, MIDI and automation data from Live's arrangement to REAPER:

In Live:
1) save .fxp presets per track (replacing internal plugins with VST alternatives as needed)
2) consolidate each MIDI track to length of whole project
3) convert Live-native automation into MIDI CCs, noting which CC relates to which plugin parameter
4) export each MIDI track to its own MIDI file


In REAPER:
1) create tracks and insert plugins
2) import .fxp presets
3) import MIDI files
4) convert MIDI CCs to REAPER-native envelopes for plugin parameters
5) trim project-length MIDI to items and loops, if needed

Obviously that is extremely time-consuming and mind-numbing, even with some scripted aid in REAPER such as macros for inserting plugins.
So I hope to automate more of the process by direct conversion.

Ableton Live sets are gzipped XML, while REAPER's project files are plaintext. Hypothetically, it's possible for a REAPER script to read an unpacked .als, get data from it, then either rewrite it into .RPP or create elements in REAPER as the data is being read.

A quick check of .als and .RPP contents suggests that converting MIDI tracks, items and MIDI notes is mostly a matter of reading/writing tags and converting units. Same is likely for the envelopes. Data of VST patches is encoded differently: hexadecimal in .als and Base64 in .RPP, but should be possible to convert as well.


Before I attempt this as a beginner-level scripter - most likely in Lua - I have to ask:
Has anyone tried writing such a conversion script before, or are there any existing tools out there that can do this?

Again, according to AATranslator website it does not convert either plugins/fx or midi, so I haven't tried that.



UPDATE 2020-07-27:
---------------------------------------------------
It turned out that reading XML from an un-gzipped .als file and recreating elements/data in REAPER in real time was the sensible way to realize this.

I've now written a proof of concept, Windows-only Lua script for importing [from Live set's Arrangement into REAPER] most types of MIDI clips and envelope data, as well as inserting plugins (but not their settings) and replicating track routings.

The script also replaces (imperfectly) some native Live plugins with REAPER or VST equivalents:
Live Compressor: ReaComp
Live Gate: ReaGate
Live EQ8: ReaEQ
Live Reverb: Dragonfly Hall Reverb VST
Live Simple Delay: Spaceship Delay VST
Live Ping Pong Delay: Spaceship Delay VST

It was tested with REAPER v6 up to 6.13 and Live sets made with Ableton Live 10.0.5.

How to use:
0. unzip the attachment, it should contain a .lua file with name beginning with "parse Ableton Live als"
1. load that script in REAPER (Actions > "Show action list" > "New Action" > "Load ReaScript", select the .lua file
2. make a copy of your Live set .als file, rename its extension to .gz (for example "Test.als" to "Test.gz")
3. unpack the .gz file with any gzip unpacking utility (such as Universal Extractor)
4. run the .lua script in REAPER; it will ask for the un-gzipped Live file and then try to rebuild the Live project in REAPER.


I am not a professional programmer and this script was written for personal needs, so I cannot provide support or further development. If you need to change/add something in this script, do it yourself or ask a better skilled scripter.

The only thing I ask is that if anyone develops forks of this script, they are also shared with REAPER community.

Legal disclaimer: This script is provided as open source and free of charge, without any guarantees of functionality.


UPDATE 2020-09-29, Proof-Of-Concept version 6
------------------------------------------------------------------
- should now support importing Live's audio clips of MP3 source files
- script will try to apply warp settings (direct conversion from Live's WarpMarker data to REAPER's Stretch Marker data)
