# OpenWRT/LEDE Project Packages Repository (papal-repo)
This repo includes custom packages primarily related to SQM and bufferbloat mitigation. They are placed here to ease installation during development and testing, and may change without notice. You should not use these packages unless requested to do so by me. Happy Testing!

## How to Use

#### Online Install
To add this repo to your OpenWrt/LEDE Project router run the following commands.  
(NOTE: Versions of LEDE/OpenWRT earlier that 17.01.5 include an out-of-date `CAKE` kernel module and `iproute2` `tc` utility, which **MUST** be manually upgraded to more recent versions.)

###### LEDE 17.01.5, OpenWrt 18.xx or later
```
opkg update; opkg install libustream-mbedtls
wget -O /tmp/papal-repo.pub https://raw.githubusercontent.com/guidosarducci/papal-repo/master/papal-repo.pub && opkg-key add /tmp/papal-repo.pub
sed -n -i '/papal_repo/!p;$a src/gz papal_repo https://raw.githubusercontent.com/guidosarducci/papal-repo/master' /etc/opkg/customfeeds.conf
opkg update
```

#### Offline Install
###### Image Builder
Add the following line
```
src/gz papal_repo https://raw.github.com/guidosarducci/papal-repo/master
```
to the ```repositories.conf``` file inside your Image Builder directory. You can use the following shell script code to achieve that:
```
sed -n -i '/papal_repo/!p;$a src/gz papal_repo https://raw.github.com/guidosarducci/papal-repo/master' repositories.conf
```

## Description of Packages

#### speedtest
The `speedtest` package provides a convenient means of on-device network performance testing for OpenWrt routers. Such performance testing primarily includes characterizing the network throughput and latency, but CPU usage can also be an important secondary measurement. These aspects of network testing are motivated chiefly by the following:

1. **Throughput:** Network speed measurements can help troubleshoot transfer problems, and be used to determine the truth of an ISP's promised speed claims. Accurate throughput numbers also provide guidance for configuring other software's settings, such as SQM ingress/egress rates, or bandwidth limits for Bittorrent.

2. **Latency:** Network latency is a key factor in high-quality experiences with real-time or interactive applications such as VOIP, gaming, or video conferencing, and excessive latency can lead to undesirable dropouts, freezes and lag. Such latency problems are endemic on the Internet and often the result of [bufferbloat](https://www.bufferbloat.net/projects/). Systematic latency measurements are an important part of identifying and mitigating this bufferbloat.

3. **CPU Usage:**  Observing CPU usage under network load gives insight into whether the router is CPU-bound, or if there is CPU "headroom" to support even higher network throughput. In addition to managing network traffic, a router actively running a `speedtest` will also use CPU cycles to generate network load, and measuring this distinct CPU usage also helps gauge its impact.

**Note:** _The `speedtest.sh` script uses servers and network bandwidth that are provided by generous volunteers (not some wealthy "big company"). Feel free to use the script to test your SQM configuration or troubleshoot network and latency problems. Continuous or high rate use of this script may result in denied access._

---
#### sqm-scripts & luci-app-sqm
(**NOTE: _These packages are being renamed and reworked for broader distribution and will be available when completed._**)

These are enhanced versions of the standard OpenWRT SQM packages, developed and tested independently for more than a year, which provide major improvements in ease of use, configuration, functionality and robustness. Further details of the changes and usage guidelines are available in the [TODO](https://github.com/guidosarducci/papal-repo/wiki). However, much of the content in the [OpenWRT User Guide](https://openwrt.org/docs/guide-user/network/traffic-shaping/sqm) is still applicable, noting the following:
- Legacy `.qos` scripts (e.g. `layer_cake`, `simple`) have been removed; all aspects of their functionality are now directly configurable by users from the GUI or CLI. Any previously configured legacy scripts are emulated after package installation for a seamless upgrade.
- Some complicated but important qdisc-specific settings are now simply configurable within the GUI. This includes CAKE's per-host fairness and ACK filter settings.
- Link Layer adaptation can now explicitly set framing compensation (ATM/PTM), MPU and packet overhead, which are applied generally across all qdiscs. Users can also choose to import predefined adaptation settings (e.g. "DOCSIS Cable", "ADSL pppoe-vcmux").

**NOTE:**
When installed, these packages (versioned 2.x.x) will upgrade the standard packages (versioned 1.x.x) and migrate an existing SQM config file to a newer schema. **Before installing these packages, make a backup of your existing SQM configuration:**
```
cp /etc/config/sqm /etc/config/sqm-v1-backup
```
The backup can be easily restored if you revert to using the stock/legacy `sqm-scripts` packages.
