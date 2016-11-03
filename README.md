# Saving SSH keys in macOS Sierra keychain

## Source links
- https://openradar.appspot.com/27348363
- https://forums.developer.apple.com/thread/48794
- http://testequals.com/2016/09/09/macos-sierra-10-12-ssh-keys/
- https://www.reddit.com/r/osx/comments/52zn5r/difficulties_with_sshagent_in_macos_sierra/
- https://forums.developer.apple.com/thread/48794

## Problem
As described in detail on https://openradar.appspot.com/27348363, macOS/OS X till Yosemite used to remember SSH keys added by command `ssh-add -K <key>`.

Unfortunately this way no longer works and command `ssh-add -K` in macOS Sierra no longer saves SSH keys in OS's keychain. As Apple Developer stated: _"That’s expected. We re-aligned our behavior with the mainstream OpenSSH in this area."_

## Solution
There is a possible solution – after usage of `ssh-add -K <key>` (it's recommended to use absolute path of keys) call command `ssh-add -A` on every startup of macOS.

Just download this file directly:
```
curl -o ~/Library/LaunchAgents/ssh.add.a.plist https://raw.githubusercontent.com/jirsbek/SSH-keys-in-macOS-Sierra-keychain/master/ssh.add.a.plist
```
or create it with Lingon app (https://www.peterborgapps.com/lingon/):

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Label</key>
	<string>ssh-add-a</string>
	<key>ProgramArguments</key>
	<array>
		<string>ssh-add</string>
		<string>-A</string>
	</array>
	<key>RunAtLoad</key>
	<true/>
</dict>
</plist>

<!-- @@@@LingonWhatStart:ssh-add -A@@@@LingonWhatEnd -->
```

## Notes
If you have issues with `ssh-add: illegal option -- K` after using `ssh-add -K` command, you may use full path of the command `/usr/bin/ssh-add`.
