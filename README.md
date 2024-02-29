## WEC-200 wiegand-to-ethernet door controller

This is a quick-and-dirty perl script for managing a WEC-200
wiegand-to-ethernet converter box from a linux host.  It will read a
file with a list of valid access card numbers, and open the door when
those cards are scanned.

NOTE: There is some support in the script for a MaCaPS wiegand to
serial converter board.

### notes

The script does not automatically restart if connectivity is lost to
the device; an external supervisor program such as `runit` should be
used to ensure the door process stays running.

The script assumes the following directories exist and are writeable
by the user the script is running as:

 * `/var/run/doorman` -- PID files go here
 * `/var/log/doorman` -- LOG files go here, a new log file is started at the beginning of each month

The script assumes that the file(s) containing the card data are
readable.

It is assumed that anyone with access to the machine and user hosting
the script is able to control the doors, so there is no special
security built-in beyond the normal unix permissions model.  It is
strongly recommended to run the WEC-200 converters on a dedicated
network segment; I once experienced an ARP-storm taking the converters
offline, and immediately isolated them to a specific network just for
the doors.

### options

  * `-r` input 1 is a "real" request to exit switch in the doorhandle (otherwise, it's a motion sensor)
  * `-i` invert the sense of input 1 (request to exit / motion sensor input)
  * `--duration-unlock <secs>` how long to keep door unlocked after scanning valid card (default: 3)
  * `--durattion-remote-unlock <secs>` how long to keep door unlocked from a remote request (default: 8)
  * `-o <label>` add other doors to use with the "door handle hack" (see below)
  * `-l <label>` give a label to the door (default `TEST`)
     * if label starts with `TEST`, log to stdout
     * otherwise all readers log to `/var/log/doorman/door.YYYY.MM.log` (file automatically rotates across month boundary)
     * log lines are prefixed with the time and the door label
  * `-f <path>` add a file to read with card numbers for this door (can be used multiple times)
     * if path is absolute, use it
     * if path is not absolute, prepend `/etc/doorman/` to the path
     * if *NO* `-f` options are passed, default to reading `/etc/doorman/cards`
     * default file must be specified if it should be used in addition to the `-f` specified files

After all options should be the IP address of the WEC-200 board to
control; default is port 1001 but a different port can be specified
using ip:port notation.

If the "IP Address" starts with `/dev` the script will connect to the
specified serial port, and it is assumed that the device is a MaCaPS
serial device instead.

### signals

 * `SIGUSR1` -- open door with lots of beeps (remote request from another system)
 * `SIGUSR2` -- open door from another door
 * `SIGALRM` -- beep the reader
 
### door handle hack

If you have an office where someone who is already in the office may
want to go out into a hallway and immediately bak in through a
different door, you may want to enable the doorhandle hack "targeting"
another door(s).  This is enabled by the `-o <label>` option.  If the
door is open, and the interior doorhandle (REX switch) is released,
and then operated again, this signals a beep to the "other" door, and
it will open shortly.  (This uses `SIGALRM` and `SIGUSR2` as described
above.)

Note if there are multiple doors "reachable" you can pass the
option multiple times, and depressing the door handle will "select"
(and beep) each "target" door in sequence -- if you cycle through them all (even if there is
just one other door) it will cancel opening any doors (and beep the
current reader to confirm this).

Using the `-o` option is not recommended on doors used by guests to
exit an office, and also not recommended if the "target" door is not
fully visible from the "source" door.

### configuring devices

See [misc](misc/) for information about configuring the devices using
the `wecconfig` tool in that directory.

