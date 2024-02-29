## misc directory

This directory has various useful files.

### wecconfig

Besides the file
[../doc/manual-re-ip-of-converter.txt](../doc/manual-re-ip-of-converter.txt),
there is also a tool `wecconfig` in this directory that will locate
and configure any WEC devices on a network.

To use, run `./wecconfig` passing the broadcast address of the network with the WEC devices (which will default to 255.255.255.255, and thus may not select the correct network on multihomed hosts).

It will give output like this:

          1: 172.31.253.30:65535 => A0.200.0.0.31.79/1001/N**M*/**/GIGA-TMS/WEC200-01
          2: 1.0.0.1:65535 => A0.200.0.0.199.199/1001/N**M*/**/GIGA-TMS/WEC200-01
          2   2 Which? 

Each number is a unique device, which reports its IP address and the
response.  The response from the device includes the MAC address in
"dotted decimal" notation.  In this example device 2 is a "fresh"
device from the factory.

Typing a number and hitting return will then prompt for the IP address to assign to the device.

Note that there does not seem to be a way to configure a netmask or a
gateway; all WEC-200 and the controller machine(s) should be on the
same LAN segment.

### /etc/doorman

Shows sample cards files for a "3-tier" access system.  See [doorman](doorman/) for more info.

### fakeconverter

This is a very very crude approximation of a WEC-200 converter using
netcat; it allows a basic test of the script.  Note that it does not
run on the same port number as the real converter (due to the standard
port < 1024 "privileged" convention).

