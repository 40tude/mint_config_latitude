# Installation
* Boot sur clé USB
* Passer en clavier FR
* gparted pour supprimer toutes les partitions
* Installation de mint
* Reboot
* Utiliser Firefox et lire https://github.com/40tude/mint_config_latitude

<!-- ##################################################### -->
# Update & upgrade

```
sudo apt update
sudo apt dist-upgrade
sudo apt upgrade
```


<!-- ##################################################### -->
# Enable automatic update
```
sudo apt install unattended-upgrades
dpkg-reconfigure --priority=low unattended-upgrades
```




<!-- ##################################################### -->
# Enable hibernation

* https://forums.linuxmint.com/viewtopic.php?t=284100


```
free -h                                 # noter la taille de la RAM
swapon                                  # faut swap = RAM
sudo swapoff -a

SIZE=16                                 # 16 MB
sudo dd if=/dev/zero of=/swapfile bs=1M count=$(($SIZE * 1024))
sudo chmod 0600 /swapfile
sudo mkswap /swapfile
sudo sed -i '/swap/{s/^/#/}' /etc/fstab
sudo tee -a /etc/fstab<<<"/swapfile  none  swap  sw 0  0"

RESUME_PARAMS="resume=UUID=$(findmnt / -o UUID -n) resume_offset=$(sudo filefrag -v /swapfile|awk 'NR==4{gsub(/\./,"");print $4;}') "

if grep resume /etc/default/grub>/dev/null; then echo -e "\nERROR: Hibernation already configured. Remove the existing configuration from /etc/default/grub and add these parameters instead:\n$RESUME_PARAMS";else sudo sed -i "s/GRUB_CMDLINE_LINUX_DEFAULT=\"/GRUB_CMDLINE_LINUX_DEFAULT=\"$RESUME_PARAMS/" /etc/default/grub;fi

sudo update-grub

# Copy Paste the lines below as a whole 
sudo tee /etc/polkit-1/localauthority/50-local.d/com.ubuntu.enable-hibernate.pkla <<'EOB'
[Enable hibernate]
Identity=unix-user:*
Action=org.freedesktop.login1.hibernate;org.freedesktop.login1.handle-hibernate-key;org.freedesktop.login1;org.freedesktop.login1.hibernate-multiple-sessions
ResultActive=yes
EOB
```

* Reboot
* The answer to the line below should be `yes` (or click "the red button, hibernate should be available" )

```
busctl call org.freedesktop.login1 /org/freedesktop/login1 org.freedesktop.login1.Manager CanHibernate
```

<!-- ##################################################### -->
# Share a public folder with Windows 

* Read : https://techviewleo.com/configure-samba-file-sharing-on-linux-mint/

```
sudo apt install samba -y
sudo apt install wsdd -y
sudo service wsdd status
sudo mkdir -p /home/public
sudo chmod 777 /home/public
sudo nano /etc/samba/smb.conf
```

```
# Philippe : comment the 2 lines
# interfaces = 127.0.0.0/8 eth0
# bind interfaces only = yes

# Before ### Debugging/Accounting ###
# Philippe
   server min protocol = NT1
   ntlm auth = yes
   unix charset = UTF-8
   # Disable printer
   load printers = no
   printing = bsd
   printcap name = /dev/null
   disable spoolss = yes

# End of the file add the lines below
# Philippe
[Docs]
   path = /home/public
   writable = yes
   guest ok = yes
   guest only = yes
   create mode = 0777
   directory mode = 0777
```

* Save & exit

```
sudo systemctl restart smbd
sudo ufw allow samba
```




<!-- ##################################################### -->
# Enable SSH
```
sudo apt install openssh-server -y
sudo systemctl is-enabled ssh
# si besoin
sudo systemctl enable ssh 
sudo systemctl start ssh
sudo systemctl status ssh
# doit être running

# firewall
sudo ufw allow ssh

# si besoin mais normalement il est running
sudo systemctl is-enabled ssh
sudo ufw enable
sudo ufw reload
sudo ufw status verbose # si besoin
```




