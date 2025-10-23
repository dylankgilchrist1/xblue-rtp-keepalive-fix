# RTP Keep Alive Fix (Transferred Call Drops)

This document provides step-by-step instructions to apply the **RTP Keepalive Fix** for **XBLUE QB** systems.  
This fix resolves an issue where **calls drop after being transferred**, caused by RTP media streams timing out when endpoints stop sending packets — most commonly observed with **certain SIP carriers** that do not maintain RTP flow during call transfers.

## Important Note
The RTP Keep Alive option was introduced in the **GUI interface starting with firmware version 30.14.0.161.6**. 
If you are running a previous version and can upgrade please do so.

**Applies To:** QB Servers with SIP Trunks  
**Last Updated:** March 14, 2022  

---
## Root Cause
When a call is transferred, the original call leg between the phone and PBX may temporarily stop sending RTP (media) packets.  
Without active RTP flow, the PBX interprets this as a lost connection and tears down the call after a short timeout.  

The default configuration does **not** send keepalive RTP packets during silence or call handoff events.  
As a result, the call is dropped even though both parties remain connected logically at the signaling level.  

## Solution 
Enable the RTP keepalive feature to maintain continuous RTP packet flow between endpoints and the PBX during call transfers. When a call is placed on hold or transferred, some devices stop sending RTP packets, causing the PBX to assume the call has ended. The `rtp_keepalive` parameter forces the PBX to send silent RTP packets at regular intervals, preventing the RTP session from timing out and ensuring transferred calls remain connected.

---

# Step 1 - Obtaining SSH Password on QB Server
1. Login to the QB Server web interface.  
2. Navigate to **Settings → Security → Service**.  
3. Enable the **SSH** checkbox.  
   - A password will appear — **save or copy** it for later.  
<img width="1400" height="816" alt="Untitled design (5)" src="https://github.com/user-attachments/assets/8907f372-824b-4b14-b427-f702d28e66ad" />


---

# Step 2 -  Downloading PuTTY and loging in 
1. Download and open PuTTY
2. Connect using:  
   - **Host Name / IP Address:** your QB Server IP  
   - **Port:** `8022`  
   - **Connection Type:** SSH  
3. When prompted:  
   - **Username:** `support`  
   - **Password:** *(the password shown on the PBX SSH screen)*  
4. Once connected, you should see:  
   ```bash
   support@IPPBX:~$
   ```
To paste text in PuTTY, simply right-click.

---

# Step 3 - Apply the RTP Keepalive Configuration
1. Paste the first command (both lines)** to open the configuration file:
   ```bash
   cd /ysdisk/support/customcfg/
   vi pjsip_custom.conf

Press i to enter insert mode (you’ll see “INSERT” at the bottom of the screen).

2. Paste the second command (both lines) into the file
 ```bash
[endpoint-basic](!)
rtp_keepalive=1
 ```
3. Press Esc to exit insert Mode
4. Type :wq and press Enter to save and quit the editor
5. Reboot the QB server to apply changes

### Document Control
**Title:** RTP Keepalive Fix — Transferred Call Drops  
**Applies To:** XBLUE QB Systems with SIP Trunks (Certain Carriers Affected)  
**Author:** Dylan Gilchrist  
**Last Updated:** October 2025
**Version:** 1.2
