# AFTER INSTALL
```
 sudo dnf makecache

 sudo dnf group upgrade core

 sudo dnf4 group install core

 sudo dnf update
```

```
 sudo dnf install unzip p7zip p7zip-plugins unrar typescript

 sudo dnf group install development-tools
```

GNOME Nautilus python bindings
```
 sudo dnf install nautilus-python
```

### Utilize ZRAM and SWAP
By default, Fedora uses lzo compression algorithm and 8 GB of swap memory. If you want to change size of swap memory or change compression algorithm based on your size of RAM memory and CPU performance, you can change default value inside of `/etc/systemd/zram-generator.conf`.

To do that, first we are gonna copy default config from `/usr/lib/systemd/zram-generator.conf`:
```
sudo cp /usr/lib/systemd/zram-generator.conf /etc/systemd/
```
Now our file `/etc/systemd/zram-generator.conf` will override default config. 

NEVER CHANGE VALUES INSIDE OF `/usr/lib/systemd/zram-generator.conf`!!! THOSE VALUES ARE DEFINED BY FEDORA AND COULD BE CHANGED AUTOMATICALLY IN SYSTEM UPDATE.

Since I have 32 GB of RAM, I don't need 8 GB of swap and I want faster ZRAM compression algorithm that has low CPU overhead. That's why I'll be using `lz4` compression algorithm and lower my swap to 4 GB of disk. You can also disable swap completely, but in case where whole 32 GB of RAM is used, system can crash. That's why I'll stick to 4 GB of swap memory.

This is my `zram-generator.conf` file:
```
[zram0]
zram-size = min(ram, 4096)
compression-algorithm = lz4
```

For more informations about `zram-generator.conf` options and compression algoritms you can check these links:

