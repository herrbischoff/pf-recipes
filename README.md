# Recipes for the pf Firewall

### Block Abusive Hosts
Limits the maximum number of connections per source to 100 and the number of connections to 15 in a 5 second span, adding hosts exceeding the limits to the `abusive_ips` table and blocks them for making any further connections. Also kills all states created by the matching rule which originate from the host which exceeds these limits.
```
table <abusive_ips> persist
block in quick from <abusive_ips>
pass in on $ext_if proto tcp to ($ext_if) port { http, https } flags S/SA keep state (max-src-conn 100, max-src-conn-rate 15/5, overload <abusive_ips> flush)
```
