# Inputpipe

### Version 0.5


## Introduction

Inputpipe is a client/server application for making any Linux input
device network-transparent. An inputpipe client runs on a computer with
some arbitrary input devices, forwarding information and status from
those devices to an inputpipe server. The server creates local input
devices that are for all practical purposes identical copies of the
devices being forwarded.

Configure in `config.mk`, build with `make`, install with `make install`. Nothing fancy there.

Inputpipe is licensed under the GNU GPL.

## Note
inputpipe doesn't seem to work with newer Linux kernels and/or devices.  
Atleast SteelSeries Rival 300 & Cooler Master QuickFire Rapid-I didn't forward under kernel 4.11.5 and I ended up using __usbip__, which works fairly better and is able to forward more than just HID devices over network.

So I recommend __usbip__ to you all as well :)

## Contents

_src/_: Source for inputpipe-server and inputpipe-client


## Server setup

(Requires Linux 2.6 or later and the 'uinput' kernel module)

The inputpipe server is the machine you wish forwarded input devices
to appear on. It uses the "uinput" kernel module to create the forwarded
input devices- therefore it requires Linux 2.6.x.

_IMPORTANT_:
inputpipe-server on its own has no security features. It is
intended mainly for use on closed networks. If you require
a little extra security you can run inputpipe-server in inetd
mode and use wrappers or inetd features to accept only certain
connections.

Even if you authenticate connections to inputpipe, note that
the data will not be encrypted! Don't use inputpipe for devices
that may be used to enter passwords.

That said, you can get started just by running "inputpipe-server" with
no arguments. For more advanced usage, see the --help text


## Client setup

(Requires Linux 2.4 or later and the 'evdev' kernel module)

Inputpipe clients can run on Linux 2.4 or Linux 2.6 machines, though
clients using Linux 2.4 will be less bandwidth-efficient and won't support
a few fun features like physical paths and "rumble" force feedback.

The inputpipe client needs to know what server to connect to, and where to
find input devices to forward. You can give it individual event devices,
you can give it categories of devices to forward, or any combination of the
two. In the case that inputpipe-client is forwarding a broad category of
devices, it will support hotplugging. New devices will automagically appear
on the server when plugged in, if they match the criteria given to
inputpipe-client.

Read the output of "inputpipe-client --help" for details, but here are some
quick examples:

1. Forwarding a single event device to the host "warzok":

 `inputpipe-client warzok /dev/input/event5`

2. Forwarding all joystick devices to warzok:

 `inputpipe-client -j warzok`

3. Forwarding all mice plus a single known input device to warzok:

 `inputpipe-client -m warzok /dev/input/event3`

Note that in example 1, inputpipe-client will exit if the device is removed
or warzok goes down. In the other examples, inputpipe-client will keep scanning
for new hotplugged devices. Also note that as long as the inputpipe server is
alive when inputpipe-client first connects, it can go down and come back up
without killing inputpipe-client - the client will retry failed connections.


## Internals

Inputpipe uses a simple but extensible binary protocol over TCP. Most input
traffic consists of thinly encapsulated and network-byte-order-adjusted
input_event structures, but inputpipe defines numerous other packet types
used for communicating a device's identity and capabilities. For full details
on the protocol, see `src/inputpipe.h`


## Compiling with musl

Run `make getheaders` and edit `config.mk` accordingly. `musl-gcc` wrapper
or gcc built with musl is required

## Contacts

* Micah Dowty <micah@navi.cx>
* Mark Vainomaa <mikroskeem@mikroskeem.eu>
