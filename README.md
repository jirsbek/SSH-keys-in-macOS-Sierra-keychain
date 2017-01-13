# Saving SSH keys in macOS Sierra keychain

## Source links
- https://openradar.appspot.com/27348363
- https://forums.developer.apple.com/thread/48794
- http://testequals.com/2016/09/09/macos-sierra-10-12-ssh-keys/
- https://www.reddit.com/r/osx/comments/52zn5r/difficulties_with_sshagent_in_macos_sierra/
- https://developer.apple.com/library/content/technotes/tn2449/_index.html#//apple_ref/doc/uid/DTS40017589

## Problem
As described in detail on https://openradar.appspot.com/27348363, macOS/OS X till Yosemite used to remember SSH keys added by command `ssh-add -K <key>`.

Unfortunately this way no longer works and command `ssh-add -K` in macOS Sierra no longer saves SSH keys in OS's keychain. As Apple Developer stated: _"That’s expected. We re-aligned our behavior with the mainstream OpenSSH in this area."_

## Solutions


### Solution 1 (recommended)
As Apple updated it's [Technical Notes] (https://developer.apple.com/library/content/technotes/tn2449/_index.html#//apple_ref/doc/uid/DTS40017589), since 10.12.2 macOS includes version 7.3p1 of OpenSSH and it's new behaviors.

In `~/.ssh` create `config` file with the following content:

```
Host * (asterisk for all hosts or add specific host)
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile <key> (e.g. ~/.ssh/userKey)
```

### Solution 2
After usage of `ssh-add -K <key>` (it's recommended to use absolute path of keys) call command `ssh-add -A` on every startup of macOS.

Just add .plist with the following content to the path `~/Library/LaunchAgents/`:

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

Or create the file with the Lingon app (https://www.peterborgapps.com/lingon/).

Or use curl command to download the .plist file to the stated path:

```curl -o ~/Library/LaunchAgents/ssh.add.a.plist https://raw.githubusercontent.com/jirsbek/SSH-keys-in-macOS-Sierra-keychain/master/ssh.add.a.plist```

#### Notes
If you have issues with `ssh-add: illegal option -- K` after using `ssh-add -K` command, you may use full path of the command `/usr/bin/ssh-add`.
