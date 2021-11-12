My personal list of DNS filters which allows me to have a balance between efficiency and family use of a network link.
I use this list with AdGuard Home -> filter 60% of my traffic...

```yaml
- enabled: true
  url: https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
  name: AdGuard DNS filter
- enabled: true
  url: https://adaway.org/hosts.txt
  name: AdAway Default Blocklist
- enabled: true
  url: https://www.malwaredomainlist.com/hostslist/hosts.txt
  name: MalwareDomainList.com Hosts List
- enabled: true
  url: https://someonewhocares.org/hosts/zero/hosts
  name: Dan Pollock's List
- enabled: true
  url: https://raw.githubusercontent.com/DandelionSprout/adfilt/master/GameConsoleAdblockList.txt
  name: Game Console Adblock List
- enabled: true
  url: https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV-AGH.txt
  name: Perflyst and Dandelion Sprout's Smart-TV Blocklist
- enabled: true
  url: https://pgl.yoyo.org/adservers/serverlist.php?hostformat=adblockplus&showintro=1&mimetype=plaintext
  name: Peter Lowe's List
- enabled: true
  url: https://raw.githubusercontent.com/hoshsadiq/adblock-nocoin-list/master/hosts.txt
  name: NoCoin Filter List
- enabled: true
  url: https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
  name: WindowsSpyBlocker - Hosts spy rules
- enabled: true
  url: https://raw.githubusercontent.com/durablenapkin/scamblocklist/master/adguard.txt
  name: Scam Blocklist by DurableNapkin
- enabled: true
  url: https://raw.githubusercontent.com/Spam404/lists/master/main-blacklist.txt
  name: Spam404
- enabled: true
  url: https://raw.githubusercontent.com/mitchellkrogza/The-Big-List-of-Hacked-Malware-Web-Sites/master/hosts
  name: The Big List of Hacked Malware Web Sites
- enabled: true
  url: https://paulgb.github.io/BarbBlock/blacklists/hosts-file.txt
  name: BarbBlock
- enabled: true
  url: https://curben.gitlab.io/malware-filter/urlhaus-filter-agh-online.txt
  name: Online Malicious URL Blocklist
- enabled: true
  url: https://abp.oisd.nl/
  name: OISD DNS filter
- enabled: true
  url: https://hblock.molinero.dev/hosts
  name: Hblock DNS filter
- enabled: true
  url: https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
  name: Steven Black DNS filter
- enabled: true
  url: https://raw.githubusercontent.com/notracking/hosts-blocklists/master/adblock/adblock.txt
  name: NoTracking
- enabled: true
  url: https://raw.githubusercontent.com/jerryn70/GoodbyeAds/master/Formats/GoodbyeAds-AdBlock-Filter.txt
  name: GoodByeAds
- enabled: true
  url: https://block.energized.pro/basic/formats/filter
  name: EnergizedProtection
- enabled: true
  url: https://badmojr.github.io/1Hosts/Pro/adblock.txt
  name: 1HostPro
- enabled: true
  url: https://www.github.developerdan.com/hosts/lists/ads-and-tracking-extended.txt
  name: Lightswitch05's ads-and-tracking-extended
- enabled: true
  url: https://gitlab.com/quidsup/notrack-blocklists/-/raw/master/trackers.hosts
  name: Notrack-Trackers
- enabled: true
  url: https://gitlab.com/quidsup/notrack-blocklists/-/raw/master/malware.hosts
  name: Notrack-Malware
- enabled: true
  url: https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
  name: Anudeep
- enabled: true
  url: https://easylist-downloads.adblockplus.org/liste_fr.txt
  name: Liste FR
- enabled: true
  url: https://easylist.to/easylist/easyprivacy.txt
  name: EasyPrivacy
```
