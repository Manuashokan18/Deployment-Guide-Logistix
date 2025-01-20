# Setting Up Password-Less Git Access with SSH

This guide explains how to configure password-less access for Git operations on GitHub using SSH keys. Follow the steps below to eliminate the need to enter a username and password every time you interact with your Git repository.

---

## **1. Generate an SSH Key**

If you don’t already have an SSH key, generate one:

### For Ed25519 (Recommended):
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

### For RSA (If Required):
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- When prompted, save the key in the default location (`~/.ssh/id_ed25519` or `~/.ssh/id_rsa`).
- Leave the passphrase empty for automatic access, or set one for added security.

---

## **2. Add the SSH Key to Your SSH Agent**

Start the SSH agent:
```bash
eval "$(ssh-agent -s)"
```

Add your private key to the agent:
```bash
ssh-add ~/.ssh/id_ed25519
```

For RSA:
```bash
ssh-add ~/.ssh/id_rsa
```

---

## **3. Add the SSH Key to Your GitHub Account**

1. Copy the public key to your clipboard:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   (Replace `id_ed25519.pub` with `id_rsa.pub` if you used RSA.)

2. Add the key to GitHub:
   - Log in to GitHub.
   - Navigate to **Settings** > **SSH and GPG Keys** > **New SSH Key**.
   - Paste the key and give it a descriptive title (e.g., "Work Machine").

---

## **4. Configure Your Git Repository to Use SSH**

Update the remote URL of your Git repository to use SSH:
```bash
git remote set-url origin git@github.com:<username>/<repository>.git
```

Example:
```bash
git remote set-url origin git@github.com:strebentechnik/logixman-web.git
```

Verify the remote URL:
```bash
git remote -v
```

Expected output:
```
origin  git@github.com:<username>/<repository>.git (fetch)
origin  git@github.com:<username>/<repository>.git (push)
```

---

## **5. Test Your SSH Connection**

Verify that your SSH key is correctly configured:
```bash
ssh -T git@github.com
```

Expected output:
```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## **6. Perform Git Operations Without a Password**

After configuring SSH, you can use Git commands without entering a username or password.

Examples:
```bash
git pull
git push
```

---

## **7. Troubleshooting**

### **A. Permission Denied (Public Key)**
- Ensure your SSH key is added to GitHub.
- Check file permissions:
  ```bash
  chmod 600 ~/.ssh/id_ed25519
  chmod 700 ~/.ssh
  ```

### **B. Repository Not Found**
- Verify the repository URL.
- Ensure your account has access to the repository.

### **C. Still Prompted for Username/Password**
- Ensure the SSH key is added to the agent:
  ```bash
  ssh-add ~/.ssh/id_ed25519
  ```
- Confirm the remote URL uses SSH, not HTTPS:
  ```bash
  git remote -v
  ```

---

By following this guide, you’ll configure password-less SSH access for GitHub, improving convenience and security for your Git operations.

