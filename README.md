# macOS Ansible Setup

Ansible playbook for automating macOS configuration and application setup.  
Includes personal preferences, essential system defaults, Homebrew packages, and Dock customization.

## Features

- System defaults (settings) tuning  
- Homebrew formulae, casks, taps (packages) installation  
- Dock configuration (using `geerlingguy.mac` collection)  
- Quick setup for (new) macOS machines  

## Requirements

- macOS (tested on Tahoe)
- Internet connection
- Administrator account
- Xcode Command Line Tools (can be installed automatically via Homebrew)

## Usage

### 0. (Optional) Grant terminal full disk access
For advanced use (e.g., modifying certain system files), go to **System Settings → Privacy & Security → Full Disk Access** and add your terminal app (Terminal, iTerm, etc.).

### 1. Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Install Ansible

```bash
brew install ansible
```

### 3. Clone this repository

```bash
git clone https://github.com/MarkHeemskerk/macos.git
cd macos
```

### 4. Create encrypted sudo password variable

Ansible needs your sudo password to install certain Homebrew packages and apply system settings. Store it securely with Ansible Vault.

```bash
# Replace 'SUDO_PASSWORD' with your actual sudo password
ansible-vault encrypt_string 'SUDO_PASSWORD' --name 'ansible_become_pass' > group_vars/all/secret.yml
```

You will be asked to create and confirm a **vault password** – remember this password, you'll need it when running the playbook.

### 5. Install required Ansible collections

This playbook uses `geerlingguy.mac` for Dock management.

```bash
ansible-galaxy collection install -r requirements.yml
```

### 6. Customize configuration

Edit the variable files to match your preferences:

- `group_vars/all/dock.yml` – applications in your Dock  
- `group_vars/all/general.yml` – general system settings
- `group_vars/all/homebrew.yml` – packages to install via Homebrew
- `group_vars/all/osx_defaults.yml` – macOS `defaults` commands

Remove or comment out anything you don't want to apply.

### 7. Run the playbook

```bash
ansible-playbook main.yml -K -J
```

You will be prompted for:

- **BECOME password** – your sudo password (the one you encrypted in step 4)  
- **Vault password** – the vault password you set in step 4

After entering both, the playbook will start configuring your system.

## Notes

- The playbook is idempotent – you can safely run it multiple times.  
- Some settings may require a logout/restart to take full effect.  
- If you change your sudo password later, re-run step 4 to update the encrypted variable.

