{ config, pkgs, ... }:

{
  imports = [ ./hardware-configuration.nix
              <home-manager/nixos>

           ];

  nix.settings.experimental-features = [ "nix-command" "flakes" ];
  nix = {
    package = pkgs.nixFlakes;
    extraOptions = " experimental-features = nix-command flakes";
  };

  boot.loader.grub = {
    enable = true;
    device = "/dev/nvme0n1";
    useOSProber = true;
    theme = pkgs.stdenv.mkDerivation {
pname = "distro-grub-themes";
  version = "3.1";
  src = pkgs.fetchFromGitHub {
    owner = "AdisonCavani";
    repo = "distro-grub-themes";
    rev = "v3.1";
    hash = "sha256-ZcoGbbOMDDwjLhsvs77C7G7vINQnprdfI37a9ccrmPs=";
  };
  installPhase = "cp -r customize/nixos $out";
    };
  };

  
  hardware.bluetooth.enable = true;
  services.blueman.enable = true;

  networking = {
    hostName = "nixos";
    wireless = {
      enable = true;
      networks = {
        briggsx1 = { psk = "Jr343434"; };
        NETGEAR48-5G = { psk = "newvalley342"; };
      };
    };
  };

  time.timeZone = "America/Los_Angeles";
  i18n.defaultLocale = "en_US.utf8";

  services = {
    pipewire = {
      enable = true;
      alsa.enable = true;
      alsa.support32Bit = true;
      pulse.enable = true;
    };
    printing.enable = true;
    xserver = {
      enable = true;
      displayManager.lightdm.enable = true;
      desktopManager = {
        xfce.enable = true;
        cde.enable = true;
      };
      layout = "us";
      xkbVariant = "";
      windowManager = {
        herbstluftwm.enable = true;
        stumpwm.enable = true;
        fvwm2.enable = true;
      };
    };
  };

  sound.enable = true;
  hardware.pulseaudio.enable = false;
  security.rtkit.enable = true;

    programs.zsh.enable = true; 
    users = {
      defaultUserShell = pkgs.zsh;
      users.thor = {
        isNormalUser = true;
        description = "thor";
        extraGroups = [ "networkmanager" "wheel" ];
      };
    };

  nixpkgs.config = {
    allowUnfree = true;
    allowBroken = true;
    packageOverrides = pkgs: {
      nur = import (builtins.fetchTarball "https://github.com/nix-community/NUR/archive/master.tar.gz") {
        inherit pkgs;
      };
    };
  };

  fonts.fonts = with pkgs; [
    emacs-all-the-icons-fonts
    font-awesome
    hack-font
    nerdfonts
  ];

  environment.systemPackages = with pkgs; [
    papirus-folders
    gnumake42
    gnumake
    bat
    cdesktopenv
    fzf
    btop
    nix-prefetch-git
    dunst
    rofi
    plank
    catppuccin-gtk
    ytfzf
    mpv
    banner
    pipes
    boxes
    cbonsai
    catimg
    cmatrix
    cava
    tty-clock
    home-manager
    lsd
    pywal
    wget
    xorg.xinit
    xorg.xorgserver
    xorg.xrdb
    xorg.xmodmap
    git
    obsidian
    brave
    blueman
    firefox
    emacs
    gotop
    gtop
    gcc
    yafetch
    bunnyfetch
    screenfetch
    neofetch
    pfetch
    cowsay
    feh
    syncthing
    polybar
    sxhkd
    dmenu
    picom
    ranger
    screenkey
    youtube-dl
    (st.overrideAttrs (oldAttrs: rec {
      src = fetchFromGitHub {
        owner = "LukeSmithxyz";
        repo = "st";
        rev = "8ab3d03681479263a11b05f7f1b53157f61e8c3b";
        sha256 = "1brwnyi1hr56840cdx0qw2y19hpr0haw4la9n0rqdn0r2chl8vag";
      };
      buildInputs = oldAttrs.buildInputs ++ [ harfbuzz ];
    }))
  ];

  system.stateVersion = "23.11";
}
