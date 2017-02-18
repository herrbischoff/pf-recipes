# Recipes for the pf Firewall

### Annoy Abusive Hosts
This list of IP addresses (`/etc/slowqueue`) is for people who you consider abusers, but you want to annoy them. IPs in this list will be subjected to 97% packet loss. The 3% of connections that do make it through will come through to your services as expected. In effect, the hosts will not be blocked entirely but have their packets randomly dropped. This is effective against download accelerators, bots, and scanners.
```
table <slowqueue> persist file "/etc/slowqueue"
block in quick from <slowqueue> to any probability 97%
```

### Block Abusive Hosts
Limits the maximum number of connections per source to 100 and the number of connections to 15 in a 5 second span, adding hosts exceeding the limits to the `abusive_ips` table and blocks them for making any further connections. Also kills all states created by the matching rule which originate from the host which exceeds these limits.
```
table <abusive_ips> persist
block in quick from <abusive_ips>
pass in on $ext_if proto tcp to ($ext_if) port { http, https } flags S/SA keep state (max-src-conn 100, max-src-conn-rate 15/5, overload <abusive_ips> flush)
```
