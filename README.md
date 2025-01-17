# An Evil Twin Attack detection and prevention from user's side using Deep Transfert Learning

In a new and unknown public network environment, attacks pose a great threat to existing signature recognition-based detection systems. Therefore, machine learning based approaches are designed as ideal tools to detect attacks such as Evil-Twin Attack (ETA) in a dynamic environment where MAC addresses of devices may vary, this transfer learning method relies on features extracted from network data. The use of this technique is based mainly on the problem that is due to a different distribution of features in the training and test data sets, which affects the performance of the learned models. In addition, the generation of labeled datasets in a realistic environment is unfeasible since it is unseen, which indeed reduces the effectiveness of supervised learning approaches to detect a user-side attack. In this project, we propose to use deep transfer learning to detect Evil-Twin attacks in various environments. The main idea is to learn the representation optimized to be invariant to changes in attack behavior from labeled training sets and unlabeled test sets. To the best of our knowledge, this is the first attempt to use a feature-based transfer learning technique to detect an ETA in dynamic network environments. Furthermore, we evaluated the technique on publicly available datasets (AWID-ETA), Furthermore we generate on an Ad Hoc network realized by ESP32 boards own dataset (CSI, RSSI, RTT and some features of the initial packets exchanged during the deauthentication phases). The results demonstrate the effectiveness of transfer learning in detecting the evil-Twin attack in new network environments.

Obviously, cracking is not part of this project, as ESP32 is not sufficient to crack hashes efficiently. The rest can be done on this small, cheap and low power SoC.


# Overview
<p align="center">
    <img src="/doc/attacks/scheem.png" alt="overview">
</p>

<p align="center">
    <img src="/doc/attacks/deauth-frame-format.drawio.svg" alt="Logo">
</p>

## Features
- **PMKID capture**
- **WPA/WPA2 handshake capture** and parsing
- **Deauthentication attacks** using various methods
- **Denial of Service attacks**
- Formatting captured traffic into **PCAP format**
- Parsing captured handshakes into **HCCAPX file** ready to be cracked by Hashcat
- Passive handshake sniffing
- Easily extensible framework for new attacks implementations
- Management AP for easy configuration on the go using smartphone for example
- And more...

### Demo video
[![CSI Tool](https://github.com/StevenMHernandez/ESP32-CSI-Tool)

## Usage
1. [Build](#Build) and [flash](#Flash) project onto ESP32 (DevKit or module)
1. Power ESP32
1. Management AP is started automatically after boot
1. Connect to this AP\
By default: 
*SSID:* `TelecomeParisAP` and *password:* `telcomeparis`
1. In browser open `192.168.4.1` and you should see a web client to configure and control tool like this:

    ![Web client UI](doc/images/ui-config.png)

## Build
This project is currently developed using ESP-IDF 4.1 (commit `5ef1b390026270503634ac3ec9f1ec2e364e23b2`). It may be broken on newer version.

Project can be built in the usual ESP-IDF way:

```shell
idf.py build
```

Legacy method using `make` is not supported by this project.

## Flash
If you have setup ESP-IDF, the easiest way is to use `idf.py flash`.

In case you don't want to setup whole ESP-IDF, you can use pre-build binaries included in [`build/`](build/) and flash them using [`esptool.py`](https://github.com/espressif/esptool) (requires Python).

Example command (follow instructions in [esptool repo](https://github.com/espressif/esptool)):
```
esptool.py -p /dev/ttyS5 -b 115200 --after hard_reset write_flash --flash_mode dio --flash_freq 40m --flash_size detect 0x8000 build/partition_table/partition-table.bin 0x1000 build/bootloader/bootloader.bin 0x10000 build/esp32-wifi-penetration-tool.bin
```

On Windows you can use official [Flash Download Tool](https://www.espressif.com/en/support/download/other-tools).

## Documentation
### Wi-Fi attacks
Attacks implementations in this project are described in [main component README](main/). Theory behind these attacks is located in [doc/ATTACKS_THEORY.md](doc/ATTACKS_THEORY.md)
### API reference
This project uses Doxygen notation for documenting components API and implementation. Doxyfile is included so if you want to generate API reference, just run `doxygen` from root directory. It will generate HTML API reference into `doc/api/html`.

### Components
This project consists of multiple components, that can be reused in other projects. Each component has it's own README with detailed description. Here comes brief description of components:

- [**Main**](main) component is entry point for this project. All neccessary initialisation steps are done here. Management AP is started and the control is handed to webserver.
- [**Wifi Controller**](components/wifi_controller) component wraps all Wi-Fi related operations. It's used to start AP, connect as STA, scan nearby APs etc. 
- [**Webserver**](components/webserver) component provides web UI to configure attacks. It expects that AP is started and no additional security features like SSL encryption are enabled.
- [**Wi-Fi Stack Libraries Bypasser**](components/wsl_bypasser) component bypasses Wi-Fi Stack Libraries restriction to send some types of arbitrary 802.11 frames.
- [**Frame Analyzer**](components/frame_analyzer) component processes captured frames and provides parsing functionality to other components.
- [**PCAP Serializer**](components/pcap_serializer) component serializes captured frames into PCAP binary format and provides it to other components (mostly for webserver/UI)
- [**HCCAPX Serializer**](components/hccapx_serializer) component serializes captured frames into HCCAPX binary format and provides it to other components (mostly for webserver/UI)

## Hardware 
This project was mostly build and tested on **ESP32-DEVKITC-32E**
but there should not be any differences for any **ESP32-WROOM-32** modules.

<p align="center">
    <img src="doc/images/soucastky_8b.png" alt="Hw components" width="400">
</p>

On the following pictures you can see a battery (Li-Pol accumulator) powered ESP32 DevKitC using following hardware:
- **ESP32-DEVKITC-32E** (cost 213 CZK/8.2 EUR/9.6 USD)
- 220mAh Li-Pol 3.7V accumulator (weights ±5g, cost 77 CZK/3 EUR/3.5 USD)
- MCP1702-3302ET step-down 3.3V voltage regulator (cost 11 CZK/0.42 EUR/0.50 USD)
- Czech 5-koruna coin for scale (weights 4.8g, diameter 23 mm, cost 0.19 EUR/0.23 USD)
<p align="center">
    <img src="doc/images/mini.jpg" alt="Hw components" width="300">
    <img src="doc/images/mini2.jpg" alt="Hw components" width="300">
</p>

Altogether (without coin) this setup weights around 17g. This can be further downsized by using smaller Li-Pol accumulator and using ESP32-WROOM-32 modul directly instead of whole dev board.

This setup cost me around 300 CZK (± 11.50 EUR/13.50 USD). Using the modul directly that costs around 80 CZK (± 3 EUR/3.5 USD) we can get to price of 160 CZK (± 6.5 EUR/7.5 USD) which makes this tool really cheap and available to almost everybody.

### Power consumption
Based on experimental measurements, ESP32 consumes around 100mA during attack executions. 

## Contributing
Feel free to contribute. Don't hestitate to refactor current code base. Please stick to Doxygen notation when commenting new functions and files. This project is mainly build for educational and demonstration purposes, so verbose documentation is welcome.

## Disclaimer
This project demonstrates how to detect vulnerabilities in Wi-Fi networks and the 802.11 standard that underlies them in an extremely Intelligent way, as well as how the ESP32 platform can be used to attack these vulnerabilities. To be used responsibly against the networks you have permission to attack.

## License
Even though this project is licensed under MIT license (see [LICENSE](LICENSE) file for details), don't be shy or greedy and share your work.
