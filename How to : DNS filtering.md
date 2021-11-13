# How to filter DNS request with Adguard Home?

I use the Docker image : https://hub.docker.com/r/adguard/adguardhome
Easy to setup, easy to use.

## Prepare
### Pull docker image
```sh
docker pull adguard/adguardhome
```

### Create config/data directory
```sh
mkdir -p /opt/adguard-home/work/data
mkdir -p /opt/adguard-home/conf
```
### Use TMPFS to protect the SD card

Use VI to define /opt/adguard-home/work/data as a TMPFS
```
tmpfs /opt/adguard-home/work/data tmpfs defaults,noexec,nosuid,nodev,mode=0755 0 0
```
Mount the TMPFS
```
sudo mount /opt/adguard-home/work/data
```

## Start the container
### docker-compose.yml

Define your container (docker-compose.yml) :
```yaml
version: '3.3'
services:
  adguard:
    container_name: adguardhome
    image: adguard/adguardhome
    environment:
      - TZ=Europe/Paris
    volumes:
      - ./work:/opt/adguardhome/work
      - ./conf:/opt/adguardhome/conf
    network_mode: host  # nécessaire si AdGuard Home est utilisé en tant que serveur DHCP
    restart: unless-stopped
```

### Start

```sh
sudo docker-compose up -d
```

# Filters

My personal list of DNS filters which allows me to have a balance between efficiency and family use of a network link.
I use this list with AdGuard Home -> filter 60% of my traffic...

```yaml
- enabled: true
  url: https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
  name: AdGuard DNS filter
  id: 1
- enabled: true
  url: https://adaway.org/hosts.txt
  name: AdAway Default Blocklist
  id: 2
- enabled: true
  url: https://www.malwaredomainlist.com/hostslist/hosts.txt
  name: MalwareDomainList.com Hosts List
  id: 4
- enabled: true
  url: https://someonewhocares.org/hosts/zero/hosts
  name: Dan Pollock's List
  id: 1632754970
- enabled: true
  url: https://raw.githubusercontent.com/DandelionSprout/adfilt/master/GameConsoleAdblockList.txt
  name: Game Console Adblock List
  id: 1632754971
- enabled: true
  url: https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV-AGH.txt
  name: Perflyst and Dandelion Sprout's Smart-TV Blocklist
  id: 1632754972
- enabled: true
  url: https://pgl.yoyo.org/adservers/serverlist.php?hostformat=adblockplus&showintro=1&mimetype=plaintext
  name: Peter Lowe's List
  id: 1632754973
- enabled: true
  url: https://raw.githubusercontent.com/hoshsadiq/adblock-nocoin-list/master/hosts.txt
  name: NoCoin Filter List
  id: 1632754974
- enabled: true
  url: https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
  name: WindowsSpyBlocker - Hosts spy rules
  id: 1632754975
- enabled: true
  url: https://raw.githubusercontent.com/durablenapkin/scamblocklist/master/adguard.txt
  name: Scam Blocklist by DurableNapkin
  id: 1632754976
- enabled: true
  url: https://raw.githubusercontent.com/Spam404/lists/master/main-blacklist.txt
  name: Spam404
  id: 1632754977
- enabled: true
  url: https://raw.githubusercontent.com/mitchellkrogza/The-Big-List-of-Hacked-Malware-Web-Sites/master/hosts
  name: The Big List of Hacked Malware Web Sites
  id: 1632754978
- enabled: true
  url: https://paulgb.github.io/BarbBlock/blacklists/hosts-file.txt
  name: BarbBlock
  id: 1632754979
- enabled: true
  url: https://curben.gitlab.io/malware-filter/urlhaus-filter-agh-online.txt
  name: Online Malicious URL Blocklist
  id: 1632754980
- enabled: true
  url: https://abp.oisd.nl/
  name: OISD DNS filter
  id: 1634855336
- enabled: true
  url: https://hblock.molinero.dev/hosts
  name: Hblock DNS filter
  id: 1634855337
- enabled: true
  url: https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
  name: Steven Black DNS filter
  id: 1634855338
- enabled: true
  url: https://raw.githubusercontent.com/notracking/hosts-blocklists/master/adblock/adblock.txt
  name: NoTracking
  id: 1636565192
- enabled: true
  url: https://raw.githubusercontent.com/jerryn70/GoodbyeAds/master/Formats/GoodbyeAds-AdBlock-Filter.txt
  name: GoodByeAds
  id: 1636565193
- enabled: true
  url: https://block.energized.pro/basic/formats/filter
  name: EnergizedProtection
  id: 1636565195
- enabled: true
  url: https://badmojr.github.io/1Hosts/Pro/adblock.txt
  name: 1HostPro
  id: 1636565196
- enabled: true
  url: https://www.github.developerdan.com/hosts/lists/ads-and-tracking-extended.txt
  name: Lightswitch05's ads-and-tracking-extended
  id: 1636565197
- enabled: true
  url: https://gitlab.com/quidsup/notrack-blocklists/-/raw/master/trackers.hosts
  name: Notrack-Trackers
  id: 1636565198
- enabled: true
  url: https://gitlab.com/quidsup/notrack-blocklists/-/raw/master/malware.hosts
  name: Notrack-Malware
  id: 1636565199
- enabled: true
  url: https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
  name: Anudeep
  id: 1636565200
- enabled: true
  url: https://easylist-downloads.adblockplus.org/liste_fr.txt
  name: Liste FR
  id: 1636565201
- enabled: true
  url: https://easylist.to/easylist/easyprivacy.txt
  name: EasyPrivacy
  id: 1636565202
```
