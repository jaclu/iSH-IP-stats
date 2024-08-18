# iSH-ip-stats

## Purpose

You can find what IP you are using in iOS-Settings, theese are just some
shorthands to get the same info directly inside iSH.

## tools provided

- network-check - displays connection status and if DNS is working
- ip-local - diplays local IP
- ip-public - displays public IP (if connected to Internet)
- ip-status - displays both (if connected to Internet)

## Dependencies

### iOS Shortcut

#### A shortcut that writes the current ip to a file.

- Create a shortcut and give it a name like SetLocal-IP
- Add a "wait", set it to something like 3 seconds. Without this you
will often end up with an empty file, since this shortcut is triggered
imeditally on connection, and it typically takes a moment for the
device to recivr an IP
- Add a "Get current IP address" Set it to local IPv4
- Add a "Append to Text File"  Use "Current IP Adress" Select a Folder
where to save it, and select a file name. This folder must be within
the file hierarchy you gave iSH access to with the mount command. Make
sure to enable "Make New Line"!
- Click play to run it once, in order for the file to be created.

The ip-tools inside iSH just read the last line of this file, but over
time it will slowly grow, so you might eventually want to remove it,
since next time you connect it will be recreated.

#### Trigger the first Shortcut to run on Network Connect

- Select Automation
- Add a Personal Automation
- Select Wi-Fi, for Network select "Any Network" then select "Network
Joined". Enable "Run After Connection interruption" and select "Run Immeditally"
- Click "Next" and select the shortcut logging your IP rceated above -
done!

#### Remove the file when disconnecting from Network

- Add "Get File from Folder" Select the file you defined above
- Add "If" Select File has any value
- Add Delete Files, select the same file again

#### Trigger the second Shortcut to run on Network Connect

More or less the same as the connect automation, but in this case evnt is
when disconnectig. Run the removal shortcut.

### Access to the file with current ip

inside iSH do: `mount -t ios . /mnt` and then select what iOS folder to
make accessible for iSH, either the folder where the IP-number is
stored or someplace above.

## Multiple devices running iSH

If you use iSH on more than one device, it is simpler if this ip
number file is not stored on iCloud, since then it would be synced and
nodes will always show the last connected IP-number, regardless of
from which device it was generated.

## Deployment

Find the file with the IP number, check the mount point you selected
and do something like: `find /mnt | grep ip_local` It doesnt need to
match the full file name used, as long as it unique enough to find
it.

Copy the full path found to the clipboard, or remember it :)

run `./deploy` inside this folder. First time it will prompt you for 
the file provided by the Shortcut. Paste or type the filename you
found above. This filename is saved to `~/.local_ip_file`
