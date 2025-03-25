https://roadmap.sh/projects/ssh-remote-server-setup

# AWS Remote Linux Server Setup & SSH Configuration

## Project Overview
This project involves setting up a remote Linux server on AWS and configuring it to allow SSH connections using multiple SSH key pairs. The goal is to learn and practice the basics of Linux server administration and SSH authentication.

## Prerequisites
- An AWS account
- AWS CLI installed (optional but recommended)
- Basic knowledge of Linux commands

---

## Step 1: Launch an EC2 Instance
1. Log in to your **AWS Console**.
2. Navigate to **EC2 Dashboard** → **Instances**.
3. Click **Launch Instance** and configure:
   - **AMI (Amazon Machine Image):** Ubuntu 22.04 LTS (or preferred Linux distro)
   - **Instance Type:** `t2.micro` (Free Tier eligible)
   - **Key Pair:** Create a new key pair (e.g., `aws-key.pem`) and download it.
   - **Security Group:** Allow **SSH (port 22)** from your IP.
4. Click **Launch** and note the **Public IPv4 address** of the instance.

---

## Step 2: Connect to the Instance using the AWS Key Pair
Ensure the downloaded key has the correct permissions:
```sh
chmod 400 aws-key.pem
```
Connect to the instance:
```sh
ssh -i aws-key.pem ubuntu@your-server-ip
```

---

## Step 3: Generate and Add Additional SSH Keys
On your **local machine**, generate two additional SSH key pairs:
```sh
ssh-keygen -t rsa -b 4096 -C "aws-key-1"
ssh-keygen -t rsa -b 4096 -C "aws-key-2"
```
Save them in `~/.ssh/` (e.g., `~/.ssh/aws-key-1` and `~/.ssh/aws-key-2`).

Copy the public keys to the AWS instance:
```sh
ssh-copy-id -i ~/.ssh/aws-key-1.pub ubuntu@your-server-ip
ssh-copy-id -i ~/.ssh/aws-key-2.pub ubuntu@your-server-ip
```
If `ssh-copy-id` is not available, manually add the keys:
```sh
cat ~/.ssh/aws-key-1.pub >> ~/.ssh/authorized_keys
cat ~/.ssh/aws-key-2.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

---

## Step 4: Test SSH Connections
Verify that both new keys work:
```sh
ssh -i ~/.ssh/aws-key-1 ubuntu@your-server-ip
ssh -i ~/.ssh/aws-key-2 ubuntu@your-server-ip
```

---

## Step 5: Configure SSH for Easier Access
On your **local machine**, edit `~/.ssh/config`:
```ini
Host aws-server
    HostName your-server-ip
    User ubuntu
    IdentityFile ~/.ssh/aws-key-1
```
Now, you can connect using:
```sh
ssh aws-server
```

---

## Stretch Goal: Install and Configure Fail2Ban
1. Install Fail2Ban:
   ```sh
   sudo apt update && sudo apt install fail2ban -y
   ```
2. Configure Fail2Ban to protect SSH:
   ```sh
   sudo nano /etc/fail2ban/jail.local
   ```
   Add:
   ```ini
   [sshd]
   enabled = true
   maxretry = 3
   bantime = 600
   ```
3. Restart Fail2Ban:
   ```sh
   sudo systemctl restart fail2ban
   sudo systemctl enable fail2ban
   ```

---

## Summary
After completing this setup, you should be able to SSH into your AWS instance using multiple keys and an alias. Additionally, Fail2Ban provides security against brute-force attacks.

✅ **Do NOT share your private keys publicly!** ✅

Let me know if you have any questions! 🚀

