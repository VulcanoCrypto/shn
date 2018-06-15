# Bulwark Secure Home Node Installer

## Table of contents
- [Requirements](#requirements)
- [Funding your Masternode](#funding-your-masternode)
- [Generating your Masternode Private Key and Output](generating-your-Masternode-Private-Key-and-Output)
- [Connecting your SHN to your network](#connecting-your-shn-to-your-network)
  * [Connecting via ethernet](#connecting-via-ethernet)
  * [Connecting via Wi-fi](#connecting-via-wi-fi)
    + [Monitor & Keyboard](#monitor---keyboard)
    + [microSD card reader](#microsd-card-reader)
- [Finding your SHN on your network](#finding-your-shn-on-your-network)
  * [Windows](#windows)
  * [macOS](#macos)
  * [Linux](#linux)
- [Installation](#installation)
- [Updates](#updates)

## Requirements
To connect your node, you need either a network router with a free RJ-45 port and an ethernet cable or a router running a 2.4Ghz Wi-fi network. If you want to connect via Wi-fi, you will also need either a monitor that supports HDMI (along with a HDMI cable) and a keyboard, or a microSD card reader that works with your computer.

## Funding your Masternode

* First, we will do the initial collateral TX and send exactly 5000 BWK to one of our addresses. To keep things sorted in case we setup more masternodes we will label the addresses we use.

  - Open your BWK wallet and switch to the "Receive" tab.

  - Click into the label field and create a label, I will use MN1

  - Now click on "Request payment"

  - The generated address will now be labelled as MN1 If you want to setup more masternodes just repeat the steps so you end up with several addresses for the total number of nodes you wish to setup. Example: For 10 nodes you will need 10 addresses, label them all.

  - Once all addresses are created send 5000 BWK each to them. Ensure that you send exactly 5000 BWK and do it in a single transaction. You can double check where the coins are coming from by checking it via coin control usually, that's not an issue.

As soon as all 5k transactions are done, we will wait for 15 confirmations. You can check this in your wallet or use the explorer. It should take around 30 minutes if all transaction have 15 confirmations

## Generating your Masternode Private Key and Output

Generate your Masternode Private Key

In your wallet, open Tools -> Debug console and run the following command to get your masternode key:

```bash
masternode genkey
```

Please note: If you plan to set up more than one masternode, you need to create a key with the above command for each one. These keys are not tied to any specific masternode, but each masternode you run requires a unique key.

Run this command to get your output information:

```bash
masternode outputs
```

Copy both the key and output information to a text file.

Close your wallet and open the Bulwark Appdata folder. Its location depends on your OS.

* **Windows:** Press Windows+R and write %appdata% - there, open the folder Bulwark.
* **macOS:** Press Command+Space to open Spotlight, write ~/Library/Application Support/Bulwark and press Enter.
* **Linux:** Open ~/.bulwark/

## Connecting your SHN to your network
To connect your home node, you have two options: ethernet and Wi-fi.

### Connecting via ethernet
Simply plug in an ethernet cable running to your router into the Raspberry Pi before you connect the power cable. Once you've done that, proceed to [Finding your SHN on your network](#finding-your-shn-on-your-network).

### Connecting via Wi-fi
To set up Wi-fi, you can either connect your SHN to a monitor and keyboard **OR** use a microSD card reader.

#### Monitor & Keyboard
Connect your monitor to the Raspberry Pi with an HDMI cable and plug in a USB keyboard. Connect power to the Raspberry Pi, wait for it to boot up, then log in with the default credentials - user "pi", password "raspberry"

Once you are logged in, run the command
```
sudo raspi-config
```
You will see a graphical interface. First, select "Network Options", then "Wi-fi". In the following screens, select your country (this is necessary in order to use the correct frequencies), then enter your Wi-fi SSID (it's name) and the password. Once you are done, select "Finish" and restart your Raspberry Pi. Then proceed to [Finding your SHN on your network](#finding-your-shn-on-your-network).

#### microSD card reader
Put the microSD card from the Raspberry Pi into your card reader and connect it with your computer. In the root directory of the SD card, create a text file called _wpa_supplicant.conf_ and add the following text to it:
```
country=YOURCOUNTRYCODE
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev update_config=1
network={
       ssid="YOURNETWORKSSID"
       psk="YOURNETWORKPASSWORD"
       key_mgmt=WPA-PSK
    }
```

Replace _YOURCOUNTRYCODE_ with the [two letter ISO code](https://en.wikipedia.org/wiki/ISO_3166-1#Current_codes) of your country, _YOURNETWORKSSID_ with the name of your wireless network and _YOURNETWORKPASSWORD_ with the password of your wireless network (the password will be encrypted during the installation). Eject the SD card, put it back into your Raspberry Pi and connect the power cable, then proceed to [Finding your SHN on your network](#finding-your-shn-on-your-network)

## Finding your SHN on your network
Next, you need to find the IP address your SHN has been assigned by your router. How you do this depends on your operating system.

### Windows
Download the [Adafruit Pi Finder](https://github.com/adafruit/Adafruit-Pi-Finder/releases) for Windows and run it. It will detect your Raspberry Pi and allow you to connect by clicking the "Terminal" button. Proceed to [Installation](#installation).

### macOS
Open Terminal.app and run the following command:
```
ping raspberrypi.local -c1 | head -1 | awk -F " " '{print $3}'
```
You should see a single line containing the IP address of your home node.

If you don't get an address, use the Linux command below.

### Linux
Open a shell and run the following command:
```
arp -na | grep -i b8:27:eb | head -1 | awk -F ' ' '{print $2}'
```
You should see a single line containing the IP address of your home node.

## Installation
Now you're ready to install! Using [Putty](https://www.putty.org/) or a terminal, connect to your SHN using the address you found in the last step and the username "pi" - the default password is "raspberry" - don't worry, we will change this in a bit.

Once you are logged in, run this line:

```
bash <( wget -qO - https://raw.githubusercontent.com/bulwark-crypto/shn/master/prepare.sh )
```

The installer will prepare some things, then ask you to change your password. After that, your Raspberry will reboot.
Wait for a minute, log into your Raspberry again, then run this command:

```
sudo bash shn.sh
```

Now the Secure Home Node will be installed. After a while, you will see the following line:

```
I will open the getinfo screen for you in watch mode now, close it with CTRL + C once we are fully synced.
```

Then you will see the status of bulwarkd syncing. Once the sync is complete (when the number of blocks displayed is up to the current block height), press `Ctrl+c`
to finish the installation. You will be shown some information, among that the configuration line you need to add your your _masternode.conf_ on your local wallet. Press Enter to restart one more time.

While the Raspberry Pi is rebooting, add the line you got from the script to _masternode.conf_, restart your wallet, open the debug console and start your masternode with the command

```
startmasternode alias false <mymnalias>
```

where _<mymnalias\>_ is the name of your masternode, TORNODE by default.

Congratulations, you're done!

## Updates
To update your Homenode to the newest version of the Bulwark Protocol simply paste the following line in your terminal:
```
bash <( curl https://raw.githubusercontent.com/bulwark-crypto/shn/master/update.sh )
```

