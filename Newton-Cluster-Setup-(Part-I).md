# SSH Newton Setup
Author: [@brandons209](https://github.com/brandons209)


This short guide will get your SSH keys decrypted and your environment setup so you can SSH to Newton, and use SCP/SFTP to transfer files to/from Newton.

This guide assumes:

1. You setup your UCF VPN and it is connected.
2. You can open a PowerShell/Terminal prompt.
  - [Click here for a cheat sheet for Linux Terminal commands](https://cheatography.com/davechild/cheat-sheets/linux-command-line/)
  - [Alternate Cheat Sheet](https://phoenixnap.com/kb/wp-content/uploads/2020/02/linux-commands-cheat-sheet-phoenixnap.pdf)
  - Remember, you can use the `TAB` key to auto complete commands in your Terminal.
    - Some of these, like `cd` and `ls` work in Windows PowerShell as well.

Please click the link below to the OS you will be working from regularly. I highly suggest using any Linux distro (Ubuntu would be the easiest to setup) as your regular work environment, as setting up and maintaining programming environments in Windows is a hassle at best and a nightmare at worst. **You can also use Windows Subsystem for Linux**, which gives you access to a full Debian Linux system through the command line. Check it out [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10) if you want to set it up.

*However* for the purposes of this tutorial any OS will work mostly the same.

## Windows 10 Setup
#### If using macOS or any Linux distro, skip this section.
#### If you're using Windows 8, you probably should upgrade to 10 or use Linux.
#### If you are using an older Windows OS, you shouldn't be, as they are no longer supported (R.I.P Windows 7)

### Installing OpenSSL
This will allow us to decrypt the Newton RSA keys in order to log into Newton, as Newton does not use password authentication.

1. Go [here](https://slproweb.com/products/Win32OpenSSL.html), scroll down, and install the `Win64 OpenSSL Light` version by clicking the **EXE** hyperlink and following the install process.

### Activating OpenSSH
This is the standard SSH client to connect to SSH servers. Windows 10 (after build 1809) comes with OpenSSH built it, but it is disabled.

1. Open an administrative PowerShell prompt by using the hotkey `CTRL+X` and selecting the PowerShell (Admin Prompt) option.


2. Run this command: `Add-WindowsCapability -Online -Name OpenSSH.Client`


3. Your output should be:
```bash
Path          :
Online        : True
RestartNeeded : False
```

If it isn't, refer to [this guide](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse).

**Important Note:**    
If you installed choclatey/MinGW/Cygwin on your computer, by default it'll try to use those program's OpenSSH installation, and this will cause pathing issues to Window's OpenSSH. You'll need to edit your system's PATH variable to enforce it to use Window's OpenSSH installation. At this point, it would be difficult to write something comprehensive as your environment will be different from mine. My recommendation
is to follow [this](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) guide on how to edit your PATH variable, and move the `C:\Windows\System32\OpenSSH\` to the bottom of the list and save.

## Decrypting RSA Keys
If you are Windows, open a PowerShell prompt now, on macOS/Linux open up your Terminal.

1. Extract your keys from the zip archive.


2. Navigate in your terminal to the folder where your keys are stored, using the `cd` command. **Hint:** on Windows, your Desktop folder can be reached by typing `cd C:\Users\<username>\Desktop`, replacing <username> with your account's name.


3. You should have a text file named something like: `username_passphrase_1.txt` Open it using a text editor or in the command line using the `cat <filename>` for Linux/macOS and `type <filename>` for Windows PowerShell, replacing `<filename>` with the passphrase filename. **Copy the content of the file to your clipboard.**


4. Use openssl to decrypt the key. The key file will be a file with no extension (not the .txt, .ppk, or .pub file): `openssl rsa -in <key file> -out <key file>_decrypted`
  - Replace `<key file>` with the name of the key file. The `-out` tag specifies the name of the output file, in this case the decrypted key file. Just name it whatever the original name and add `_decrypted` to the end of the name.
  - The program will ask you to enter the password for the RSA key file. Paste what you copied from step 3.


5. Move the decrypted keyfile somewhere safe on your computer. I recommend moving it to the `.ssh` folder in your home directory. The rest of the tutorial will assume you moved it to your `.ssh` directory. **(you may need to create this folder using `mkdir`)**
  - Windows: `mv <key file>_decrypted C:\Users\<username>\.ssh\` replacing `<username>` with your account's username.
  - Linux/macOS: `mv <key file>_decrypted ~/.ssh`


6. Your key file is decrypted and ready to be used to log into Newton!

## Setup Newton Configuration
Now we need to setup a configuration file so we can easily log into Newton from the command line.

1. Open a PowerShell/Terminal and cd to your `.ssh` directory.
- Windows 10: run `cd C:\Users\<username>\.ssh` replacing <username> with your username of your account.
- Linux/macOS: run `cd ~/.ssh`


2. Now, edit the `config` file.
  - Windows 10: run `notepad.exe .\config` **Click yes when it asks you to create the file**
  - Linux/macOS: run `nano config`


3. Add in this to your config file (both Windows and Linux/macOS):
```
Host            newton
HostName        newton.ist.ucf.edu
User            <newton username>
IdentityFile    ~/.ssh/<key file>_decrypted
```

The IdentityFile should point to where ever your decrypted key is stored. In this case it is stored in the `.ssh` directory.

\<newton username> is the username for your Newton account. It should be the `crcvreu` plus some numbers at the beginning of your key file name.

4. Save the file.
  - Windows: use `CTRL+S` to save, exit out of notepad, then run this command in PowerShell to rename the file properly: `mv config.txt config`
  - Linux/macOS: use `CTRL+X`, press `y`, then press `Enter`.


5. Now you are ready to log in!

## Log into Newton
Remember to connect to the UCF VPN.

In PowerShell/Terminal, run `ssh newton`. You will be asked if you want to connect, type `yes` and press enter. Then you should be logged into newton!
