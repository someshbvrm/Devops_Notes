# 🖥️ Setting Up XRDP with XFCE Desktop on Ubuntu Server

## 📋 Overview
This guide will help you install and configure a remote desktop environment on your Ubuntu server using XRDP with the lightweight XFCE desktop environment.

## 🚀 Installation Steps

### 1. Install Required Packages
```bash
sudo apt update && sudo apt install -y xrdp xfce4 xfce4-goodies
```

### 2. Backup Original Configuration
```bash
sudo cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bak
```

### 3. Modify XRDP Configuration
```bash
# Change default port from 3389 to 3390
sudo sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini

# Increase color depth for better visual experience
sudo sed -i 's/max_bpp=32/#max_bpp=32\nmax_bpp=128/g' /etc/xrdp/xrdp.ini
sudo sed -i 's/xserverbpp=24/#xserverbpp=24\nxserverbpp=128/g' /etc/xrdp/xrdp.ini
```

### 4. Set XFCE as Default Session
```bash
echo xfce4-session > ~/.xsession
```

### 5. Configure Startup Script
Edit the startup script:
```bash
sudo nano /etc/xrdp/startwm.sh
```

Make the following changes:
- Comment out these lines by adding `#` at the beginning:
  ```
  # test -x /etc/X11/Xsession && exec /etc/X11/Xsession
  # exec /bin/sh /etc/X11/Xsession
  ```
- Add this line at the end of the file:
  ```
  startxfce4
  ```

### 6. Start XRDP Service
```bash
sudo systemctl enable xrdp
sudo systemctl restart xrdp
```

### 7. Configure Firewall (If Enabled)
```bash
sudo ufw allow 3390/tcp
```

## 🔗 Connecting to Your Server

1. On your local machine, open your Remote Desktop Client
2. Connect to: `your-server-ip:3390`
3. Use your Ubuntu username and password to log in

## ⚙️ Additional Configuration (Optional)

### Change Session Environment
If you want to use a different desktop environment, modify the `~/.xsession` file:
```bash
echo "your-desktop-environment-session" > ~/.xsession
```

### Adjust Screen Resolution
You can modify the resolution in `/etc/xrdp/xrdp.ini` under the `[xrdp1]` section:
```
width=1920
height=1080
```

## 🛠️ Troubleshooting

### Check XRDP Status
```bash
sudo systemctl status xrdp
```

### View XRDP Logs
```bash
tail -f /var/log/xrdp.log
```

### Restart XRDP Service
```bash
sudo systemctl restart xrdp
```

## 🔒 Security Considerations

1. **Change Default Port**: We've already changed from 3389 to 3390
2. **Use SSH Tunneling**: For enhanced security, consider tunneling RDP through SSH:
   ```bash
   ssh -L 33389:localhost:3390 your-user@your-server-ip
   ```
   Then connect to `localhost:33389` with your RDP client

3. **Consider Firewall Rules**: Restrict access to specific IP addresses if possible

## 💡 Tips

- XFCE is lightweight and ideal for server environments
- The changed port (3390) helps avoid conflicts with Windows RDP services
- For better performance, consider using X2Go instead of XRDP for remote desktop access

Enjoy your new remote desktop environment! 🎉