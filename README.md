# Quick command

Copying and pasting are pain in the ass if there are 20 VM/LXC in the fleet. So I compose these command.

These commands are one-liner so that they can be easily copy and paste into the terminal

## Set up auto update

```bash
cat << EOF > update.sh && chmod +x ./update.sh && ./update.sh && (crontab -u $(whoami) -l; echo "15 7 * * * /bin/bash /root/update.sh" ) | crontab -u $(whoami) -
#!/bin/bash
apt-get update
apt-get upgrade -y
apt-get autoclean
EOF
```
## Set up apt cache server

The apt cache server in this case, runs on 172.21.1.10:3142

```bash
apt update && echo "Acquire::http::Proxy \"http://172.21.1.10:3142\";" > /etc/apt/apt.conf.d/00aptproxy  && apt update && apt upgrade -y
```

The reason doing an `apt update` first is that Ubuntu needs to update key information.

## Enable UTF-8 locale in Ubuntu

```bash
sed -i 's/^# \(C\.UTF-8\|en_US\.UTF-8\) UTF-8$/\1 UTF-8/' /etc/locale.gen && locale-gen
```

## Download update signature from apt server, bypassing the proxy server

```bash
sed -i.bak 's/^/#/' '/etc/apt/apt.conf.d/00aptproxy' && apt update && sudo sed -i.bak 's/#//g' '/etc/apt/apt.conf.d/00aptproxy'
```
