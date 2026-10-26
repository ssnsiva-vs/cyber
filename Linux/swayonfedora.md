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

