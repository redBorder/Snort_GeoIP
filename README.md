Snort GeoIP Reputation Enhancement Patch
========================================
  
NOTE.- This patch is targeted to Snort 2.9.4 and upwards. No verification done in prior versions, but could work
  
Adds the following features:
  
- Default Action. If default action is specified at snort.conf and no action is taken it will use the default action directive. This is usefull for example when you want to run snort in pure white list (allowing only white list ips or countries) 
  
- Action ordered. The first action that matches will be used (not overwritten). This is usefull for example for manifiest file. The files at manifiest will be looked up ordered and the first action match will be used (like a firewall).
  
- Geolocalization white/black list. It will add to the original white or black list the capability to filter based on countries. It will allow us define black list countries or even white list. It uses a manifiest to use the ordered defined in that file. 
  
- New reputation options for snort.conf:
    - default_action whitelist|monitorlist       -> defines default white list. If it is not present no default action will be taken.
    - ordered                                    -> if present it will use ordered option.
    - geoip_db  path_to_GeoIP.dat,               -> location of GeoIP.dat provided by maxmind
    - geoip_path path_to_geoip_files             -> path to manifiest (geoip.info) and geoipfiles defined at manifiest
  
- NOTE: to activate geolocalization it must be enabled at configure with "--enable-reputationgeoip" option


For example I have used this reputation options for tests:
  
<pre>
preprocessor reputation: \
   memcap 4000, \
   shared_mem /opt/rb/etc/snort/iplists, \
   shared_refresh 60, \
   priority whitelist, \
   white trust, \
   nested_ip both, \
   default_action whitelist, \
   ordered, \
   geoip_db /opt/rb/share/GeoIP/GeoIP.dat, \
   geoip_path /opt/rb/etc/snort/geoips, \
   scan_local
</pre>

I have added the files: 

1.  /opt/rb/share/GeoIP/GeoIP.dat   -> GeoIP database    (this file is required for geolocalization)
2.  /opt/rb/etc/snort/geoips/geo.info -> GeoIP manifiest file. This file is required for geolocalization too. I show you an example of content:

    - white.geo, white
    - black.geo, black

    NOTE: last two lines are examples and refer to two geoip files located at same directory. (similar to IP Manifiest)
    NOTE: it allow comment with #
    NOTE: format:    
        filename, [white|monitor|black]


3.  GeoIP files. Following the same example as before:

    3.1 /opt/rb/etc/snort/geoips/white.geo
        US

    3.2 /opt/rb/etc/snort/geoips/black.geo 
        NL
        FR

    NOTE: With this example United States will be white listed and Netherland and France will be black listed
    NOTE: The country code can be extracted from :  http://en.wikipedia.org/wiki/ISO_3166-1


Important, the geolocalization library we have used is GeoIP-1.4.8 from Maxmind. You can download it from 

http://www.maxmind.com/download/geoip/api/c/