`zram-generator.conf`: [https://man.archlinux.org/man/zram-generator.conf.5](https://man.archlinux.org/man/zram-generator.conf.5)

`compression algoritms`: [https://hamradio.my/2025/01/the-role-of-compression-algorithms-in-zram](https://hamradio.my/2025/01/the-role-of-compression-algorithms-in-zram)


### Disable NetworkManager-wait-online.service
Disabling it can decrease the boot time by at least ~15s-20s:
```
 sudo systemctl disable NetworkManager-wait-online.service
```

### Hardware Acceleration
https://fedoraproject.org/wiki/Hardware_Video_Acceleration

### Disable Gnome Software from Startup Apps
Gnome Software by default autostarts on every boot

This takes at least 100 MB of RAM up to 900 MB

Skip this step if you want it to do updates in the background

```
 sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```

## NVIDIA (Not Complete)
### If the graphical interface (GUI) works fine after installation, do this step last:
### Option 1:
#### How to Install the NVIDIA Driver on GNOME:

- Open GNOME Software.
You can find it in the application menu or search for “Software.”

- Scroll to the bottom of the main screen.
Click on the “Hardware Drivers” category.

- Find the NVIDIA driver in the list.
Select the recommended NVIDIA driver for your system.

- Click “Install.”
Wait for the installation to complete. You may be prompted to enter your password.

- Restart your computer to apply the changes.
### Option 2:
#### Install the NVIDIA Driver in terminal:
```
 sudo dnf install akmod-nvidia

 sudo dnf install xorg-x11-drv-nvidia-cuda

 modinfo -F version nvidia # Check if the kernel module is built.
```
Restart your computer.
```
 sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1"
```
Restart again.

## Fish shell with plugins

### Install fish
```
 sudo dnf install fish
```
### Change default shell to fish
```
 chsh -s /usr/bin/fish
```
## CLI tools
- bat - better 'cat' alternative 
- fd - better 'find' alternative
- fzf - fuzzy search file
- eza - better 'ls' alternative
- thefuck - corrects errors in previous console commands

### install bat, fd, fzf, thefuck
```
 sudo dnf install bat fd fzf thefuck
```

### install eza using cargo
```
 sudo dnf install cargo
 
 cargo install eza
```

### creating alias to use bat and eza instead of cat and ls
```
 alias -s cat bat

 alias -s ll="eza -l --icons always"

 alias -s ls="eza --icons always"
```

### useful alias for typescript
```
 alias -s tsc="tsc --target es2022"

 alias -s tsrun="ts file.ts && node file.js"
```

## Starship shell theme
```
 curl -sS https://starship.rs/install.sh | sh
```

### installing fisher - fish plugin manager
```
 curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
```

### Install fish plugins
#### info for every plugin can be found on github, e.g. [github.com/jethrokuan/z](https://github.com/jethrokuan/z)
```
 fisher install jethrokuan/z \
	patrickf1/fzf.fish \
	franciscolourenco/done \
	jorgebucaran/autopair.fish \
	decors/fish-colored-man \
	gazorby/fish-abbreviation-tips \
	nickeb96/puffer-fish \
	jorgebucaran/nvm.fish \
	realiserad/fish-ai
```


### Fish Config
```
 nano ~/.config/fish/config.fish
```

Paste this in config file:
```
# Fish Welcome message
set -g fish_greeting ""

# Starship
starship init fish | source

# thefuck
thefuck --alias | source

# cargo
export PATH="$HOME/.cargo/bin:$PATH"

```

## FLATPAKS

### Add Flathub repo
```
 flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

### Switch Fedora flatpaks with Flathub flatpaks
```
 flatpak install --reinstall flathub $(flatpak list --app-runtime=org.fedoraproject.Platform --columns=application | tail -n +1 )
```

### Remove unused flatpaks
```
 flatpak uninstall --unused
```

### SWITCH REMAINING FEDORA FLATPAKS MANUALLY
### list all flatpak apps
```
 flatpak list
```

### find all apps installed from fedora instead of flathub, remove them and install from flathub
```
 flatpak uninstall <fedora-app

 flatpak install <flathub-app
```

### Set Gnome Software to prefer Flathub flatpaks
```
 gsettings set org.gnome.software packaging-format-preference "['flatpak:flathub', 'flatpak:fedora-testing', 'flatpak:fedora', 'rpm']"
```

### Install Apps
```
 flatpak install net.nokyan.Resources \
       com.getpostman.Postman \
       io.beekeeperstudio.Studio \
       com.brave.Browser \
       com.github.ryonakano.pinit \
       org.qbittorrent.qBittorrent \
       com.mattjakeman.ExtensionManager \
       io.github.flattool.Warehouse \
       io.github.giantpinkrobots.flatsweep \
       io.github.diegoivan.pdf_metadata_editor \
       com.rtosta.zapzap \
       io.github.getnf.embellish \
       org.libreoffice.LibreOffice \
       org.gimp.GIMP \
       com.github.tchx84.Flatseal \
       com.github.jeromerobert.pdfarranger \
       org.gnome.Papers \
       io.github.nokse22.inspector \
       org.gnome.Boxes \
       org.gnome.Showtime \
       com.stremio.Stremio \
       com.obsproject.Studio
```

 - resources  (```net.nokyan.Resources```) - task manager
 - pinit (```com.github.ryonakano.pinit```) - app for manually adding app launcher for executable
 - extension manager (```com.mattjakeman.ExtensionManager```) - for managing gnome extensions
 - warehouse (```io.github.flattool.Warehouse```) - toolbox for flatpaks
 - flatsweep (```io.github.giantpinkrobots.flatsweep```) - gui app for cleaning unused flatpak data
 - paper clip (```io.github.diegoivan.pdf_metadata_editor```) - pdf metadata editor
 - zapzap (```com.rtosta.zapzap```) - whatsapp client
 - embellish (```io.github.getnf.embellish```) - fonts downloader
 - libreoffice (```org.libreoffice.LibreOffice```) - office
 - gimp (```org.gimp.GIMP```) - photo editor
 - flatseal (```com.github.tchx84.Flatseal```) - gui app for managing flatpak permissions
 - pdf arranger (```com.github.jeromerobert.pdfarranger```) - for merging, spliting, rotating, croping, and rearranging pdf files
 - papers (```org.gnome.Papers```) - pdf viewer
 - inspector (```io.github.nokse22.inspector```) - for viewing system info
 - boxes (```org.gnome.Boxes```) - for virtual machines
 - showtime (```org.gnome.Showtime```) - video player


## Microsoft fonts - required for using MS Office fonts in LibreOffice
```
 sudo dnf install curl cabextract xorg-x11-font-utils fontconfig

 sudo rpm -ivh --nodigest --nofiledigest https://li.nux.ro/download/nux/dextop/el7/x86_64/webcore-fonts-vista-3.0-1.noarch.rpm https://li.nux.ro/download/nux/dextop/el7/x86_64/webcore-fonts-3.0-1.noarch.rpm
```

## Removing unnecessary apps
```
 sudo dnf rm totem

 sudo dnf group remove libreoffice
```

## GNOME EXTENSIONS (Recommended extensions)
More info for every extension can be found on [extensions.gnome.org](https://extensions.gnome.org)
```
 sudo dnf install gnome-shell-extension-appindicator \
	gnome-shell-extension-blur-my-shell \
	gnome-shell-extension-caffeine \
	gnome-shell-extension-dash-to-dock \
	gnome-shell-extension-gsconnect
```
	
- App Indicator - app indicators to be shown in top bar
- Blur my shell - ui blur
- Caffeine - button in system menu (right corner in top bar) for disabling temporarily screensaver and auto suspend when you work
- Dash to Dock - dock on bottom of screen (alternatively dash-to-panel can be used for more windows like ui look)
- GSConnect - for connecting phone with pc, remote control from phone, data transfer, sync notifications, media control...

### Rest of extensions install using Extension Manager
- Window Gestures - better and configurable touchpad gestures
- Astra Monitor - system monitor for topbar (performance metrics like CPU, GPU, RAM, disk usage, network statistics...)
- Clipboard Indicator - clipboard manager in topbar
- PIP on top (for firefox based browsers) - keeps Picture-in-Picture popup above windows on all workspaces
- Tiling Assistent or Tiling Shell ( I prefer Tilling Assistent ) - for tiling windows (GNOME by default can tile window only to 1/2 screen instead of 1/4 of screen)
- Window Thumbnails - Picture-in-Picture of window

## Making theme consistent

### Install adw-gtk3-theme for gtk3 apps
```
 sudo dnf install adw-gtk3-theme

 flatpak install org.gtk.Gtk3theme.adw-gtk3 org.gtk.Gtk3theme.adw-gtk3-dark

 gsettings set org.gnome.desktop.interface gtk-theme 'adw-gtk3-dark' && gsettings set org.gnome.desktop.interface color-scheme 'prefer-dark'

 sudo flatpak override --filesystem=xdg-data/themes

 sudo flatpak mask org.gtk.Gtk3theme.adw-gtk3

 sudo flatpak mask org.gtk.Gtk3theme.adw-gtk3-dark
```

## JetBrains IDEs
JetBrains IDEs are installed using JetBrains Toolbox.

Download Toolbox app from
[https://www.jetbrains.com/toolbox-app/](https://www.jetbrains.com/toolbox-app/)
(select .tar.gz file)

Extract the file and make it executable as a program:
```
tar -xf jetbrains-toolbox-X.X.X.XXXXX.tar.gz

cd jetbrains-toolbox-X.X.X.XXXXX/

chmod +x jetbrains-toolbox/bin

./jetbrains-toolbox
```

Note: You can disable Toolbox from running at system startup in the Toolbox settings.

## VS Code
Download VS Code from [https://code.visualstudio.com/](https://code.visualstudio.com/) as .rpm

## Docker
Install Guide: [https://docs.docker.com/engine/install/fedora/](https://docs.docker.com/engine/install/fedora/)

## OR PODMAN
https://podman.io/docs/installation

```
 sudo dnf install podman podman-compose podman-docker podman-tui
```

## Distrobox
```
sudo dnf install distrobox
```

## Steam
```
sudo dnf install steam steam-devices
```

## Lutris
```
sudo dnf install lutris
```

## Dolphin Emulator
```
flatpak install org.DolphinEmu.dolphin-emu
```

## Neovim & LazyVim Install
```
 sudo dnf install neovim

 git clone https://github.com/LazyVim/starter ~/.config/nvim

 nvim

```

### Options:
~/.config/nvim/lua/config/options.lua :
```
vim.opt.scrolloff = 8
vim.opt.sidescrolloff = 30
vim.opt.updatetime = 50
```

#### NeoVim plugins:
VS Code Color scheme:

~/.config/nvim/lua/plugins/vscode.lua
```
return {
  {
    "Mofiqul/vscode.nvim",
    opts = {
      -- transparent = true,
      -- color_overrides = {
      --   vscFront = "#ffffff",
      -- },
      -- group_overrides = {
      --   CursorLine = { bg = "#262626" },
      --   LineNr = { fg = "#569cd6" },
      --   NeoTreeCursorLine = { bg = "#383838" },
      --   NeoTreeDirectoryName = { fg = "#569cd6", bg = "NONE" },
      --   NeoTreeRootName = { fg = "#dcdcaa", bg = "NONE" },
      -- },
    },
  },
  {
    "LazyVim/LazyVim",
    opts = {
      colorscheme = "vscode",
    },
  },
}
```

Python activate venv in current project dir in lazyvim
~/.config/nvim/lua/plugins/python.lua
```
return {
  {
    "neovim/nvim-lspconfig",
    opts = {
      servers = {
        pyright = {
          settings = {
            python = {
              venvPath = ".",
              pythonPath = "./.venv/bin/python",
            },
          },
        },
      },
    },
  },
}
````

Old Which Key preset:

~/.config/nvim/lua/plugins/which-key.lua
```
return {
  "folke/which-key.nvim",
  opts = {
    preset = "modern",
  },
}
```

Rainbow Delimiters:

~/.config/nvim/lua/plugins/rainbow-delimiters.lua
```
return {
  "HiPhish/rainbow-delimiters.nvim",
}
```
