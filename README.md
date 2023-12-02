# XPAH-TBLSHOOT
TD-XPAH troubleshooting notes

Commands used
 - iwlist wlan1 scan   #scans
 - sudo ip addr # if showing <no-carrier>, then not connected
 - sudo iw wlan1 info  #check that is mesh mode and not managed
 - sudo iw wlan1 mesh_param dump   #shows mesh parameters
 - sudo systemctl restart wpa_supplicant
 - sudo iw wlan1 mesh join halow_mesh
 - sudo systemctl stop wpa_supplicant
 - sudo ifconfig wlan1 down , sudo iw wlan1 mesh join halow_mesh , sudo ifconfig wlan1 up
 - sudo systemctl stop accesspoint@wlan1.service , sudo systemctl stop wpa_supplicant
 - sudo brctl show br0
 - iw wlan1 mpath dump # see other nodes MAC addresses

Notes
  - When a mesh interface is not connected it will show <NO-CARRIER,BROADCAST,MULTICAST,UP> when you run "ip addr".
When is has connected successfully you will not see the "NO-CARRIER" flag.
  - You can view mesh connections when nodes are connected via "sudo iw wlan1 mpath dump".
  - That's probably because wpa_supplicant is running and controls wlan1. You could try stopping wpa_supplicant "sudo   
 systemctl stop wpa_supplicant" and then the join commands.
  - Try "sudo ifconfig wlan1 down" then "sudo iw wlan1 mesh join halow_mesh" and then "sudo ifconfig wlan1 up
  - Ok, so let's stop the AP and wpa_supplicant and try to get two mesh nodes to connect manually. Run "sudo systemctl stop accesspoint@wlan1.service" and "sudo systemctl stop wpa_supplicant". Then try the mesh join commands
  - Let's remove the wlan1 interface from the bridge, "sudo brctl delif br0 wlan1"
  - Ah, sorry. When the accesspoint stops it removes the interfaces. Forgot about those systemd lines.
What does the join command show now.
  - Make sure wpa_supplicant is stopped, "sudo systemctl stop wpa_supplicant" and also "sudo killall wpa_supplicant".
  - That flag will disappear when they connect. Try running "sudo iw reg set US" and "iw wlan1 set channel 36" to make sure we have the same region and channel.
  - The AP is controlled by hostapd while the wlan1 mesh interface is controlled by wpa_supplicant. When Raspbian recently changed to systemd for network devices  it meant the older, simpler /etc/network/interfaces method doesn't work. As many folks have discovered, systemd-networkd is tricky and timing issues between the kernel state machine, wpa_supplicant, and hostapd can be difficult to trace and fix
