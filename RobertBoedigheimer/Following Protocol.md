# Following Protocol

The clue:

> I'm sure I don't have a key logger uploading what I type, but... can you just check?

Additionally, there is a link to download a file. This is a pcap, or packet capture file. It was created by running [Wireshark](https://www.wireshark.org/) while a (simulated) keylogger was running. 

I used Fiddler, File -> Import Sessions, I chose "Packet Capture" and could see the individual request/responses where I then could see each unique key and find the complete flag.

![Fiddler Screenshot](./media/FollowingProtocol.png)