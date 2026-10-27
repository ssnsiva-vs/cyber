# Sway on Fedora
No SDDM or Desktop Manager. Only Window Manager.

Setting up Sway from a minimal Fedora Server installation requires manual configuration to ensure permissions, audio, and graphics initialize properly without a heavy desktop environment.

1. Install Graphic Drivers and XDG Portals

First, ensure your graphics pipeline and desktop portals (required for screen sharing and file dialogues) are installed:
• Intel/AMD GPUs: sudo dnf install mesa-dri-drivers
• NVIDIA GPUs: Requires the proprietary driver (akmod-nvidia) and the nvidia-vaapi-driver wrapper. Note that NVIDIA requires setting the WLR_NO_HARDWARE_CURSORS=1 environment variable.
• XDG Portals: sudo dnf install xdg-desktop-portal xdg-desktop-portal-wlr

2. Audio & Session Pipewire Setup

Minimal installs lack an audio server and seat management daemon. Install PipeWire and seatd:
• Audio: sudo dnf install pipewire pipewire-pulseaudio wireplumber
• Seat Management: sudo dnf install seatd
• Enable Seatd: Run sudo systemctl enable --now seatd and add your user to the group via sudo usermod -aG _seatd $USER (then log out and back in).

3. Launching Directly from TTY (Recommended)

Avoid installing a bulky display manager (like GDM or SDDM). Instead, log in via the regular text console (TTY) and start Sway manually.
To ensure applications choose Wayland over X11 by default, configure your environment variables first. Add these lines to your ~/.bash_profile or ~/.profile:
bash
# Enable Wayland for various toolkits
export MOZ_ENABLE_WAYLAND=1
export QT_QPA_PLATFORM=wayland
export XDG_SESSION_TYPE=wayland
export XDG_CURRENT_DESKTOP=sway

# Optional: Auto-start Sway upon login on TTY1
if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
  exec sway
fi
Use code with caution.

4. Configuration Directory

Sway requires a configuration file to open properly. If it fails to boot into a blank screen with a status bar, copy the default template to your home folder:
bash
mkdir -p ~/.config/sway
cp /etc/sway/config ~/.config/sway/config
Use code with caution.

To set up Fedora Server with Sway and launch it directly from a TTY without a display or login manager (like GDM), install the Sway package group and configure your shell profile to start Sway upon logging into a TTY. [1, 2] 
## 1. Install Sway Packages
Log into your Fedora Server via TTY or SSH, then update and install Sway and its base configuration:

sudo dnf install -y @swaywm sway-config-fedora foot alacritty

## 2. Configure Automatic Launch on TTY Login
To start Sway automatically when you log into a specific TTY (for example, tty1), add an auto-start check to your shell profile. [1] 
Edit your ~/.bash_profile (or ~/.profile): [1] 

nano ~/.bash_profile

Add the following code block to the bottom of the file. This checks if you are on tty1 and automatically starts Sway with the required Wayland and XDG environment variables:

if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
  export XDG_CURRENT_DESKTOP=sway
  export QT_QPA_PLATFORM=wayland
  export SDL_VIDEODRIVER=wayland
  export XDG_SESSION_TYPE=wayland
  exec swayfi

## 3. Disable or Bypass Any Display Manager
If a display manager (like GDM, SDDM, or LightDM) was accidentally installed, disable its systemd service so it doesn't start on boot:

sudo systemctl disable --now gdm

Ensure your system boots into multi-user target (text-based TTY mode) rather than graphical target:

sudo systemctl set-default multi-user.target

## 4. Copy and Customize Sway Configuration
Copy the default Fedora Sway configuration into your user directory so you can customize keybindings, terminals, and output settings: [3] 

mkdir -p ~/.config/sway
cp /etc/sway/config ~/.config/sway/config

Reboot your server. It will drop to a text TTY1 prompt; log in with your username and password, and Sway will launch automatically. [1] 
If you run into permission issues with Wayland graphics or multi-monitor scaling, let me know your GPU brand (Intel, AMD, or Nvidia) so we can adjust the environment variables.

[1] [https://www.reddit.com](https://www.reddit.com/r/swaywm/comments/lv2336/launch_sway_without_a_display_manager_on_login/)
[2] [https://computingforgeeks.com](https://computingforgeeks.com/setup-sway-tiling-window-manager-on-fedora-with-waybar/)
[3] [https://fedoramagazine.org](https://fedoramagazine.org/setting-up-the-sway-window-manager-on-fedora/)
