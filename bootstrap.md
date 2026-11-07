#On the new Mint PC:

sudo apt update
sudo apt full-upgrade
sudo reboot

#After reboot

cat /etc/os-release

#Enable SSH
sudo apt install openssh-server vim
#sudo systemctl enable --now ssh

#systemctl status ssh

#Bootstrap the ansible account
sudo adduser ansible
sudo usermod -aG sudo ansible

export EDITOR=vim

sudo visudo -f /etc/sudoers.d/ansible
ansible ALL=(ALL:ALL) NOPASSWD: ALL



#Then copy it:
ssh-copy-id -i ~/.ssh/ansible_ed25519.pub ansible@IP_OF_MINT_PC

#Test:
ssh -i ~/.ssh/ansible ansible@IP_OF_MINT_PC

