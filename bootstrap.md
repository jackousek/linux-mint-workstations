
# On the new Linux Mint PC:

# Update package lists
sudo apt update

# Install SSH and Vim
sudo apt install openssh-server vim

# Verify SSH is running
systemctl is-active ssh

# Bootstrap the Ansible account
sudo adduser --disabled-password --gecos "" ansible
sudo usermod -aG sudo ansible

# Configure passwordless sudo for Ansible
sudo EDITOR=/usr/bin/vim VISUAL=/usr/bin/vim visudo -f /etc/sudoers.d/ansible

# Add this line inside visudo:
# ansible ALL=(ALL:ALL) NOPASSWD: ALL


# On the Ansible server:

# Load the Ansible public key into a shell variable
ANSIBLE_PUBKEY="$(cat ~/.ssh/ansible_ed25519.pub)"

# Install the Ansible public key on the new workstation
ssh -t itadmin@IP_OF_MINT_PC \
"sudo install -d -m 700 -o ansible -g ansible /home/ansible/.ssh &&
 echo '$ANSIBLE_PUBKEY' | sudo tee /home/ansible/.ssh/authorized_keys >/dev/null &&
 sudo chown ansible:ansible /home/ansible/.ssh/authorized_keys &&
 sudo chmod 600 /home/ansible/.ssh/authorized_keys"

# Test SSH access
ssh -i ~/.ssh/ansible_ed25519 ansible@IP_OF_MINT_PC

# Test passwordless sudo
ssh -i ~/.ssh/ansible_ed25519 ansible@IP_OF_MINT_PC 'sudo -n id'


# Deploy workstation configuration
ansible-playbook \
  -i inventory/test/hosts.yml \
  playbooks/workstation.yml \
  --limit HOSTNAME \
  --ask-vault-pass

# Reboot the workstation after successful deployment
# and verification of SSH access.
# Run this on the Mint workstation:
sudo reboot


