# Raspberry Pi → Discord IP Notification (systemd Method)

This guide shows how to configure a **Raspberry Pi** to automatically send its IP address to a Discord channel every time it boots. The method uses a **Discord webhook** and a **systemd service**, which is the most reliable way to run tasks at startup.

---

# 1. Create a Discord Webhook

1. Open Discord.
2. Go to the server where you want the message to appear.
3. Click **Server Settings**.
4. Select **Integrations**.
5. Click **Webhooks**.
6. Click **New Webhook**.
7. Copy the **Webhook URL**.

It will look similar to:

```
https://discord.com/api/webhooks/123456789012345678/abcdefghijklmnopqrstuvwxyz
```

Keep this URL — you will paste it into the script later.

---

# 2. Create the IP Notification Script

On the Raspberry Pi, create a new script:

```bash
nano send_ip.sh
```

Paste the following code:

```bash
#!/bin/bash

# Wait for network to be fully ready
sleep 20

WEBHOOK="PASTE_YOUR_WEBHOOK_URL_HERE"

HOST=$(hostname)
LOCAL_IP=$(hostname -I | awk '{print $1}')

MESSAGE="Raspberry Pi $HOST is online. IP: $LOCAL_IP"

curl -H "Content-Type: application/json" \
-X POST \
-d "{\"content\": \"$MESSAGE\"}" \
$WEBHOOK
```

Replace:

```
PASTE_YOUR_WEBHOOK_URL_HERE
```

with your Discord webhook.

Save and exit:

```
CTRL + O
Enter
CTRL + X
```

---

# 3. Make the Script Executable

```
chmod +x send_ip.sh
```

---

# 4. Move the Script to a System Location

Move the script to `/usr/local/bin` so the system can run it at boot.

```
sudo mv send_ip.sh /usr/local/bin/send_ip.sh
```

Set correct permissions:

```
sudo chmod +x /usr/local/bin/send_ip.sh
```

---

# 5. Create the systemd Service

Create a new service file:

```
sudo nano /etc/systemd/system/send-ip.service
```

Paste the following configuration:

```
[Unit]
Description=Send Raspberry Pi IP to Discord
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/send_ip.sh
Type=oneshot

[Install]
WantedBy=multi-user.target
```

Save and exit.

---

# 6. Enable the Service

Reload systemd so it detects the new service:

```
sudo systemctl daemon-reload
```

Enable the service to run at boot:

```
sudo systemctl enable send-ip.service
```

---

# 7. Test the Service

Before rebooting, test it manually:

```
sudo systemctl start send-ip.service
```

If everything is working correctly, a message should appear in your Discord channel such as:

```
Raspberry Pi raspberrypi is online. IP: 192.168.1.45
```

---

# 8. Reboot to Confirm

Restart the Raspberry Pi:

```
sudo reboot
```

After the system finishes booting and connects to the network, Discord should receive the IP message automatically.

---

# 9. Checking Service Status (Optional)

If you want to verify that the service ran correctly:

```
systemctl status send-ip.service
```

You can also view logs:

```
journalctl -u send-ip.service
```

---

# Result

Every time the Raspberry Pi boots:

1. The system waits for the network connection.
2. The script retrieves the hostname and IP address.
3. A message is sent automatically to your Discord channel.

This is a simple way to keep track of your Raspberry Pi’s IP address when running it as a home server or remote device.
