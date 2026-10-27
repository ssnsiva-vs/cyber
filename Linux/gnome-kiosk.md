# Configure a Fedora Server to boot directly into Google Chrome in Wayland kiosk mode, 

Use the GNOME Kiosk single-application session or a lightweight Wayland compositor like Weston or Sway. The cleanest native approach on Fedora is leveraging GNOME Kiosk Script Wayland, which runs a single-purpose full-screen window environment without a full desktop shell. [1, 2, 3] 
------------------------------
## Step 1: Install Google Chrome and GNOME Kiosk

   1. Install Google Chrome using the official repository:
   
   sudo dnf install fedora-workstation-repositories
   sudo dnf config-manager --set-enabled google-chrome
   sudo dnf install google-chrome-stable
   
   2. Install the GNOME Kiosk packages: [4] 
   
   sudo dnf install gnome-kiosk gnome-kiosk-script
   
   
------------------------------
## Step 2: Configure the Kiosk Startup Script

   1. Create or edit the user's kiosk script so it launches Google Chrome natively on Wayland pointing to your target URL or app:
   
   mkdir -p ~/.config
   cat << 'EOF' > ~/.config/gnome-kiosk-script
   #!/bin/sh
   exec google-chrome-stable --ozone-platform=wayland --kiosk "https://your-url-here.com"
   EOF
   chmod +x ~/.config/gnome-kiosk-script
   
   Note: If running as a dedicated user (e.g., kiosk), place this script inside /home/kiosk/.config/gnome-kiosk-script and adjust ownership with chown -R kiosk:kiosk /home/kiosk/.config. [3] 

------------------------------
## Step 3: Set Up Automatic Login and Kiosk Session

   1. Configure your display manager (GDM) or accounts service to automatically log your user into the Wayland kiosk session.
   2. Edit or create the user session configuration (for example, /var/lib/AccountsService/users/kiosk or your target user file):
   
   [User]
   Session=gnome-kiosk-script-wayland
   SystemAccount=false
   
   3. Enable automatic login in GDM (/etc/gdm/custom.conf): [3] 
   
   [daemon]
   AutomaticLoginEnable=True
   AutomaticLogin=kiosk
   
   
------------------------------
## Alternative: Using Weston (Standalone Wayland Compositor)
If you prefer not to use GNOME Kiosk, you can run Chrome inside Weston, a reference Wayland compositor, via a systemd user or system service: [2] 

   1. Install Weston:
   
   sudo dnf install weston
   
   2. Create a systemd service file at /etc/systemd/system/chrome-kiosk.service:
   
   [Unit]
   Description=Google Chrome Wayland Kiosk
   After=graphical.target
   
   [Service]
   User=kiosk
   Environment=XDG_RUNTIME_DIR=/run/user/1000
   ExecStart=/usr/bin/weston --shell=kiosk-shell.so --command="google-chrome-stable --ozone-platform=wayland --kiosk https://your-url-here.com"
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   
   3. Enable the service: [2] 
   
   sudo systemctl daemon-reload
   sudo systemctl enable --now chrome-kiosk.service
   
   
If you want to lock down the operating system further—such as disabling TTY terminal switching or setting up automatic crash recovery—let me know what specific URL or local web app the machine needs to load.

[1] [https://www.hexnode.com](https://www.hexnode.com/blogs/what-is-linux-kiosk-mode/)
[2] [https://community.element14.com](https://community.element14.com/products/devtools/single-board-computers/f/forum/11618/how-to-write-a-system-service-to-auto-startup-chromium-on-wayland-with-non-root-user)
[3] [https://fedoramagazine.org](https://fedoramagazine.org/build-a-kiosk-with-fedora-silverblue/)
[4] [https://www.youtube.com](https://www.youtube.com/watch?v=x3NVAEYoS68)
