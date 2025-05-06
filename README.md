
# MITRE-Turla-Carbon

This project simulates **Phase 1 - "Carbon"** of the **MITRE Turla** adversary emulation scenario released in 2023. It replicates a multi-phase cyber espionage campaign conducted by the Turla threat group, targeting high-value government and defense-related entities.

## 📌 Scenario Summary

In this phase, the Turla group:

1. **Initial Access**: Gains entry via a spearphishing email delivering a fake software installer.
2. **Execution**: Installs the EPIC backdoor onto the victim machine.
3. **Persistence & C2**: Establishes persistence and command-and-control (C2) channels.
4. **Discovery**: Identifies and accesses the Domain Controller.
5. **Lateral Movement**: Deploys `CARBON-DLL` into the Windows environment.
6. **Linux Compromise**: Moves laterally to a Linux Apache web server and installs `PENGUIN` to create a watering hole.

## ⚙️ Lab Requirements

The emulation uses the MITRE ATT&CK Evaluations range configuration, requiring specific Windows and Linux operating systems. Provisioning is automated using Ansible.

## 🔧 Lab Setup Instructions

1. **Login** to the host: `support-dns-srv1`
2. **Clone the repo**:

   ```bash
   git clone https://github.com/scottbrumley/mitre-turla-config.git
   cd mitre-turla-config
   ```

3. **Initialize Ansible**:

   ```bash
   ./init.sh
   ```

4. **Run main lab configuration**:

   ```bash
   ansible-playbook -i inventory runall.yml --ask-pass -u adminuser
   ```

## 🔄 Operational Commands

### Update GitHub Content

```bash
git pull origin main
```

### Ping All Hosts

```bash
ansible-playbook -i inventory pingall.yml --ask-pass -u lab-user
```

### Restart Redirectors

```bash
ansible-playbook -i inventory redirectors.yml --ask-pass -u lab-user --ask-become-pass
```

### Disable Microsoft Defender (Destructive)

```bash
ansible-playbook -i inventory disable_defender.yml --ask-pass -u lab-user --ask-become-pass
```

## 🛡️ XDR Agent Installation

### Step 1: Create/Edit Secrets Vault

Create:

```bash
ansible-playbook create_secrets.yml
```

Edit:

```bash
ansible-playbook edit_secrets.yml
```

Vault content format:

```yaml
xsiamKEY: <XSIAM API Key>
xsiamID: <XSIAM API ID>
xsiamURL: <XSIAM API URL>
xsiamWINDIST: <Windows Installer Distribution ID>
xsiamLINDIST: <Linux Installer Distribution ID>
falconCID: <CrowdStrike Falcon Customer ID>
```

### Step 2: Retrieve Required Info

- **XSIAM API URL**:  
  Settings → Integrations → API Keys → Copy API URL

- **API Key & ID**:  
  - Create key with Standard Security Level
  - Role: *Instance Administrator* or *Account Admin*
  - Copy generated key and ID

- **Installer Distribution IDs**:  
  Endpoints → Agent Instructions → ⋮ → Show ID → Copy Windows and Linux IDs

### Step 3: Install Agents

```bash
ansible-playbook -i inventory xdr_agent_install.yml --ask-pass -u lab-user --ask-become-pass --ask-vault-pass
```

### Step 4: Validate in XSIAM

Navigate to: `Endpoints → All Endpoints`

### Step 5: Grouping Best Practice

- **Servers**: bannik, brieftragerin, kagarov  
- **Workstations**: domovoy, khabibulin, hobgoblin

## 🧰 Script Overview

| Script                     | Description                                                                 |
|----------------------------|-----------------------------------------------------------------------------|
| `runall.yml`              | Full environment setup including users, Exchange servers, hostnames, etc.  |
| `pingall.yml`             | Pings all inventory hosts to check if they're online                        |
| `redirectors.yml`         | Restarts HTTP redirectors                                                   |
| `disable_defender.yml`    | Disables Windows Defender (irreversible without manual steps)              |
| `xdr_agent_install.yml`   | Installs Cortex XDR agents using XSIAM API and Ansible Vault                |
| `create_secrets.yml`      | Creates the encrypted Ansible Vault secrets file                            |
| `edit_secrets.yml`        | Opens existing Vault for editing                                            |

## 📁 Repo Structure

```
mitre-turla-config/
├── README.md
├── init.sh
├── runall.yml
├── inventory/
├── pingall.yml
├── redirectors.yml
├── disable_defender.yml
├── xdr_agent_install.yml
├── create_secrets.yml
├── edit_secrets.yml
├── scripts/
└── docs/
```

## 📚 References

- [MITRE Turla Evaluation 2023](https://attackevals.mitre-engenuity.org/adversaries/turla/)
- [ESET on CARBON](https://www.welivesecurity.com/2017/10/17/turla-carbon-analysis/)
- [Kaspersky on PENGUIN](https://securelist.com/the-penguin-turla-2-0/72084/)

---

**Disclaimer**: This project is for **research and educational purposes only**. Do not run outside of a secure, isolated lab environment.
