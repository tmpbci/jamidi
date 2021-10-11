MOVED to https://git.interhacker.space/protonphoton/jamidi.git


Jamidi v0.1b 
By llstr, Sam Neurohack

LICENCE : CC NC

Midi and more exchanges over LAN/Internet. Now with ORCA live coding support !!

Imagine Bob own a TR 808 at home and Alice a nozoid OCS-2. They can control each other devices in a webpage or a midi controller.
If John has a mutliple encoders midi controller at home and want to control Alice and Bob devices, all changes made by John will be displayed to everyone and played by devices.

More you can use also a vcvrack complex patch to drive light fixtures, laser abstract generators, midi instruments, wherever they are...





# Jamidi features

- Jamidi vs rtpmidi ? rtpmidi is transport efficiency, but bound to midi specifications. Jamidi support midi cc and note but is more OSC style : you can add any type of "command".

- Jamidi is websocket based, so accept anything (webpage, home made client,...). 

- Jamidi doesn't broadcast audio. Use whatever solution you like. We use icecast and VLC to listen.

- Jamidi is experimental and nowhere safe. Network managment and security is not in jamidi scope. Some ideas : All computers can be on the same encrypted vpn (tinc, zerotier,..). The server can run on a VPS and all client connect to it. The server is at home, closer to midi devices,...

- Look at jamidi.json for set your configuration. 2 element types : midi device and IP servers




# ORCA livecoding support.

Livecode some instrument somewhere else. https://github.com/hundredrabbits/Orca

On computer linked with desired midi instrument :

python3 main.py


Livecoder should configure ORCA and use it :

CTRL K ip:ipaddress
CTRL K udp:udport (default is 8083)

MIDI CCs base 36 use ;cmnd

        m : midi channel 0-F (0-15)
        n : number       0-Z (0-35)
        d : data         0-Z will output (d/36)*127


MIDI NOTES use ;nmonv 

        m : midi channel 0-F (0-15)
        o : octave       0-8    
        n : Note         A-G. For note with # : a-g
        v : velocity     0-Z will output (v/36)*127



# How it work : Websocket


Websocket transport a string like :

/ocs2/cc/2 0

or 

/tr808/note/1

cc   	: is a "command". Currently cc, reset (highly specific to nozoid synthetiser). You can add any tyoe of "command".

ocs2 	: is a "device", that must be described, follow examples in jamidi.json. Here the Alice OCS2 will reveive broadcasted midi informations. 

/ocs2 and /tr808 changes made by John will be displayed to everyone and played by devices.


Websocket default port is 8081 but one can change.




#  How it work : Server

Will receive all "commands" from all clients, forward them to local devices and broadcast them too.

servername 		: 'local', 'llstrvpn'. Servers (IP, port,...) must be described in jamidi.json

How to Run python server  :

python3 main.py

Options : 

  -h 		            Show this help message and exit

  -s SERVERNAME 		Servername: 'local', 'llstrvpn' (local by default)

  -d DEVICENAME			Midi device for incoming ORCA via OSC (mmo3 by default)

  -nocurrent        Do not send all current CC values to all new client   (enabled by default)

  -nobroadcast      Do not broadcast all incomings commands to all client (enabled by default)

  -noreset          Do not broadcast all incomings commands to all client (enabled by default)

  -nothrough        Disable the builtin midithrough from any midi IN to --device enabled by default

  -verbose				  Enable debug mode (disabled by default)



#  How it work : Clients

Send midi over network to Jamidi. Can be webpages or midi instrument/software. Multiple clients types is supported.

How to Run python client :

python3 client.py


Options :

servername : Remote server 'local', 'xrkia' ('local' by default). Servers must be described in jamidi.json

-s servername 		servername: 'local', 'xrkia' ('local' by default)

-nodefault            Do not send reset values to local device a startup.


Some "rules" are available. Say you want all network incoming midi CC 1 channel 0 goes to a specific device on channel 3 CC 48. Rules must be described in rules.json

Each rule may happen at all time or only during a "song".





# Webpages examples

1/ Run :


python3 main.py


2/ In a browser open 2 instances of each of these :


indexaurora.html is a three rotating encoders example.

mmo3.html and ocs2.html demo to control 2 real life nozoids.


3/ Each aurora pages talk to each other. Each mmo3 to each other and so on..


These html pages will work with local jamidi server, but they can't guess your online configuration. You need to modify IP and port in line :

var LJ = 'ws://127.0.0.1:8081/'



# Midi instrument usage

1/ You need to edit jamidi.json and follow ocs2 example.

2/ Create a client/webpage that will send /ocs2/xxx

3/ Run :

python3 main.py





# Install

You need python3 and pip3 

sudo apt-get install python3-pip 

pip3 install python-rtmidi (sudo apt install libasound2-dev, sudo apt install libjack-dev)

pip3 install mido

pip3 install numpy


for Websocket client :

Download .tar.gz file here : https://pypi.org/project/websocket_client/#files

Decompress (tar -xvf)

python3 setup.py install 
