# 🖥️ Ubuntu Server GUI Installation & Remote Desktop Access Tutorial

This tutorial guides you through **installing a GUI on Ubuntu Server**, **connecting through Remote Desktop (RDP)** from Windows, and **fixing login issues** if the GUI login screen doesn’t accept your password.

---

## 🧩 1. Update and Upgrade Ubuntu Server

Start by updating the package lists and upgrading existing packages:

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🖥️ 2. Install XFCE Desktop Environment

XFCE is lightweight and stable — ideal for servers.

```bash
sudo apt install xfce4 xfce4-goodies -y
```

---

## 🔑 3. Install and Enable XRDP

XRDP allows Windows users to connect via Remote Desktop.

```bash
sudo apt install xrdp -y
sudo systemctl enable --now xrdp
```

Verify it’s active:
```bash
sudo systemctl status xrdp
```

---

## ⚙️ 4. Configure XRDP to Use XFCE

Tell XRDP to start the XFCE desktop when a user logs in.

```bash
echo xfce4-session > ~/.xsession
```

---

## 🌐 5. Allow RDP Through Firewall (Optional)

If UFW is enabled, allow RDP port `3389`:

```bash
sudo ufw allow 3389/tcp
sudo ufw reload
```

---

## 🪟 6. Connect from Windows Remote Desktop

1. Press **Windows + R**, type:
   ```
   mstsc
   ```
2. Enter your server’s IP:
   ```
   your-server-ip
   ```
3. Login using your **Ubuntu username** and **password**.

You should now see the **XFCE desktop** remotely. 🎉

---

## ⚠️ 7. Fixing the “Login Failed” or “Incorrect Password” on GUI

If the GUI login screen appears after reboot but doesn’t accept your password — even when it’s correct — follow these fixes.

---

### 🔹 Step 1: Check Installed Session
```bash
dpkg -l | grep xfce4-session
```
If not installed:
```bash
sudo apt install xfce4-session -y
```

---

### 🔹 Step 2: Create LightDM Configuration
LightDM manages the GUI login. Sometimes it defaults to a broken “ubuntu” session.

Create a custom config forcing XFCE:

```bash
sudo mkdir -p /etc/lightdm/lightdm.conf.d
echo -e "[Seat:*]\nuser-session=xfce\nautologin-user-timeout=0" | sudo tee /etc/lightdm/lightdm.conf.d/99-xfce.conf
```

---

### 🔹 Step 3: Set XFCE as the Default Session Manager
```bash
sudo update-alternatives --install /usr/bin/x-session-manager x-session-manager /usr/bin/startxfce4 50
sudo update-alternatives --set x-session-manager /usr/bin/startxfce4
```

---

### 🔹 Step 4: Install a Stable Greeter
Replace the default (Unity) greeter, which often causes login failures.

```bash
sudo apt install lightdm-gtk-greeter -y
echo -e "[Seat:*]\ngreeter-session=lightdm-gtk-greeter\nuser-session=xfce" | sudo tee /etc/lightdm/lightdm.conf.d/99-xfce.conf
```

---

### 🔹 Step 5: Clean Up Xauthority Files
```bash
sudo rm -f /var/lib/lightdm/.Xauthority
sudo rm -f ~/.Xauthority
sudo rm -rf ~/.cache/sessions
sudo chown -R $USER:$USER /home/$USER
```

---

### 🔹 Step 6: Restart LightDM
```bash
sudo systemctl restart lightdm
```

If you’re on a remote session, you may need to reconnect.

---

## ✅ 8. Verify Successful Login

After reboot:

- You should see the **Ubuntu login screen (LightDM)**.
- Enter your correct username and password.
- XFCE desktop should load successfully.

---

## 🧠 Optional: Auto-start XFCE at Boot

If you want the GUI to always start at boot:
```bash
sudo systemctl set-default graphical.target
```

---

## 🧰 9. Install GUI Applications (Optional)

You can now safely install GUI-based software:

```bash
sudo snap install code --classic     # Visual Studio Code
sudo apt install firefox -y          # Firefox Browser
sudo apt install gnome-system-monitor -y  # Task Manager
```

---

## 🎯 Summary

| Step | Action | Command |
|------|---------|----------|
| 1 | Update Server | `sudo apt update && sudo apt upgrade -y` |
| 2 | Install XFCE | `sudo apt install xfce4 xfce4-goodies -y` |
| 3 | Install XRDP | `sudo apt install xrdp -y` |
| 4 | Set XFCE Session | `echo xfce4-session > ~/.xsession` |
| 5 | Configure LightDM | Add XFCE session config |
| 6 | Install GTK Greeter | `sudo apt install lightdm-gtk-greeter -y` |
| 7 | Restart GUI | `sudo systemctl restart lightdm` |
| 8 | Connect via RDP | Use `mstsc` on Windows |

---

### 🏁 Final Result
✅ XFCE desktop environment installed  
✅ Remote Desktop (RDP) working  
✅ GUI login screen functional  
✅ System stable after reboot  

Enjoy your fully functional Ubuntu Server Desktop! 🎉