<!-- ##################################################### -->
# Configure TimeShift
* voir : https://www.youtube.com/watch?v=HKCowLHiQ8o&list=WL&index=8
* à 13:28


<!-- ##################################################### -->
# Install VSCode



<!-- ##################################################### -->
# Install Chrome
* S'assurer que le gestionnaire synaptic n'est PAS ouvert

```
# installez la clé de signature de package
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
# ajouter le référentiel Chrome 
sudo add-apt-repository "deb http://dl.google.com/linux/chrome/deb/ stable main"
sudo apt update
sudo apt install google-chrome-stable -y
```



<!-- ##################################################### -->
# Install Git

```
sudo apt install git -y
git config --global user.name "MON NOM"            # avec les guillemets
git config --global user.email xxx@gmail.com
git config --list
```


<!-- ##################################################### -->
## Enable SSH (pour se connecter à github sans VSCode)
* Voir : https://youtu.be/3O4ZmH5aolg?si=OVWCEeq_0nj-UExM&t=359
* Aller dans $HOME
```
mkdir .ssh
cd .ssh
ssh-keygen # Enter à chaque question posée
cat id_rsa.pub
```

* Copier la sortie de cat
* Aller sur github pour ajouter la clé publique au compte
* Cliquer profil en haut à droite
* Settings dans la liste
* A gauche de la page chercher SSH & GPG
* Click bouton New SSH Key
* Coller la clé publique






<!-- ##################################################### -->
# Curseur
* Thèmes/Para avancés/Souris/Yaru


<!-- ##################################################### -->
# Install MS Fonts
```
sudo apt install ttf-mscorefonts-installer
```


<!-- ##################################################### -->
# Install Meslo Fonts

* Aller sur : https://www.nerdfonts.com/font-downloads
* Recupérer Meslo
* Décompresser fichier zip
* Un répertoire Meslo est créé
* Enlever le readme.txt et le fichier licence.txt du répertoire
* Restent plus que des fichiers ttf
* Copier le répertoire Meslo dans home/.local/share/fonts
* Reconstruire le cache des fonts
* Lire : https://www.baeldung.com/linux/install-multiple-fonts

```
mkdir -p ~/.local/share/fonts
mv ./Téléchargements/Meslo ./.local/share/fonts
fc-cache -fv
```

## Lister les polices installées
```
fc-list
fc-list | grep Meslo
fc-list : family style | grep Meslo
fc-match -s Arial
```


<!-- ##################################################### -->
# Parce que je m'en rappelle jamais

## Créer un lien symbolique
* ln -s /home/share/installation.md ln_installation.md

## Changer le nom du PC
* Editer en root /etc/hostname







<!-- ##################################################### -->
# À finir
* VSCode
* Docker

## Poshgit
* https://ohmyposh.dev/docs/installation/linux
* curl -s https://ohmyposh.dev/install.sh | sudo bash -s
* Configurer Alacritty pour utiliser Meslo

## Visiter
* https://github.com/ibraheemdev/modern-unix

* zfish (bash) : https://azlinux.fr/installer-fish-shell/
* exa (ls) apt install -y exa
* bat (cat) apt install -y exa https://www.linode.com/docs/guides/how-to-install-and-use-the-bat-command-on-linux/
* ripgrep (rg, grep) apt install -y ripgrep
* Alacritty (terminal)


# Configuration Alacritty
Je suis en version 0.13.0-dev

Le fichier .toml n''est pas pris en compte
Le fichier .yml est pris en compte
mkdir -p ~/.config/alacritty/
/home/philippe/.config/alacritty/alacritty.toml
/home/philippe/.config/alacritty/alacritty.yml

Lire : 
* https://github.com/tmcdonell/config-alacritty/blob/master/alacritty.yml
* https://github.com/alacritty/alacritty/blob/master/extra/man/alacritty.5.scd
* https://github.com/alacritty/alacritty-theme


## Exemple de fichier .toml
```
[font]
size = 16.0

# [font.normal]
# family = "MesloLGM Nerd Font"
```


```
##################################################################
```

