# Commands

```shell
# Print current directory path
pwd

# Change directory
cd [PATH]

# Open current directory in finder
open .

# Open a specified file in current directory with a specified app
open [FILE_NAME] -

# Open a file at a path
open [PATH]/[FILE_NAME]

# Open with an app
open FILE_NAME] -a [APP_NAME]

# Line continuation, use /
echo 1 2 3 \
4

echo 1 \
&& echo 2

# Line end, use &&
echo 1 &&
echo 2

# View all active processes
top

# View file contents
cat / path / to / file

# Copy contents of folder to another path
ditto -V ~ / source / folder / ~ / new / folder /

# Show all files, including hidden
defaults write com.apple.finder AppleShowAllFiles -bool TRUE && 
killall Finder

# Selectively display some hidden files
chflags nohidden / Library / specify the path to the desired folder

# Download file
curl -O [URL_OF_FILE]

# Keep mac awake
caffeinate

# Keep awake for certain amount of time
caffeinate -t number of seconds

# Play tetris
Emacs
# Fn && F10 together

# Mac app sound like a phone when plugged into power
defaults write com.apple.PowerChime ChimeOnAllHardware -bool true; open /System/Library/CoreServices/PowerChime.app

# Change screenshot location
defaults write com.apple.screencapture location ~/your/location/here &&
killall SystemUIServer

# Set new default name for screenshots
defaults write com.apple.screencapture name "New Screen Shot Name" && 
killall SystemUIServer

# Change format of screenshots
defaults write com.apple.screencapture type jpg

# Kill dashboard
defaults write com.apple.dashboard mcx-disabled -boolean TRUE &&
killall Dock

# Add a spacer in dock
defaults write com.apple.dock persistent-apps -array-add '{"tile-type"="spacer-tile";}' &&
killall Dock

# Download browser history
sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* 'select LSQuarantineDataURLString from LSQuarantineEvent'

# Destroy browser history
sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* 'delete from LSQuarantineEvent'

# Shutdown mac
sudo shutdown -h now

# Restart mac
sudo shutdown -r now

# Delete files from mac trash and freespace securely
diskutil secureErase freespace 3/Volumes/hard-drive-name 

# Revoke access to contacts
tccutil reset AddressBook

# Revoke access to location data
tccutil reset CoreLocationAgent

# Get a list of all services
launchctl list

# Reschedule time machine backups
sudo defaults write/System/Library/LaunchDaemons/com.apple.backupd-auto StartInterval -int 1800

# Check for updates daily
defaults write com.apple.SoftwareUpdate ScheduleFrequency -int 1

# Power management settings
pmset -g
```


# IP Address Lookup
```shell
ping example.com
dig A example.com
nslookup example.com
host example.com
whois XX.XX.XX.XX
nmap target --script whois-ip
``` 