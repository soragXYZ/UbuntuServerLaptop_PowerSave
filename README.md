# Basics
```
sudo apt update
sudo apt upgrade -y
sudo apt autoclean -y
sudo apt install powertop
```

# powertop
```
cat << EOF | sudo tee /etc/systemd/system/powertop.service
[Unit]
Description=Powertop tunings

[Service]
Type=oneshot
Environment="TERM=dumb"
RemainAfterExit=yes
ExecStart=/usr/sbin/powertop --auto-tune

[Install]
WantedBy=multi-user.target
EOF
```


# empecher veille fermeture lid
```
sudo sed -i 's/^#HandleLidSwitch=.*/HandleLidSwitch=ignore/' /etc/systemd/logind.conf
sudo sed -i 's/^#HandleLidSwitchExternalPower=.*/HandleLidSwitchExternalPower=ignore/' /etc/systemd/logind.conf
sudo sed -i 's/^#HandleLidSwitchDocked=.*/HandleLidSwitchDocked=ignore/' /etc/systemd/logind.conf
```


# Screen saving
```
cat << EOF | sudo tee /etc/systemd/system/screenoff.service
[Unit]
Description=Screen off

[Service]
Type=oneshot
Environment="TERM=linux"
RemainAfterExit=yes
ExecStart=/usr/bin/setterm -blank 1
StandardOutput=tty
TTYPath=/dev/console

[Install]
WantedBy=multi-user.target
EOF
```

```
sudo systemctl daemon-reload
sudo systemctl enable powertop.service
sudo systemctl enable screenoff.service
sudo reboot
```

# Optional: remove 2min timeout if connected to WiFi and not Ethernet
```
sed '/dhcp4: true/a \      optional: true' /etc/netplan/00-installer-config.yaml | sudo tee /etc/netplan/00-installer-config.yaml
sudo netplan apply
sudo reboot
```
