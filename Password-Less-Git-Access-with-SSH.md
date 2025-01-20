# Setting Up Password-Less Git Access with SSH

This guide explains how to set up Git operations on GitHub without entering a password. Follow these steps to use SSH keys.

---

## **1. Generate an SSH Key**

If you do not have an SSH key, create one.

### For Ed25519 (Recommended):
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

### For RSA (If Needed):
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- Save the key in the default location (`~/.ssh/id_ed25519` or `~/.ssh/id_rsa`).
- Leave the passphrase empty for automatic access, or set one for extra security.

---

## **2. Add the SSH Key to Your System**

Start the SSH agent:
```bash
eval "$(ssh-agent -s)"
```

Add the private key to the agent:
```bash
ssh-add ~/.ssh/id_ed25519
```

If you use RSA:
```bash
ssh-add ~/.ssh/id_rsa
```

---

## **3. Add the SSH Key to GitHub**

1. Copy your public key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   If using RSA:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```

2. Go to GitHub settings:
   - Log in to GitHub.
   - Navigate to **Settings** > **SSH and GPG Keys** > **New SSH Key**.
   - Paste the public key and name it (e.g., "Work Machine").

---

## **4. Set Your Git Remote to Use SSH**

Update your repository URL to use SSH:
```bash
git remote set-url origin git@github.com:<username>/<repository>.git
```

Example:
```bash
git remote set-url origin git@github.com:strebentechnik/logixman-web.git
```

Check the remote URL:
```bash
git remote -v
```

You should see:
```
origin  git@github.com:<username>/<repository>.git (fetch)
origin  git@github.com:<username>/<repository>.git (push)
```

---

## **5. Test Your SSH Connection**

Check if your SSH key works:
```bash
ssh -T git@github.com
```

Expected output:
```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## **6. Use Git Without Password**

After setting up SSH, you can use Git commands without entering a username or password.

Examples:
```bash
git pull
git push
```

---

## **7. Troubleshooting**

### **A. Permission Denied**
- Ensure your SSH key is added to GitHub.
- Check permissions for your SSH key files:
  ```bash
  chmod 600 ~/.ssh/id_ed25519
  chmod 700 ~/.ssh
  ```

### **B. Repository Not Found**
- Verify the repository URL is correct.
- Ensure your GitHub account has access to the repository.

### **C. Still Asked for Password**
- Add the SSH key to the agent:
  ```bash
  ssh-add ~/.ssh/id_ed25519
  ```
- Confirm the remote URL is set to SSH:
  ```bash
  git remote -v
  ```

---

This guide helps you configure password-less Git access with SSH for convenience and security.

