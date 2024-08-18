# iSH-IP-stats

## Purpose

This provides utilities to retrieve IP information directly within iSH, same to what is available in iOS Settings. Since IP information isn't directly available in iSH, these utilities rely on iOS Shortcuts triggered when the network connects or disconnects to maintain a file with the current IP that is accessible to iSH.

## Tools Provided

- **`network-check`**: Displays the network connection status and verifies if DNS is functional.
- **`ip-local`**: Retrieves and displays the local IP address.
- **`ip-public`**: Retrieves and displays the public IP address (if connected to the internet).
- **`ip-status`**: Displays both local and public IP addresses (if connected to the internet).

## Dependencies

### Accessing the IP File in iSH

To access the file containing the current IP in iSH, the appropriate iOS folder must be mounted. If you have already mounted folders, this step is complete. Otherwise, mount the desired iOS folder:
    
    mount -t ios . /mnt

Choose the iOS folder where the IP file will be stored, or select a parent directory above it.

### iOS Shortcuts Setup

1. **Create a Shortcut to Write the Local IP to a File:**
   - Name the shortcut, e.g., `SetLocal-IP`.
   - Add a "Wait" action (set to ~3 seconds). This delay allows time for the device to acquire an IP address after connecting.
   - Add a "Get Current IP Address" action and set it to retrieve the local IPv4 address.
   - Add an "Append to Text File" action, using "Current IP Address" as input. Select the folder (within iSH's mounted hierarchy) where the file should be saved, and specify the filename. Ensure "Make New Line" is enabled.
   - Run the shortcut manually once to create the file.

2. **Trigger the Shortcut on Network Connect:**
   - Go to "Automation" in the Shortcuts app.
   - Add a new "Personal Automation".
   - Choose "Wi-Fi" and select "Any Network" with the event "Network Joined". Enable "Run After Connection Interruption" and "Run Immediately".
   - In the next step, assign the shortcut created in step 1 to run on connection.

3. **Remove the IP File on Network Disconnect:**
   - Add an action to "Get File from Foler" the folder containing the local IP file.
   - Add an "If" condition: "File has any value".
   - Add a "Delete Files" action targeting the same IP file.

4. **Trigger the Removal Shortcut on Network Disconnect:**
   - Create another automation like the connect automation, but set it to trigger on network disconnection. Assign the removal shortcut to this event.

## Multiple Devices Running iSH

If you run iSH on multiple devices, avoid storing the IP file on iCloud with the same file name on each device, as this can cause syncing issues. Each device will show the most recent IP address, regardless of which device generated it.

## Deployment

1. Locate the IP file by checking the mount point in iSH:
    
    `find /mnt | grep ip_local`

   The filename doesn't need to be complete, as long as it's unique enough to locate the file.

2. Copy the full path to the IP file.

3. Run the `deploy` script in this folder:

    ./deploy

   The first time you run it, it will prompt for the IP file. Paste or type the full path to the file and hit Enter. The filename is saved to `~/.local_ip_file`.
