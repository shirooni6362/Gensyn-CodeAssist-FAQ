# Gensyn CodeAssist - Complete FAQ & Troubleshooting Guide

> **Last Updated:** November 6, 2024  
> **Version:** 1.0  
> **Community-Driven Support Resource**

This comprehensive guide consolidates **all confirmed issues, solutions, and best practices** from the Gensyn CodeAssist Discord support channel, official documentation, and community testing. Created to help you resolve issues faster and get coding immediately.

---

## 📑 Table of Contents

### Quick Navigation
- [🆘 Most Common Issues (Start Here!)](#-most-common-issues-start-here)
- [⚡ Quick Troubleshooting Checklist](#-quick-troubleshooting-checklist)

### Installation Issues
- [❌ "command not found: uv"](#-command-not-found-uv)
- [❌ "command not found: docker"](#-command-not-found-docker)
- [⏳ Installation Stuck on "Setting up container"](#-installation-stuck-on-setting-up-container)
- [🔧 Missing Dependencies (Python/UV/Docker)](#-missing-dependencies-pythonuvdocker)
- [🐧 Linux: "Running as root without --no-sandbox"](#-linux-running-as-root-without---no-sandbox)
- [💻 Colima as Docker Alternative](#-colima-as-docker-alternative)

### Platform-Specific Issues
- [⚠️ Port Conflict: Cannot Run CodeAssist + RL Swarm](#️-port-conflict-cannot-run-codeassist--rl-swarm)
- [🌐 Running on VPS: Login/Submit Button Not Working](#-running-on-vps-loginsubmit-button-not-working)
- [🪟 Windows (WSL2) Specific Issues](#-windows-wsl2-specific-issues)
- [🍎 macOS Specific Issues](#-macos-specific-issues)

### Authentication Issues
- [📧 Not Receiving Email OTP](#-not-receiving-email-otp)
- [🔑 How to Change Hugging Face Token](#-how-to-change-hugging-face-token)
- [📤 Model Upload/Push Failures](#-model-uploadpush-failures)

### Training Issues
- [🎓 "No episode snapshots were found"](#-no-episode-snapshots-were-found)
- [⚠️ Permission Denied Errors](#️-permission-denied-errors)
- [🤔 Model Not Improving After Training](#-model-not-improving-after-training)
- [⏱️ Training Taking Too Long](#️-training-taking-too-long)

### Usage Questions
- [❓ What Is CodeAssist and How Does It Work?](#-what-is-codeassist-and-how-does-it-work)
- [💻 System Requirements](#-system-requirements)
- [🎯 Understanding No-Ops and Rewards](#-understanding-no-ops-and-rewards)
- [⚙️ How to Update CodeAssist](#️-how-to-update-codeassist)
- [📊 Getting Logs for Support](#-getting-logs-for-support)

### Advanced Topics
- [🔄 Stopping and Restarting CodeAssist](#-stopping-and-restarting-codeassist)
- [🗑️ Complete Uninstall/Reset](#️-complete-uninstallreset)
- [🐛 Generic "AN ERROR HAS OCCURRED"](#-generic-an-error-has-occurred)

---

## 🆘 Most Common Issues (Start Here!)

If you're experiencing problems, **90% of issues fall into these categories:**

| Issue | Quick Fix | Full Guide |
|-------|-----------|------------|
| `uv: command not found` | Run `curl -LsSf https://astral.sh/uv/install.sh \| sh` then `source ~/.bashrc` |
| Docker not running | Open Docker Desktop app 
| Port 3000 conflict | Stop RL Swarm: `docker stop $(docker ps -aq)` |
| VPS login broken | Use SSH port forwarding for ports 3000, 8000, 8001, 8008 |
| Installation frozen | Wait 20-30 min (first run downloads large images) |

---

## ⚡ Quick Troubleshooting Checklist

Before diving into specific issues, verify these basics:


### 1. Check if Docker is running
```bash
docker ps
```

### 2. Check if UV is installed
```bash
uv --version
```

### 3. Check Python version (need 3.10+)

```bash
python3 --version
```

### 4. Verify you're in the correct directory

```bash
ls -la  # Should see run.py, compose.yml, etc.
```


### 5. Check for port conflicts

```bash
lsof -i :3000  # Should be empty if nothing running
```

> **If all checks pass and you still have issues:** Collect logs and check the specific error section below.

---

## 📦 Installation Issues

### ❌ "command not found: uv"

**Frequency:** Very High (10+ reports)

#### Problem
Running `uv run run.py` returns:
```bash
bash: uv: command not found
# or
zsh: command not found: uv
```

#### Why This Happens
The `uv` package manager isn't installed, or your shell hasn't refreshed to recognize it after installation.

#### Solution

**For Linux/WSL2:**
```bash
# Install UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# CRITICAL: Refresh your shell
source ~/.bashrc
# or if using zsh:
source ~/.zshrc

# Verify installation
uv --version
# Should output: uv 0.9.7 (or similar)
```

**For macOS:**
```bash
# Using Homebrew
brew install uv

# Verify
uv --version
```

**For Windows (WSL2):**
```bash
# Run the Linux commands INSIDE your WSL2 terminal
curl -LsSf https://astral.sh/uv/install.sh | sh
source ~/.bashrc
```

#### Still Not Working?

1. **Check if UV binary is in PATH:**
   ```bash
   which uv
   # Should show: /home/username/.local/bin/uv
   ```

2. **If empty, manually add to PATH:**
   ```bash
   echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Try reopening your terminal completely**

---

### ❌ "command not found: docker"

**Frequency:** High (7+ reports)

#### Problem
Running `uv run run.py` fails with:
```bash
bash: docker: command not found
# or
Error connecting to Docker daemon
Please ensure Docker is installed and running
```

#### Why This Happens
Either Docker isn't installed, or Docker Desktop isn't running in the background.

#### Solution

**Step 1: Install Docker**

- **Windows/Mac:** Download [Docker Desktop](https://docs.docker.com/desktop/)
- **Linux:** 
  ```bash
  # Install Docker Engine
  curl -fsSL https://get.docker.com -o get-docker.sh
  sudo sh get-docker.sh
  
  # Add your user to docker group (avoid sudo)
  sudo usermod -aG docker $USER
  
  # Log out and back in, then verify
  docker ps
  ```

**Step 2: Start Docker**

- **Windows/Mac:** Open Docker Desktop app from Start Menu/Applications
- **Linux:** 
  ```bash
  sudo systemctl enable --now docker
  sudo systemctl status docker
  # Should show "active (running)"
  ```

**Step 3: Verify Docker is Running**
```bash
docker ps
# Should show table headers (CONTAINER ID, IMAGE, etc.)
# NOT an error message
```

#### Platform-Specific Notes

**WSL2 Users:**
- Docker Desktop must have WSL2 integration enabled
- Open Docker Desktop → Settings → Resources → WSL Integration
- Enable your WSL2 distribution

**Linux VPS Users:**
- Ensure Docker service starts on boot: `sudo systemctl enable docker`
- Check firewall isn't blocking Docker

---

### ⏳ Installation Stuck on "Setting up container"

**Frequency:** High (6+ reports)

#### Problem
Terminal shows:
```
[5/6] RUN /install.sh
```
And appears frozen for 20+ minutes. Users report "stuck for hours."

#### Why This Happens
**This is NORMAL!** The first run downloads:
- Large Docker images (multiple GB)
- AI model dependencies
- Build dependencies

On slow connections, this can take 30+ minutes.

#### Solution

**DO THIS:**
1. **Wait patiently** - Check if you see:
   - Disk activity (LED blinking)
   - Network activity (download speeds in Docker Desktop)
   - CPU usage (Task Manager/Activity Monitor)

2. **Expected times:**
   - Fast connection (100+ Mbps): 10-20 minutes
   - Average connection (25-50 Mbps): 20-40 minutes
   - Slow connection (<10 Mbps): 1+ hours

**ONLY interrupt if:**
- No disk/network activity for 2+ hours
- Terminal shows error messages
- Docker Desktop shows "failed" status

**If you need to restart:**
```bash
# Stop gracefully
Ctrl+C

# Check internet connection
ping google.com

# Stop any conflicting containers
docker stop $(docker ps -aq)

# Retry
uv run run.py
```

#### Pro Tips
- Close other heavy applications during first install
- Don't run RL Swarm simultaneously
- Future runs will be MUCH faster (cached layers)

---

### 🔧 Missing Dependencies (Python/UV/Docker)

**Frequency:** Very High (31 reports)

#### Problem
Various "command not found" or "module missing" errors when starting CodeAssist.

#### Why This Happens
CodeAssist requires specific versions of core tools.

#### Solution

**Required Dependencies:**
- **Python:** 3.10 or higher
- **Docker:** Latest version
- **UV:** Latest version

**Check What You Have:**
```bash
# Check Python
python3 --version
# Need: Python 3.10.x or higher

# Check Docker
docker --version
# Need: Docker version 20.x or higher

# Check UV
uv --version
# Need: uv 0.9.x or higher
```

**Install Missing Components:**

**Python 3.10+ (if needed):**
```bash
# Linux (Ubuntu/Debian)
sudo apt update
sudo apt install python3.10 python3-pip

# macOS
brew install python@3.10

# Windows WSL2
sudo apt update && sudo apt install python3.10
```

**Docker:**
- See [Docker installation section](#-command-not-found-docker)

**UV:**
- See [UV installation section](#-command-not-found-uv)

**After Installing All Dependencies:**
```bash
# Navigate to CodeAssist directory
cd ~/codeassist  # adjust path as needed

# Run
uv run run.py
```

---

### 🐧 Linux: "Running as root without --no-sandbox"

**Frequency:** Medium (3+ reports)

#### Problem
Linux users see:
```
[ERROR:content/browser/zygote_host_impl_linux.cc:101] 
Running as root without --no-sandbox is not supported
```

#### Why This Happens
CodeAssist uses Electron for its UI, which cannot run as root user without disabling security sandboxing.

#### Solution

**Option A: Run as Non-Root User (RECOMMENDED)**
```bash
# Create a non-root user if needed
sudo adduser codeassist

# Switch to that user
su - codeassist

# Clone and run CodeAssist
cd ~
git clone <repo-url>
cd codeassist
uv run run.py
```

**Option B: Disable Sandbox (WORKAROUND)**
```bash
# Set environment variables
export CHROME_SANDBOX=false
export ELECTRON_RUN_AS_NODE=1
export ELECTRON_DISABLE_SECURITY_WARNINGS=true

# Then run
uv run run.py
```

⚠️ **Security Note:** Option B reduces security. Only use on trusted, isolated systems.

---

### 💻 Colima as Docker Alternative

**Frequency:** Low (4 reports)

#### Question
"Can I use Colima instead of Docker Desktop?"

#### Answer
**YES!** Colima works with CodeAssist.

#### Setup
```bash
# Install Colima (macOS)
brew install colima

# Start Colima
colima start

# Verify it's running
docker ps

# Run CodeAssist normally
uv run run.py
```

#### Troubleshooting
If CodeAssist fails with Colima:
1. Check Colima status: `colima status`
2. Restart Colima: `colima restart`
3. Share logs in #codeassist-support if issues persist

---

## 🖥️ Platform-Specific Issues

### ⚠️ Port Conflict: Cannot Run CodeAssist + RL Swarm

**Frequency:** Very High (15+ reports)

#### Problem
Trying to run CodeAssist while RL Swarm is running causes:
```
Bind for 0.0.0.0:3000 failed: port is already allocated
```
Web UI won't load.

#### Why This Happens
Both applications use **port 3000** for their web interface.

#### Solution A: Stop RL Swarm (EASIEST)

```bash
# Stop all Docker containers
docker stop $(docker ps -aq)

# Verify nothing on port 3000
lsof -i :3000
# Should return empty

# Start CodeAssist
uv run run.py

# Open http://localhost:3000
```

#### Solution B: Change CodeAssist Port (ADVANCED)

If you need both running simultaneously:

```bash
# 1. Navigate to CodeAssist directory
cd ~/codeassist

# 2. Edit compose.yml
nano compose.yml

# 3. Find this line under 'web-ui':
    ports:
      - "3000:3000"

# 4. Change to:
    ports:
      - "3001:3000"

# 5. Save (Ctrl+X, Y, Enter)

# 6. Edit run.py
nano run.py

# 7. Find all instances of '3000' and replace with '3001'

# 8. Save and run
uv run run.py

# 9. Open http://localhost:3001
```

**If using SSH tunnel, update your tunnel command:**
```bash
ssh -N -L 3001:localhost:3001 username@server
```

---

### 🌐 Running on VPS: Login/Submit Button Not Working

**Frequency:** High (10+ reports)

#### Problem
When accessing CodeAssist on a VPS through ngrok/cloudflared tunnel:
- "Submit Solution" button does nothing
- Login modal keeps reappearing
- Cannot authenticate

#### Why This Happens
CodeAssist uses **4 separate backend services** on different ports:
- `3000` - Web UI
- `8000` - API service
- `8001` - State service
- `8008` - Solution tester

Single-port tunnels (ngrok, cloudflared) only expose port 3000, breaking backend communication.

#### Solution: SSH Port Forwarding (REQUIRED for VPS)

**Step 1: Set Up Tunnels**
```bash
# Open 4 separate terminal windows/tabs and run:

# Terminal 1
ssh -N -L 3000:localhost:3000 username@your_vps_ip

# Terminal 2
ssh -N -L 8000:localhost:8000 username@your_vps_ip

# Terminal 3
ssh -N -L 8001:localhost:8001 username@your_vps_ip

# Terminal 4
ssh -N -L 8008:localhost:8008 username@your_vps_ip
```

**Step 2: Access Locally**
Open `http://localhost:3000` in your browser. Login and submit should now work.

#### Alternative: Use Screen/Tmux for Persistent Tunnels

```bash
# On your LOCAL machine
ssh username@your_vps_ip

# Create a screen session
screen -S port-forwards

# Run all forwards in background
ssh -N -L 3000:localhost:3000 username@localhost &
ssh -N -L 8000:localhost:8000 username@localhost &
ssh -N -L 8001:localhost:8001 username@localhost &
ssh -N -L 8008:localhost:8008 username@localhost &

# Detach: Ctrl+A, then D
```

#### Why NOT to Use ngrok/cloudflared
❌ Only exposes one port  
❌ Backend APIs remain inaccessible  
❌ CORS and authentication break  

✅ SSH forwarding works reliably  
✅ No external service dependencies  
✅ Secure and private  

---

### 🪟 Windows (WSL2) Specific Issues

#### Common WSL2 Problems

**Docker Integration Not Working:**
1. Open Docker Desktop
2. Settings → Resources → WSL Integration
3. Enable integration for your WSL2 distro (Ubuntu, Debian, etc.)
4. Click "Apply & Restart"
5. Inside WSL2 terminal: `docker ps` should work

**Path Issues:**
```bash
# Make sure you're IN WSL2, not PowerShell
# Your prompt should show:
username@hostname:~$

# NOT:
PS C:\Users\...>
```

**Slow Performance:**
- Keep project files INSIDE WSL2 filesystem (`~/codeassist`)
- NOT in `/mnt/c/...` (Windows drives are slow)

**Networking Issues:**
```bash
# If localhost:3000 doesn't work in Windows browser
# Find WSL2 IP address:
ip addr show eth0 | grep inet

# Use that IP: http://<WSL2_IP>:3000
```

---

### 🍎 macOS Specific Issues

#### Git Pull Errors on macOS

Some macOS users report errors after `git pull`. If this happens:

```bash
# Discard local changes and force update
cd ~/codeassist
git reset --hard origin/main
git pull
```

#### Homebrew Permissions

If UV or Docker installation via Homebrew fails:
```bash
# Fix Homebrew permissions
sudo chown -R $(whoami) /usr/local/Cellar

# Retry installation
brew install uv
```

#### M1/M2 (Apple Silicon) Notes

CodeAssist works on Apple Silicon. If you encounter:
- Slow Docker builds → Normal on first run
- Architecture warnings → Ignore if app runs fine

---

## 🔐 Authentication Issues

### 📧 Not Receiving Email OTP

**Frequency:** High (7+ reports)

#### Problem
Clicked "Login with Email" but the one-time passcode never arrives in inbox.

#### Solutions

**If on VPS:**
- Ensure all 4 ports are forwarded (see [VPS section](#-running-on-vps-loginsubmit-button-not-working))
- OTP emails fail without proper port forwarding

**Try Google Login Instead:**
- Click "Login with Google" on the CodeAssist UI
- More reliable than email login currently

**Update CodeAssist:**
```bash
cd ~/codeassist
git pull
uv run run.py
# Retry email login
```

**Check Spam Folder:**
- Search for emails from `@gensyn.ai` or `noreply`

**Still Not Working?**
Report in #codeassist-support with:
- Your email provider (Gmail, Outlook, etc.)
- Whether Google login works
- Screenshot of login screen

---

### 🔑 How to Change Hugging Face Token

**Frequency:** Medium (3+ reports)

#### Problem
Entered wrong Hugging Face token and want to update it.

#### Solution

```bash
# 1. Navigate to CodeAssist directory
cd ~/codeassist

# 2. Delete the .env file (stores old token)
rm .env

# 3. Restart CodeAssist
uv run run.py

# 4. When prompted, enter your NEW Hugging Face token
```

#### Creating a New Token

1. Go to https://huggingface.co/settings/tokens
2. Click "New token"
3. Name it "CodeAssist"
4. Set Type to **"Write"** (CRITICAL - read-only won't work)
5. Copy the token
6. Paste into CodeAssist when prompted

---

### 📤 Model Upload/Push Failures

**Frequency:** High (14 reports)

#### Problem
Training completes but model upload to Hugging Face fails with authentication or permission errors.

#### Solutions

**Step 1: Verify Token Type**
- Token MUST be **"Write"** type, not "Read"
- Go to https://huggingface.co/settings/tokens
- Check your token's type
- Create new Write token if needed

**Step 2: Update Token in CodeAssist**
```bash
cd ~/codeassist
rm .env
uv run run.py
# Enter your WRITE token when prompted
```

**Step 3: Check Disk Space**
```bash
# Ensure enough space for model upload
df -h
# Need at least 5GB free
```

**Step 4: Manual Upload (if automated fails)**
```bash
# Find your trained model
ls -lh persistent-data/models/

# Upload manually using Hugging Face CLI
pip install huggingface-hub
huggingface-cli upload <your-username>/<model-name> persistent-data/models/<model-dir>
```

---

## 🎓 Training Issues

### 🎓 "No episode snapshots were found"

**Frequency:** High (8+ reports)

#### Problem
After pressing `Ctrl+C` to start training:
```
No processed episodes found.
Training cannot proceed.
```

#### Why This Happens
- Bug in older versions of CodeAssist
- Incorrect file permissions on `persistent-data/` folder

#### Solution

```bash
# 1. Stop CodeAssist
Ctrl+C

# 2. Update to latest version
cd ~/codeassist
git pull

# 3. Fix permissions
sudo chown -R $USER:$USER persistent-data

# 4. Restart
uv run run.py

# 5. Code some problems, then Ctrl+C to train
# Should now work correctly
```

#### If Still Failing

Check that episodes are being recorded:
```bash
ls -lh persistent-data/episodes/
# Should see .json files after coding problems
```

If folder is empty:
1. Verify you're actually coding problems (not just browsing UI)
2. Check you're accepting/rejecting assistant suggestions
3. Restart CodeAssist and try again

---

### ⚠️ Permission Denied Errors

**Frequency:** Medium

#### Problem
```
PermissionError: [Errno 13] Permission denied: '/app/persistent-data/...'
```

#### Solution

```bash
# Fix ownership of persistent-data folder
cd ~/codeassist
sudo chown -R $USER:$USER persistent-data

# If using Docker on Linux, ensure user is in docker group
sudo usermod -aG docker $USER

# Log out and back in for group changes to take effect
```

---

### 🤔 Model Not Improving After Training

**Frequency:** Medium

#### Problem
Trained the model but it seems worse or unchanged.

#### Why This Happens
**This is EXPECTED!** According to official docs:
- Models often get worse before getting better
- Needs 50-100+ training episodes for noticeable improvement
- Early training can cause temporary regression

#### What to Do

**Keep Training:**
1. Code 20-30 more problems
2. Train again (Ctrl+C)
3. Repeat 3-5 times
4. Improvement becomes noticeable after ~100 episodes

**Focus on Quality Feedback:**
- Accept good suggestions
- Edit imperfect suggestions (don't just reject)
- Reject terrible suggestions
- Each interaction teaches the model

**Track Progress:**
- Note baseline: "How many problems did assistant solve initially?"
- After each training: "Did it get better at similar problems?"

---

### ⏱️ Training Taking Too Long

**Frequency:** Low

#### Problem
Training process runs for hours without completing.

#### Solutions

**Check System Resources:**
```bash
# Monitor CPU/RAM usage during training
top
# or
htop

# Training is CPU-intensive; high usage is normal
```

**Reduce Training Data (if desperate):**
```bash
# Move some episodes to backup folder
mkdir persistent-data/episodes-backup
mv persistent-data/episodes/*.json persistent-data/episodes-backup/
# Keep only last 20-30 episodes in main folder
```

**Expected Training Times:**
- 10-20 episodes: 5-15 minutes
- 50 episodes: 30-60 minutes
- 100+ episodes: 1-2+ hours

Times vary by:
- CPU speed
- RAM available
- Number of episodes
- Episode complexity

**Let It Run:**
- Training should NOT take more than 2-3 hours
- If exceeding that, report in #codeassist-support

---

## ❓ Usage Questions

### ❓ What Is CodeAssist and How Does It Work?

**Category:** General Understanding

#### What It Is
CodeAssist is a **private, local AI coding assistant** that learns from YOUR coding style and preferences.

#### How It Works

1. **You Code:** Solve LeetCode-style problems in the CodeAssist editor
2. **Assistant Helps:** AI suggests code completions
3. **You Teach:** 
   - ✅ Accept good suggestions
   - ✏️ Edit imperfect suggestions
   - ❌ Reject bad suggestions
4. **Model Learns:** Each interaction becomes training data
5. **You Train:** Press `Ctrl+C` to start training on your feedback
6. **Model Improves:** Your personal assistant gets better at YOUR style

#### Key Features
- 🔒 **Private:** Runs locally, your code never leaves your machine
- 🎓 **Personalized:** Learns your coding patterns
- 🏆 **Rewarded:** Earn participation points for training
- 🔧 **Open:** Fully local, no cloud API required

---

### 💻 System Requirements

**Frequency:** High (20+ reports)

#### Minimum Requirements
- **RAM:** 12 GB
- **CPU:** Modern multi-core processor (Intel/AMD/Apple Silicon)
- **Storage:** 10 GB free space
- **GPU:** Not required
- **Internet:** Required for initial setup and model uploads

#### Recommended Requirements
- **RAM:** 32 GB
- **CPU:** 6+ cores
- **Storage:** 20 GB free (for multiple training sessions)
- **Connection:** 25+ Mbps (for faster downloads)

#### Performance Notes

**8 GB RAM:**
- May work but will be slow
- Expect frequent swapping
- Not recommended

**16 GB RAM:**
- Usable for basic training
- Keep other apps closed

**32 GB+ RAM:**
- Smooth experience
- Can multitask while training

---

### 🎯 Understanding No-Ops and Rewards

**Frequency:** High

#### What Are No-Ops?
"No-Op" means "No Operation" - when the assistant chooses to do nothing instead of making a suggestion.

**This is GOOD behavior!** It means:
- Assistant isn't confident enough to suggest code
- Better to wait than write bad code
- You should code that part manually

#### How Rewards Work

**Participation Points are earned for:**
- Training your model
- Uploading trained models to Hugging Face
- Quality of training data (quantity + diversity)

**What Affects Rewards:**
- Number of training episodes
- Variety of problems solved
- Quality of feedback (accepting/editing/rejecting)

**Note:** Problem difficulty does NOT affect rewards (unconfirmed; team clarification pending)

#### Best Practices for Rewards
1. Solve diverse problems (different topics/difficulty)
2. Provide clear feedback (don't ignore suggestions)
3. Train regularly (every 20-30 problems)
4. Upload models successfully

---

### ⚙️ How to Update CodeAssist

**Frequency:** Medium

#### When to Update
- New features announced
- Bug fixes released
- Experiencing issues resolved in latest version

#### Update Process

```bash
# 1. Stop CodeAssist if running
Ctrl+C

# 2. Navigate to directory
cd ~/codeassist

# 3. Pull latest changes
git pull

# 4. Restart
uv run run.py
```

#### If Git Pull Fails

```bash
# Discard local changes and force update
git reset --hard origin/main
git pull

# CAREFUL: This deletes any custom modifications you made
```

#### Check Current Version
```bash
cd ~/codeassist
git log -1 --oneline
# Shows latest commit
```

---

### 📊 Getting Logs for Support

**Frequency:** High

#### When You Need Logs
- Reporting bugs in #codeassist-support
- Experiencing crashes
- Training failures
- Model upload issues

#### How to Collect Logs

**Method 1: Docker Container Logs**
```bash
# Get logs from all CodeAssist containers
docker logs codeassist-state-service > state-service.log
docker logs codeassist-solution-tester > solution-tester.log
docker logs codeassist-web-ui > web-ui.log

# View them
cat state-service.log
```

**Method 2: CodeAssist Runtime Logs**
```bash
# Logs are also stored in:
cd ~/codeassist
ls -lh logs/

# Recent logs
tail -n 100 logs/latest.log
```

**Method 3: Full System Info**
```bash
# Collect everything for support
cd ~/codeassist

# Create support bundle
{
  echo "=== System Info ==="
  uname -a
  docker --version
  python3 --version
  uv --version
  
  echo -e "\n=== Docker Containers ==="
  docker ps -a
  
  echo -e "\n=== CodeAssist Logs ==="
  docker logs codeassist-state-service 2>&1 | tail -n 50
  
} > support-info.txt

# Upload support-info.txt to Discord
```

---

## 🔧 Advanced Topics

### 🔄 Stopping and Restarting CodeAssist

#### Graceful Stop
```bash
# In the terminal running CodeAssist
Ctrl+C

# Wait for containers to stop gracefully
# You'll see "Shutting down..." messages
```

#### Force Stop
```bash
# If Ctrl+C doesn't work
docker stop $(docker ps -aq --filter "name=codeassist")
```

#### Full Restart
```bash
# Stop everything
docker stop $(docker ps -aq)

# Start fresh
cd ~/codeassist
uv run run.py
```

---

### 🗑️ Complete Uninstall/Reset

#### Remove All CodeAssist Data

**WARNING:** This deletes all trained models and episodes.

```bash
# Navigate to parent directory
cd ~

# Remove entire codeassist folder
rm -rf codeassist

# Remove Docker images (optional)
docker image rm $(docker images "codeassist*" -q)

# Remove Docker volumes (optional)
docker volume rm $(docker volume ls -q --filter "name=codeassist")
```

#### Fresh Install After Uninstall

```bash
cd ~
git clone <codeassist-repo-url>
cd codeassist
uv run run.py
```

---

### 🐛 Generic "AN ERROR HAS OCCURRED"

**Frequency:** Low (3 reports)

#### Problem
Vague error message:
```
AN ERROR HAS OCCURRED
Please upload your logs to #codeassist-support
```

#### Possible Causes
- Docker not running
- Duplicate CodeAssist instance
- Corrupted container state
- Port conflicts

#### Solution

**Step 1: Collect Logs**
```bash
docker logs codeassist-state-service > error-logs.txt
docker logs codeassist-solution-tester >> error-logs.txt
```

**Step 2: Check Docker Status**
```bash
docker ps -a
# Look for any containers with "Exited" status
```

**Step 3: Clean Restart**
```bash
# Stop all CodeAssist containers
docker stop $(docker ps -aq --filter "name=codeassist")

# Remove stopped containers
docker rm $(docker ps -aq --filter "name=codeassist")

# Restart CodeAssist
cd ~/codeassist
uv run run.py
```

**Step 4: Check for Duplicate Instances**
```bash
# See if CodeAssist is already running
ps aux | grep "run.py"

# Kill any duplicate processes
kill <process_id>
```

**If Still Failing:**
1. Post `error-logs.txt` in #codeassist-support
2. Include output of `docker ps -a`
3. Mention your OS and setup (local/VPS)

---

## 🔍 Known Issues & Workarounds

### Broken Test Cases on Some LeetCode Problems

**Status:** Confirmed by team

Some problems have broken test cases in CodeAssist.

**Workaround:**
- Skip problems with obviously broken tests
- Report problem numbers in Discord
- Continue with other problems

---

### macOS Git Pull Occasionally Fails

**Status:** Under investigation

Some macOS users report git errors after `git pull`.

**Workaround:**
```bash
git reset --hard origin/main
git pull --rebase
```

---

### Participation Points Calculation Unclear

**Status:** Awaiting official documentation

Community is unclear on:
- How points are calculated
- Whether problem difficulty affects rewards
- Minimum episodes needed for points

**Current Understanding:**
- Points awarded for training + uploading models
- More episodes = more points
- Quality > quantity for training data

**Team has been asked to clarify this in official docs.**

---

## 📚 Additional Resources

### Official Links
- 📖 **Official Docs:** [docs.gensyn.ai/codeassist](https://docs.gensyn.ai/codeassist)
- 🎥 **Video Walkthrough:** [Jesse's YouTube Tutorial](https://youtube.com/gensyn-codeassist)
- 💬 **Discord Support:** `#codeassist-support` channel
- 🐙 **GitHub:** [CodeAssist Repository]

### Community Resources
- This FAQ (bookmark it!)
- Discord pinned messages
- Community tips in `#codeassist-general`

### Getting Help

**Before Posting in Support:**
1. ✅ Check this FAQ first
2. ✅ Search Discord for your error message
3. ✅ Try the Quick Troubleshooting Checklist

**When Posting in Support:**
Include:
```
Problem: [Brief description]
OS: [Windows/Mac/Linux]
Setup: [Local/VPS]
What I tried: [List steps]
Logs: [Attach logs using instructions above]
Error message: [Exact error if applicable]
```

---

## 💡 Pro Tips & Best Practices

### For First-Time Users

1. **Start Small:** Code 5-10 easy problems before first training
2. **Read Prompts:** The assistant gives hints in its completions
3. **Don't Rush:** Let the assistant suggest, then decide thoughtfully
4. **Train Regularly:** Every 20-30 problems is ideal
5. **Be Patient:** Model improvement takes time (50-100+ episodes)

### For VPS Users

1. **Always use SSH port forwarding** (not ngrok/cloudflared)
2. **Keep terminals open** while using CodeAssist
3. **Use `tmux` or `screen`** for persistent sessions
4. **Monitor resources** with `htop` during training

### For Training Optimization

1. **Diverse Problems:** Mix easy, medium, hard
2. **Clear Feedback:**
   - Accept = "This is exactly right"
   - Edit = "This is close, here's the correction"
   - Reject = "This is completely wrong"
3. **Don't Skip Suggestions:** Even if wrong, rejecting teaches the model
4. **Train After Patterns Emerge:** Once you notice repeated mistakes, train to fix them

### Resource Management

**Before Training:**
```bash
# Close unnecessary apps
# Check available RAM
free -h  # Linux
vm_stat  # macOS

# Should have 8GB+ free
```

**During Training:**
- Don't interrupt unless it freezes (2+ hours no activity)
- Don't start other Docker containers
- Let the process complete

**After Training:**
- Upload to HuggingFace immediately (gets you points)
- Start next coding session with improved model

---

## 🚨 Emergency Troubleshooting

### Nothing Works - Nuclear Option

If everything is broken and you've tried all solutions:

```bash
# 1. Backup your models (optional)
cp -r ~/codeassist/persistent-data/models ~/codeassist-backup

# 2. Stop everything
docker stop $(docker ps -aq)
docker rm $(docker ps -aq)

# 3. Remove CodeAssist
cd ~
rm -rf codeassist

# 4. Clean Docker
docker system prune -af --volumes

# 5. Fresh install
git clone <repo-url>
cd codeassist
uv run run.py

# 6. Restore models if you backed them up
cp -r ~/codeassist-backup/models ~/codeassist/persistent-data/
```

### Quick Diagnostic Commands

```bash
# System health check
{
  echo "Docker status:"
  docker ps
  
  echo -e "\nPort 3000 usage:"
  lsof -i :3000
  
  echo -e "\nDisk space:"
  df -h
  
  echo -e "\nRAM usage:"
  free -h
  
  echo -e "\nCodeAssist containers:"
  docker ps -a --filter "name=codeassist"
  
} | tee health-check.txt
```

---

## 🎯 Common Mistakes to Avoid

| ❌ DON'T | ✅ DO |
|----------|-------|
| Run CodeAssist and RL Swarm simultaneously (port conflict) | Keep Docker Desktop running in background |
| Use ngrok/cloudflared for VPS (breaks authentication) | Use SSH port forwarding for VPS setups |
| Interrupt first installation before it completes | Wait patiently during first installation |
| Run as root on Linux (security risk) | Run as non-root user on Linux |
| Forget to `source ~/.bashrc` after installing UV | Refresh shell after installing dependencies |
| Train after only 2-3 problems (not enough data) | Code 20-30 problems before training |
| Expect immediate model improvement (takes 50+ episodes) | Be patient with model learning curve |
| | Update CodeAssist regularly (`git pull`) |
| | Provide clear feedback (accept/edit/reject) |
| | Upload models to HuggingFace after training |

---

*This FAQ is community-maintained and not officially affiliated with Gensyn. For official documentation, visit docs.gensyn.ai*

*Have suggestions? Find an error? Have a better solution? **Submit a Pull Request** - let's make this the definitive CodeAssist troubleshooting resource together.*
