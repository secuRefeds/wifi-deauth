
</br>
A DoS attack that disconnects all devices from a target wifi network.
* The network's password is not required
* Tested on Kali linux and parrot os


**IMPORTANT** </br>
In some occasions, network APs might operate on both 5GHz and 2.4GHz under the same BSSID name. <br>
In order to truly bring the AP down, I usually run simultaneously two de-authers using 2 network interfaces: one for 2.4GHz and one for 5GHz. </br>
| Bandwidth | Channel range |
|----------|---------------|
| 2.4 GHz   | 1 <--> 14     |
| 5 GHz     | 35 <--> 165   |

## How it works

The program iterates over all possible channels, and by sniffing `802.11` packets it determines which access points are available. </br>
After the attacker chooses a target access point to attack, the program:
1. Continously sends spoofed deauthentication packets using broadcast mac address as the destination
2. Starts sniffing for clients that are connected to the AP by filtering for certain 802.11 packet frames and sending spoofed deauthentication packets to those clients in addition to the broadcast address


# Usage
#### Installing on the system
```bash
git clone https://github.com/secuRefeds/wifi-deauth.git
sudo pip3 install .
sudo wifi-deauth -i <iface>
```

#### Running without installing 
```bash
git clone https://github.com/secuRefeds/wifi-deauth.git
sudo pip3 install -r requirements.txt # install requirements manually
cd wifi_deauth
sudo python3 wifi_deauth.py -i <iface>
```

### Usage notes
*  `<iface>` is the name of the network interface (i.e `wlan0` or `eth0`) that supports packet injection
* The initial iteration over all channels might take a minute or two (depends on how many bands the interface supports)

### Optional arguments
* `--bssid <name>` - filter for a specific BSSID (this should shorten the channel-scanning duration), beware that the name is case-sensitive and whitespaces should be passed with an escape character (i.e -> `new\ york`)
* `--channels <ch1,ch2>` - scan for specific channels only, otherwise all supported channels will be scanned
* `--clients <m_addr1,m_addr2>` - target only specific clients to disconnect from the AP, otherwise all connected clients will be targeted (note: using this option disables deauth broadcast)
* `--kill` (or run `sudo systemctl stop NetworkManager`) - kill NetworkManager service which might interfere with the attack
* `--skip-monitormode` - enable monitor mode manually (otherwise the program does it automatically)

### Misc notes
* Check `ifconfig` to find the interface nickname
* Works for 2.4GHhz and 5Ghz
* Beware that some access points have protections against this kind of attack and therefore it might not work on them
* Setting custom client mac addresses (`--clients`) is not suggested, as some clients might reconnect using a random MAC address which is different than the one set

### Requirements
* Linux OS
* A network adapter that supports monitor mode and packet injection
* Scapy library (listed in `requirements.txt`)



# Disclaimer

This tool is only for testing and can only be used where strict consent has been given. Do not use it for illegal purposes! It is the end user’s responsibility to obey all applicable local, state and federal laws. I assume no liability and am not responsible for any misuse or damage caused by this tool and software.


