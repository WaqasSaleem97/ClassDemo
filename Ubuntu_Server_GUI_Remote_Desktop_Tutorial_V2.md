# 🖥️ Ubuntu Server GUI + Remote Desktop Access (Minimal & Flexible)

This guide helps you install a lightweight GUI (XFCE) on Ubuntu Server, access it via Remote Desktop (RDP), and control whether the GUI login screen appears at boot.

---

## 🧩 1. Update Your Server

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🖥️ 2. Install XFCE Desktop Environment

```bash
sudo apt install xfce4 xfce4-goodies -y
```

---

## 🔑 3. Install and Enable XRDP

```bash
sudo apt install xrdp -y
sudo systemctl enable --now xrdp
```

Verify XRDP is running:

```bash
sudo systemctl status xrdp
```

---

## ⚙️ 4. Configure XRDP to Use XFCE

```bash
echo xfce4-session > ~/.xsession
```

---

## 🌐 5. Allow RDP Through Firewall (Optional)

```bash
sudo ufw allow 3389/tcp
sudo ufw reload
```

---

## 🪟 6. Connect from Windows Remote Desktop

1. Press **Windows + R**, type `mstsc`
2. Enter your server’s IP
3. Login with your Ubuntu username and password

You should now see the XFCE desktop remotely 🎉

---

## ⚠️ 7. Fix GUI Login Screen Issues (LightDM)

If the GUI login screen appears but fails to log in, follow these steps:

### 🔹 Step 1: Install LightDM and GTK Greeter

```bash
sudo apt install lightdm lightdm-gtk-greeter -y
```

### 🔹 Step 2: Configure LightDM to Use XFCE

```bash
sudo mkdir -p /etc/lightdm/lightdm.conf.d
echo -e "[Seat:*]\ngreeter-session=lightdm-gtk-greeter\nuser-session=xfce\nautologin-user-timeout=0" | sudo tee /etc/lightdm/lightdm.conf.d/99-xfce.conf
```

### 🔹 Step 3: Clean Up Session Files

```bash
sudo rm -f /var/lib/lightdm/.Xauthority
sudo rm -f ~/.Xauthority
sudo rm -rf ~/.cache/sessions
sudo chown -R $USER:$USER /home/$USER
```

### 🔹 Step 4: Restart LightDM

```bash
sudo systemctl restart lightdm
```

---

## 🧠 8. Control GUI Login Screen at Boot

By default, Ubuntu boots into terminal (CLI). You can toggle GUI login screen with these commands:

### 🔸 Disable GUI Login Screen (Boot to CLI)

```bash
sudo systemctl set-default multi-user.target
sudo systemctl disable lightdm
```

### 🔸 Enable GUI Login Screen (Boot to GUI)

```bash
sudo systemctl enable lightdm
sudo systemctl set-default graphical.target
```

### 🔸 Start/Stop GUI Manually (No Reboot)

- Start GUI:

```bash
sudo systemctl start lightdm
```

- Stop GUI:

```bash
sudo systemctl stop lightdm
```

---

## 🧰 9. Install GUI Applications (Optional)

```bash
sudo snap install code --classic     # Visual Studio Code
sudo apt install firefox -y          # Firefox Browser
sudo apt install gnome-system-monitor -y  # Task Manager
```

---

## 🎯 Summary

| Step | Action | Command |
|------|--------|---------|
| 1 | Update Server | `sudo apt update && sudo apt upgrade -y` |
| 2 | Install XFCE | `sudo apt install xfce4 xfce4-goodies -y` |
| 3 | Install XRDP | `sudo apt install xrdp -y` |
| 4 | Set XFCE Session | `echo xfce4-session > ~/.xsession` |
| 5 | Install LightDM + Greeter | `sudo apt install lightdm lightdm-gtk-greeter -y` |
| 6 | Configure LightDM | Create `99-xfce.conf` |
| 7 | Clean Session Files | Remove `.Xauthority` and cache |
| 8 | Control GUI Boot | Use `set-default` and `enable/disable lightdm` |
| 9 | Connect via RDP | Use `mstsc` on Windows |

---

### 🏁 Final Result

✅ XFCE desktop installed  
✅ RDP access working  
✅ GUI login screen optional  
✅ System stable and flexible
```
