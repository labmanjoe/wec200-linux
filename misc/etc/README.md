## Sample 3-level access

This shows 3 files which would be for 3-tiered access, to run card readers for
this scenario, you would run at least 3 doorman processes with
different arguments:

### it lab

        doorman -l ITLAB -f cards -f cards.server -f cards.itlab 1.2.3.4

### server

        doorman -l SERVER -f cards -f cards.server 1.2.3.5

### general door

        doorman -l ENTRY -f cards 1.2.3.6




