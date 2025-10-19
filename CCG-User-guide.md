# Complete Guide: Installing and Using molino Package on JupyterHub Server

This comprehensive guide covers every step from initial setup to using molino in JupyterHub, including image selection and server management.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Install Docker/Podman](#step-1-install-dockerpodman)
3. [Step 2: Prepare Working Directory](#step-2-prepare-working-directory)
4. [Step 3: Clean Up Previous Images](#step-3-clean-up-previous-images)
5. [Step 4: Create Dockerfile](#step-4-create-dockerfile)
6. [Step 5: Build the Image](#step-5-build-the-image)
7. [Step 6: Verify Built Image](#step-6-verify-built-image)
8. [Step 7: Connect to VPN](#step-7-connect-to-vpn)
9. [Step 8: Tag the Image](#step-8-tag-the-image)
10. [Step 9: Push to Registry](#step-9-push-to-registry)
11. [Step 10: Access JupyterHub](#step-10-access-jupyterhub)
12. [Step 11: Select Custom Image](#step-11-select-custom-image)
13. [Step 12: Start Server](#step-12-start-server)
14. [Step 13: Test molino Installation](#step-13-test-molino-installation)
15. [Step 14: Working with molino](#step-14-working-with-molino)
16. [Step 15: Stop Server](#step-15-stop-server)
17. [Troubleshooting](#troubleshooting)
18. [Advanced Tips](#advanced-tips)

---

## Prerequisites

Before starting, ensure you have:

- Personal laptop/computer running Linux, macOS, or Windows
- Docker or Podman installed on your system
- SBU university VPN credentials and configuration
- JupyterHub account (username and password)
- Stable internet connection
- At least 10GB free disk space on your laptop
- Basic command line knowledge

---

## Step 1: Install Docker/Podman

### For Ubuntu/Debian:

```bash 
# Update package index

sudo apt update

# Install Podman

sudo apt install -y podman

# Verify installation

podman --version
```

### For Fedora/RHEL:

```bash
# Install Podman

sudo dnf install -y podman

# Verify installation

podman --version
```

### For Arch Linux:
```bash
# Install Podman
sudo pacman -S podman
# Verify installation
podman --version
```

### For macOS:

1. Download Docker Desktop from: https://www.docker.com/products/docker-desktop
2. Install the .dmg file
3. Open Docker Desktop
4. Verify installation:
```bash
docker --version
```

### For Windows:

1. Download Docker Desktop from: https://www.docker.com/products/docker-desktop
2. Run the installer
3. Restart your computer
4. Open Docker Desktop
5. Verify installation in PowerShell:
```bash
docker --version
```

**Note:** The rest of this guide uses `podman` commands. If you're using Docker, simply replace `podman` with `docker` in all commands.

---

## Step 2: Prepare Working Directory

Create a dedicated directory for your Docker build files on your computer:

```bash 

# Create directory

mkdir ~/my-jupyter

# Navigate to directory

cd ~/my-jupyter

# Verify current location

pwd
```

Expected output: `/home/yourusername/my-jupyter`

---

## Step 3: Clean Up Previous Images

If this is your first time, skip this step. Otherwise, clean up old images to free space.

### View existing images:

```bash 
podman images
```

### Remove specific image by ID:

```bash 
podman rmi IMAGE_ID
```

Replace `IMAGE_ID` with the actual ID from the images list.

### Remove all unused images:

```bash 
podman image prune -a
```
Type `y` when prompted to confirm.

### Complete system cleanup:

```bash 
podman system prune -a --volumes
```
This removes:
- All stopped containers
- All unused images
- All unused volumes
- All build cache

Type `y` to confirm.

### Verify cleanup:

```bash 
podman images
podman system df
```

---

## Step 4: Create Dockerfile

Create the Dockerfile with all necessary configurations:

```bash 

# Create and edit Dockerfile

nano Dockerfile

```

Copy and paste the following complete Dockerfile content:
```bash 
# Use Python 3.11 base image from Jupyter
FROM quay.io/jupyter/minimal-notebook:python-3.11

# Switch to root for system packages
USER root

# Install system dependencies (note: no \&\&, just &&)
RUN apt-get update && \
    apt-get install -y \
        git git-lfs pre-commit \
        tmux nano vim wget curl htop \
        gfortran \
        texlive texlive-latex-extra \
        ncdu rsync \
        build-essential \
        make cmake clang \
        bzip2 tar gzip \
        podman distrobox \
        openmpi-bin openmpi-common \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Switch back to jovyan user
USER jovyan

# Set environment variables for molino
ENV HDF5_USE_FILE_LOCKING=FALSE
ENV MOLINO_DIR=/home/jovyan/DataFiles/Molino/Data

# Install Python packages in stages
RUN conda install -y numpy pandas scipy matplotlib && conda clean -afy
RUN conda install -y camb astropy h5py && conda clean -afy
RUN conda install -y emcee corner getdist seaborn && conda clean -afy
RUN conda install -y scikit-learn py-xgboost && conda clean -afy
RUN conda install -y networkx sympy joblib tqdm plotly pillow && conda clean -afy
RUN conda install -y opencv pymc scikit-image mpi4py && conda clean -afy
RUN conda install -y dask-gateway dask distributed && conda clean -afy

# Install cobaya via pip
RUN pip install --no-cache-dir cobaya

# Clone and install molino
RUN git clone https://github.com/changhoonhahn/molino.git && \
    cd molino && \
    pip install --no-cache-dir -e .

# Set working directory
WORKDIR /home/jovyan
```

**Important Notes:**

1. Adjust `MOLINO_DIR` if your data location is different:
   - Current setting: `/home/jovyan/DataFiles/Molino/Data`
   - Change this to match your actual molino data path on the server

2. If you don't need certain packages, you can comment them out by adding `#` at the start of the line

To save and exit nano:
1. Press `Ctrl + O` (save)
2. Press `Enter` (confirm filename)
3. Press `Ctrl + X` (exit)

### Verify Dockerfile creation:

```bash 
# Check file exists

ls -lh Dockerfile

# View file contents

cat Dockerfile
```

---

## Step 5: Build the Image

Now build the Docker image from your Dockerfile:

```bash
podman build -t my-molino-image .
```

Or with Docker:

```bash
docker build -t my-molino-image .
```

**What happens during build:**

- Downloads base Jupyter image (~1.6GB)
- Installs system packages
- Installs Python packages via conda
- Clones molino repository
- Installs molino in development mode

**Expected time:** 10-30 minutes depending on:
- Internet speed
- CPU performance
- Whether base images are cached

**Monitor progress:**

The build process will show each step. Watch for:
- `STEP 1/XX: FROM...` - Downloading base image
- `STEP X/XX: RUN conda install...` - Installing packages
- `STEP XX/XX: RUN git clone...` - Installing molino

**If build succeeds:**

You'll see:
```bash
Successfully tagged localhost/my-molino-image:latest
```

**If build fails:**

- Note which STEP failed
- Check error message carefully
- See Troubleshooting section below
- You can resume from cache if you fix the issue

---

## Step 6: Verify Built Image

Check that your image was created successfully:

```bash
podman images
```

Expected output:

```bash
REPOSITORY                              TAG         IMAGE ID      CREATED         SIZE
localhost/my-molino-image               latest      abc123def456  3 minutes ago   4.5 GB
quay.io/jupyter/minimal-notebook        python-3.11 xyz789ghi012  1 day ago       1.6 GB
```
### Get detailed image information:

```bash
# Inspect image

podman inspect my-molino-image

# Check image size

podman images --format "{{.Repository}}:{{.Tag}} {{.Size}}"
```

### Test image locally (optional):

```bash
# Run container locally to test

podman run -it --rm -p 8888:8888 my-molino-image

# You should see Jupyter startup messages

# Press Ctrl+C to stop
```
---

## Step 7: Connect to VPN

Before pushing to the server registry, connect to SBU VPN.

### For Linux (NetworkManager):

1. Open Network Settings
2. Click on VPN
3. Select your SBU VPN profile (pptp or l2tp)
4. Click Connect
5. Enter credentials if prompted

### For Linux (Command Line):

```bash
# Start VPN connection
nmcli connection up "SBU VPN"

# Check VPN status

nmcli connection show --active
```

### For macOS:

1. Open System Preferences
2. Go to Network
3. Select VPN
4. Click Connect

### For Windows:

1. Click Network icon in system tray
2. Select VPN connection
3. Click Connect
4. Enter credentials

### Verify VPN connection:

```bash
# Ping JupyterHub server

ping 192.168.80.49

# Should see responses like:

# 64 bytes from 192.168.80.49: icmp_seq=1 ttl=64 time=2.3 ms

# Ping by domain

ping jupyter.islabccg.ir

# Test registry connection

curl -k https://jupyter.islabccg.ir:5000/v2/
```

If ping fails:
- Check VPN connection status
- Verify VPN credentials
- Check firewall settings
- See Troubleshooting section

---

## Step 8: Tag the Image

Tag your local image for the server registry:

```bash
podman tag my-molino-image jupyter.islabccg.ir:5000/my-molino-image:latest
```

Or with Docker:

```bash
docker tag my-molino-image jupyter.islabccg.ir:5000/my-molino-image:latest
```

**Explanation:**

- `my-molino-image` - Your local image name
- `jupyter.islabccg.ir:5000` - Server registry address
- `/my-molino-image` - Image name on registry
- `:latest` - Tag (version identifier)

### Verify tagging:

```bash
podman images | grep molino
```

You should see two entries:
```bash
localhost/my-molino-image                    latest      abc123...
jupyter.islabccg.ir:5000/my-molino-image    latest      abc123...
```

Note: Both have the same IMAGE ID.

### Alternative: Use a version tag:

```bash
# Tag with specific version
podman tag my-molino-image jupyter.islabccg.ir:5000/my-molino-image:v1.0
# Tag with date
podman tag my-molino-image jupyter.islabccg.ir:5000/my-molino-image:2025-10-13
```

---

## Step 9: Push to Registry

Upload your image to the JupyterHub server registry:

```bash
podman push --tls-verify=false jupyter.islabccg.ir:5000/my-molino-image:latest
```

Or with Docker:

```bash
docker push jupyter.islabccg.ir:5000/my-molino-image:latest
```

**Why `--tls-verify=false`?**

The server registry uses HTTP (not HTTPS) in the local network, so we need to disable TLS verification.

**What happens during push:**

- Image layers are compressed
- Layers are uploaded to registry
- Progress is shown for each layer

**Expected output:**

```
Getting image source signatures
Copying blob sha256:abc123...
Copying blob sha256:def456...
...
Copying config sha256:xyz789...
Writing manifest to image destination
Storing signatures
```

**Expected time:** 5-20 minutes depending on:
- Image size (~4-5GB)
- Internet upload speed
- Network congestion

### Monitor upload progress:

Each layer shows progress:
```bash
Copying blob abc123 [=====>                ] 45.2MB/150.5MB
```

### Verify push succeeded:

```bash
# Check if image exists on registry
curl -k https://jupyter.islabccg.ir:5000/v2/my-molino-image/tags/list

# Should return:
# {"name":"my-molino-image","tags":["latest"]}
```

---

## Step 10: Access JupyterHub

Now that your image is on the server, access JupyterHub:

### Open JupyterHub:

1. Open web browser
2. Navigate to: `https://jupyter.islabccg.ir`
3. You may see SSL certificate warning - this is normal for internal servers
4. Click "Advanced" and "Proceed to site" (exact wording varies by browser)

### Login page:

You'll see the JupyterHub login screen with:
- Username field
- Password field
- Sign in button

### Enter credentials:

1. Enter your JupyterHub username
2. Enter your password
3. Click "Sign in"

**Common login issues:**

- **Invalid credentials**: Verify username and password
- **Account not found**: Contact admin to create account
- **Page won't load**: Check VPN connection
- **Connection timeout**: Verify server is running

---

## Step 11: Select Custom Image

After successful login, you'll see the profile selection page.

### Profile options explained:

**Option 1: CPU - Low Load**
- Automatically selects machine with lowest CPU load
- Uses default JupyterCCG image
- No customization
- Good for: Quick tasks, testing

**Option 2: Customizable CPU**
- ✅ **Choose this option**
- Select specific machine
- Select custom image
- Configure resources
- Good for: Custom environments, specific requirements

**Option 3: Customizable GPU**
- Similar to Option 2 but for GPU machines
- Currently only Giv machine has GPU
- Good for: Deep learning, GPU-accelerated tasks

### Select Customizable CPU:

1. Click on **"Customizable CPU"** option
2. You'll see additional configuration fields appear

---

## Step 12: Start Server

Configure and start your custom server:

### Configuration fields:

**1. Machine Selection:**

Dropdown menu showing available machines:
- `Esfandiar` - Main computational server
- `Giv` - GPU server (if you selected GPU option)
- `Rakhsh` - Additional server
- Other machines as available

**Select machine based on:**
- Current load (check with other users)
- Your computational needs
- Memory requirements

**2. Container Image:**

This is the most important field. Enter your custom image:

```bash
jupyter.islabccg.ir:5000/my-molino-image:latest
```

**Important:**
- Type exactly as shown above
- Replace `my-molino-image` with your image name if different
- Include `:latest` tag (or your specific version tag)
- Do not add `http://` or `https://`

**Examples of valid image names:**
```bash
jupyter.islabccg.ir:5000/my-molino-image:latest
jupyter.islabccg.ir:5000/my-molino-image:v1.0
jupyter.islabccg.ir:5000/username-molino:2025-10-13
```

**3. Base URL (Advanced - Optional):**

Default: `/lab/` (JupyterLab interface)

Other options:
- `/tree/` - JupyterNotebook classic interface
- `/rstudio/` - RStudio interface (if installed in image)

**Leave as default unless you know what you're doing.**

**4. Additional Options (if available):**

- Memory limit
- CPU limit
- GPU allocation (for GPU profile)

### Review configuration:

Before starting, verify:
- ✅ Machine selected: `rostam` (or your choice)
- ✅ Image: `jupyter.islabccg.ir:5000/my-molino-image:latest`
- ✅ Base URL: `/lab/` (default)

### Start the server:

1. Click the **"Start"** button
2. You'll see the server spawn progress page

### Server spawn stages:

**Stage 1: Pending**
```bash
Your server is starting up.
You will be redirected automatically when it's ready for you.
```

**Stage 2: Event log shows:**

Click "Event log" button to see detailed progress:

```bash
[I timestamp] Starting server...
[I timestamp] Selecting node: rostam
[I timestamp] Using image: jupyter.islabccg.ir:5000/my-molino-image:latest
[I timestamp] Creating persistent volume claim...
[I timestamp] PVC bound successfully
[I timestamp] Creating pod...
[I timestamp] Pod scheduled
[I timestamp] Pulling image...
[I timestamp] Image pulled successfully
[I timestamp] Starting container...
[I timestamp] Container started
[I timestamp] Server ready at /user/yourname/lab
```

**Expected wait time:**

- **First time:** 3-5 minutes (image must be pulled from registry to machine)
- **Subsequent times:** 30-90 seconds (image already cached)

**What's happening:**

1. **Node selection**: Kubernetes assigns your server to chosen machine
2. **PVC creation**: Creates/attaches your persistent home directory
3. **Image pull**: Downloads your custom image (first time only)
4. **Container start**: Launches container from image
5. **Jupyter start**: Starts JupyterLab server
6. **Health check**: Verifies server is responding

### Successful start:

You'll be automatically redirected to JupyterLab interface.

### If server doesn't start:

Check Event log for errors:
- **Image pull failed**: Check image name spelling
- **Node overloaded**: Try different machine or wait
- **PVC creation timeout**: Contact admin
- **Permission denied**: Contact admin

See Troubleshooting section for detailed solutions.

---

## Step 13: Test molino Installation

Once JupyterLab loads, verify molino is installed correctly.

### JupyterLab interface overview:

You'll see:
- **Left sidebar**: File browser, running kernels, extensions
- **Main area**: Launcher with options to create notebooks, terminals, etc.
- **Top menu**: File, Edit, View, Run, etc.

### Method 1: Test via Terminal

**Open terminal:**

1. In Launcher, click "Terminal" icon
   - Or: File menu → New → Terminal
2. Terminal opens in main area

**Run test commands:**

```bash
# Test Python and molino import
python -c "import molino; print('✅ molino successfully imported!')"

# Expected output:
# ✅ molino successfully imported!

# Check molino location
python -c "import molino; print(f'molino path: {molino.__file__}')"

# Expected output:
# molino path: /home/jovyan/molino/molino/__init__.py

# Verify environment variables
echo "HDF5_USE_FILE_LOCKING: \$HDF5_USE_FILE_LOCKING"
echo "MOLINO_DIR: \$MOLINO_DIR"

# Expected output:
# HDF5_USE_FILE_LOCKING: FALSE
# MOLINO_DIR: /home/jovyan/DataFiles/Molino/Data

# Check Python version
python --version
# Check installed packages
conda list | grep -E "numpy|pandas|astropy|h5py"
pip list | grep -E "molino|cobaya"
```

### Method 2: Test via Notebook

**Create new notebook:**

1. In Launcher, click "Python 3 (ipykernel)" under Notebook section
   - Or: File menu → New → Notebook
2. New notebook opens with empty cell

**Run test code:**

In the first cell, copy and paste:

```bash
# Cell 1: Import test
import molino
import numpy as np
import pandas as pd
import astropy
import h5py
import os

print("=" * 50)
print("✅ All imports successful!")
print("=" * 50)
```

Press `Shift + Enter` to run the cell.

**Expected output:**
```bash
==================================================
✅ All imports successful!
==================================================
```

**In second cell:**

```bash
# Cell 2: Version check
print(f"Python version: {import sys; sys.version}")
print(f"NumPy version: {np.__version__}")
print(f"Pandas version: {pd.__version__}")
print(f"Astropy version: {astropy.__version__}")
print(f"h5py version: {h5py.__version__}")
```

**In third cell:**

```bash
# Cell 3: Environment variables
print(f"HDF5_USE_FILE_LOCKING: {os.getenv('HDF5_USE_FILE_LOCKING')}")
print(f"MOLINO_DIR: {os.getenv('MOLINO_DIR')}")
print(f"HOME: {os.getenv('HOME')}")
print(f"USER: {os.getenv('USER')}")
```

**Expected output:**
```bash
HDF5_USE_FILE_LOCKING: FALSE
MOLINO_DIR: /home/jovyan/DataFiles/Molino/Data
HOME: /home/jovyan
USER: jovyan
```

**In fourth cell:**
```bash
# Cell 4: Check molino data directory
import os

molino_dir = os.getenv('MOLINO_DIR')
print(f"Molino data directory: {molino_dir}")
print(f"Directory exists: {os.path.exists(molino_dir)}")

if os.path.exists(molino_dir):
contents = os.listdir(molino_dir)
print(f"Contents: {contents[:5]}...")  \# Show first 5 items
else:
print("⚠️ Warning: Molino data directory not found!")
print("Make sure data is uploaded to the correct location.")
```

---

## Step 14: Working with molino

Now you can use molino for your research.

### Basic molino usage:

**Load molino data:**
```bash
# Import molino

import molino

# Example: Load galaxy catalog

# (Adjust according to molino documentation)

catalog = molino.load_catalog()

print(f"Catalog shape: {catalog.shape}")
print(f"Columns: {catalog.columns}")
```

### Working with notebooks:

**Best practices:**

1. **Save frequently**: Auto-save is enabled but save manually often
2. **Use meaningful names**: Rename "Untitled.ipynb" to something descriptive
3. **Add markdown cells**: Document your analysis
4. **Checkpoint regularly**: File → Save and Checkpoint

**Rename notebook:**

1. Right-click notebook tab
2. Select "Rename Notebook"
3. Enter new name (e.g., "molino_analysis_2025-10-13.ipynb")

**Create folder structure:**

In file browser (left sidebar):
1. Click "New Folder" icon
2. Name it (e.g., "molino_projects")
3. Drag notebooks into folder

### For long-running computations:

**Use terminal instead of notebooks for better reliability:**

1. Export notebook to Python script:
   - File → Save and Export Notebook As → Executable Script
   - Saves as `.py` file

2. Run in terminal:
```bash
cd /home/jovyan/your_project_folder
python your_analysis_script.py > output.log 2>\&1 \&
```

This runs in background and saves output to log file.

3. Monitor progress:
```bash
# View log in real-time
tail -f output.log

# Check if process is running
ps aux | grep python
```

### Access shared data:

**Connect to Samba share:**

From terminal:
```bash
# Mount samba share (if not automatically mounted)

# Usually already accessible via /home/jovyan/shared or similar

# List shared data
ls -lh /home/jovyan/DataFiles/

# Access molino data
ls -lh \$MOLINO_DIR
```

**From file browser:**

Shared folders should appear in `/home/jovyan/` directory.

---

## Step 15: Stop Server

**Important:** Always stop your server when finished to free resources for others.

### Method 1: Via JupyterLab menu

1. Click **File** in top menu
2. Click **Hub Control Panel**
3. New tab opens showing control panel
4. Click red **"Stop My Server"** button
5. Wait for confirmation message
6. Server is now stopped

### Method 2: Via direct URL

1. Navigate to: `https://jupyter.islabccg.ir/hub/home`
2. Click **"Stop My Server"**

### Verify server stopped:

- Page shows "Start My Server" button
- No longer showing "Stop My Server"

### What happens when you stop:

- Container is terminated
- Resources (CPU, RAM) are freed
- **Your files are preserved** (in /home/jovyan)
- Next start will be faster (image cached)

### What is NOT lost:

- All files in `/home/jovyan/`
- Notebooks and scripts
- Data you uploaded
- Installed conda environments in home

### What IS lost:

- Running processes
- Unsaved notebook outputs
- System packages (container is recreated)
- Anything outside `/home/jovyan/`

---

## Troubleshooting

### Build Issues

**Problem: Build fails at conda install step**

```bash 
Error: Solving environment failed
```

Solution:
1. Check which package caused conflict
2. Remove that package line from Dockerfile
3. Rebuild
4. Install problematic package later via pip

**Problem: Out of disk space during build**

```bash
Error: no space left on device
```

Solution:
```bash
# Clean up Docker/Podman
podman system prune -a --volumes

# Remove unused images
podman rmi \$(podman images -q)

# Check available space
df -h
```

**Problem: Network timeout during build**

```bash
Error: Failed to download package
```

Solution:
- Check internet connection
- Retry build (may be temporary issue)
- Use VPN if needed for package sources

### Push Issues

**Problem: Cannot push to registry**

```bash
Error: server gave HTTP response to HTTPS client
```

Solution:
```bash
# Use --tls-verify=false flag
podman push --tls-verify=false jupyter.islabccg.ir:5000/my-molino-image:latest
```

**Problem: VPN connection issues**

```bash
Error: unable to resolve host jupyter.islabccg.ir
```

Solution:
1. Verify VPN is connected
2. Test connectivity:
```bash
ping 192.168.80.49
ping jupyter.islabccg.ir
```
3. Reconnect VPN if needed
4. Check VPN configuration

**Problem: Authentication required**

```bash 
Error: authentication required
```

Solution:
- Contact admin to enable push access
- Verify you're using correct registry address

### Server Start Issues

**Problem: Server stuck on "pending"**

Event log shows: `Waiting for node selection...`

Solution:
- Machine may be overloaded
- Wait 5 minutes
- If still stuck, stop server and try different machine

**Problem: Image pull failed**

Event log shows: `Failed to pull image`

Solution:
1. Verify image name is correct:
```bash
jupyter.islabccg.ir:5000/my-molino-image:latest
```
2. Check image exists in registry:
```bash
curl -k https://jupyter.islabccg.ir:5000/v2/my-molino-image/tags/list
```
3. Contact admin if image not found

**Problem: Pod failed to start**

Event log shows: `Pod failed` or `CrashLoopBackOff`

Solution:
- Image may have errors
- Check Dockerfile for issues
- Rebuild and repush image
- Contact admin for pod logs

**Problem: PVC creation timeout**

Event log shows: `Waiting for PVC...` for >10 minutes

Solution:
- NAS may have issues
- Contact admin
- May need to delete and recreate PVC

### Runtime Issues

**Problem: Kernel dies when importing molino**

Error: `Kernel died, restarting...`

Solution:
- May be memory issue
- Check molino data path is correct
- Try in terminal instead:
```bash
python -c "import molino"
```

**Problem: Cannot find molino module**

Error: `ModuleNotFoundError: No module named 'molino'`

Solution:
1. Wrong image selected - verify in Hub Control Panel
2. Build failed - check build logs
3. Reinstall in running container:
```bash
cd /home/jovyan
git clone https://github.com/changhoonhahn/molino.git
cd molino
pip install -e .
```
**Problem: HDF5 locking errors**

Error: `Unable to lock file, errno = 11`

Solution:
- Check environment variable:
```bash
echo \$HDF5_USE_FILE_LOCKING
```
- Should be `FALSE`
- If not set, add to Dockerfile and rebuild

**Problem: Molino data not found**

Error: `FileNotFoundError: Molino data directory not found`

Solution:
1. Check MOLINO_DIR path:
```bash
echo \$MOLINO_DIR
```
2. Verify data exists:
```bash
ls -lh \$MOLINO_DIR
```
3. Update MOLINO_DIR in Dockerfile if path is wrong
4. Contact admin to verify data is uploaded

---

## Advanced Tips

### Custom conda environments

Create isolated environments for different projects:

```bash
# Create new environment
conda create -p ~/my_project_env python=3.11

# Activate environment
conda activate ~/my_project_env

# Install packages in this environment
conda install numpy pandas

# Add environment as Jupyter kernel
python -m ipykernel install --user --name my_project --display-name "Python (My Project)"
```

Now you can select "Python (My Project)" kernel in notebooks.

### Install additional packages

**Via conda (in home directory environment):**

```bash
conda create -p ~/myenv python=3.11
conda activate ~/myenv
conda install package_name
```

**Via pip (user installation):**

```bash
pip install --user package_name
```

### Version control with Git

**Clone repository:**

```bash
cd /home/jovyan
git clone https://github.com/username/repository.git
cd repository
```

**Configure Git:**

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

**Commit changes:**

```bash
git add .
git commit -m "Update analysis"
git push origin main
```

### Data management

**Check disk usage:**

```bash
# Check home directory size
du -sh /home/jovyan

# Find large files
du -ah /home/jovyan | sort -rh | head -20

# Clean conda cache
conda clean -a

# Clean pip cache
pip cache purge
```

**Transfer large files:**

Use terminal with rsync or scp:

```bash
# Copy from external server
rsync -avz user@server:/path/to/data /home/jovyan/data/

# Upload to share
cp large_file.dat /home/jovyan/shared/
```

### Monitoring resources

**Check memory usage:**

```bash
# Current memory usage
free -h

# Top processes
htop

# Python memory usage in notebook
import psutil
print(f"Memory: {psutil.virtual_memory().percent}%")
```

**Check CPU usage:**

```bash
# CPU info
lscpu

# Current load
uptime

# Top CPU processes
top
```

### Jupyter extensions

**Install JupyterLab extensions:**

```bash
# Example: Code formatter
pip install --user jupyterlab-code-formatter black
# Restart Jupyter to see extension
```

**Useful extensions:**
- `jupyterlab-git` - Git integration
- `jupyterlab-code-formatter` - Code formatting
- `jupyterlab-toc` - Table of contents
- `jupyterlab-vim` - Vim keybindings

### Backup your work

**Regular backups recommended:**

```bash
# Create tarball of your work
cd /home/jovyan
tar -czf backup_\$(date +%Y%m%d).tar.gz notebooks/ data/ scripts/

# Copy to share
cp backup_*.tar.gz /home/jovyan/shared/backups/
```

**Use Git for code:**

Commit and push regularly to GitHub/GitLab.

### Updating your image

**When you need to add packages:**

1. Update Dockerfile
2. Rebuild image with new tag:
```bash
podman build -t my-molino-image:v2.0 .
```
3. Tag for registry:
```bash
podman tag my-molino-image:v2.0 jupyter.islabccg.ir:5000/my-molino-image:v2.0
```
4. Push new version:
```bash
podman push --tls-verify=false jupyter.islabccg.ir:5000/my-molino-image:v2.0
```
5. Use new version in JupyterHub:
```bash
jupyter.islabccg.ir:5000/my-molino-image:v2.0
```

### Performance optimization

**For large computations:**

1. Use terminal instead of notebook
2. Use multiprocessing:
```bash
from multiprocessing import Pool

def process_data(data):
\# Your processing
return result

with Pool(4) as p:
results = p.map(process_data, data_list)
```

3. Monitor memory:
```bash
import tracemalloc
tracemalloc.start()

# Your code
current, peak = tracemalloc.get_traced_memory()
print(f"Peak memory: {peak / 10**6} MB")
```

---

## Quick Reference Commands

### Docker/Podman Commands

```bash
# Build image
podman build -t name .

# List images
podman images

# Remove image
podman rmi image_id

# Clean up
podman system prune -a

# Tag image
podman tag local_name registry/name:tag

# Push image
podman push --tls-verify=false registry/name:tag

# Pull image
podman pull registry/name:tag

# Run container locally
podman run -it --rm -p 8888:8888 image_name
```

### JupyterHub Workflow

```bash
# On your laptop:
1. cd ~/my-jupyter
2. nano Dockerfile
3. podman build -t my-image .
4. podman tag my-image jupyter.islabccg.ir:5000/my-image:latest
5. podman push --tls-verify=false jupyter.islabccg.ir:5000/my-image:latest

# In browser:
6. Open https://jupyter.islabccg.ir
7. Login
8. Select "Customizable CPU"
9. Enter: jupyter.islabccg.ir:5000/my-image:latest
10. Click "Start"
```

### Common Terminal Commands

```bash
# Navigation
cd /home/jovyan
pwd
ls -lh

# File operations
cp source dest
mv source dest
rm file
mkdir directory

# Check disk space
df -h
du -sh directory

# Process management
ps aux
top
htop
kill -9 PID

# Package management
conda list
pip list
conda install package
pip install --user package

# Environment variables
echo \$VARIABLE
export VARIABLE=value
```

### Git Commands

```bash
# Clone
git clone https://github.com/user/repo.git

# Status
git status

# Add and commit
git add .
git commit -m "message"

# Push
git push origin main

# Pull
git pull origin main

# Branch
git branch
git checkout -b new_branch
```

---

## Useful Resources

### Documentation

- **Molino GitHub**: https://github.com/changhoonhahn/molino
- **JupyterLab Docs**: https://jupyterlab.readthedocs.io/
- **Podman Docs**: https://docs.podman.io/
- **Docker Docs**: https://docs.docker.com/
- **Conda Docs**: https://docs.conda.io/

### Server Information

- **JupyterHub URL**: https://jupyter.islabccg.ir
- **Registry**: jupyter.islabccg.ir:5000
- **VPN Info**: https://ictc.sbu.ac.ir/vpn

### Getting Help

1. **Check this guide first**
2. **Review Event Log** in JupyterHub for errors
3. **Check container logs** if available
4. **Contact system administrator**
5. **Check molino documentation** for package-specific issues

---

## Checklist

Use this checklist to ensure you've completed all steps:

### Laptop Setup
- [ ] Docker/Podman installed
- [ ] VPN configured and tested
- [ ] Working directory created
- [ ] Dockerfile created
- [ ] Image built successfully
- [ ] Image pushed to registry

### JupyterHub Setup
- [ ] Logged into JupyterHub
- [ ] Selected "Customizable CPU"
- [ ] Entered correct image name
- [ ] Server started successfully
- [ ] JupyterLab interface loaded

### Verification
- [ ] molino imports without errors
- [ ] Environment variables set correctly
- [ ] Can access molino data directory
- [ ] Conda packages installed
- [ ] Basic molino commands work

### Cleanup
- [ ] Work saved to /home/jovyan
- [ ] Server stopped when finished
- [ ] Local images cleaned up (optional)

---

## Appendix

### Dockerfile Template Explained

```bash
# Base image - provides Jupyter and Python
FROM quay.io/jupyter/minimal-notebook:python-3.11

# Switch to root for system packages
USER root

# Install system dependencies
RUN apt-get update \&\& \
apt-get install -y package1 package2 \&\& \
rm -rf /var/lib/apt/lists/*  \# Clean cache

# Switch back to non-root user (security)
USER \${NB_UID}

# Set environment variables
ENV VAR_NAME=value

# Install Python packages
RUN conda install -y packages \&\& \
conda clean -afy  \# Clean cache

# Install via pip
RUN pip install --no-cache-dir package

# Clone and install from source
RUN git clone repo \&\& \
cd repo \&\& \
pip install -e .

# Set working directory
WORKDIR /home/jovyan
```

### Image Name Format

```bash
registry_host:port/image_name:tag
│              │   │          └─ Version/variant
│              │   └─ Your image name
│              └─ Registry port
└─ Registry hostname

Example:
jupyter.islabccg.ir:5000/my-molino-image:latest
```

### Common Error Codes

- **Exit code 1**: General error
- **Exit code 137**: Out of memory
- **Exit code 139**: Segmentation fault
- **Exit code 255**: Network/connection error

### Environment Variables Reference

```bash
# Standard Jupyter variables

HOME=/home/jovyan
USER=jovyan
NB_USER=jovyan
NB_UID=1000

# Molino specific
HDF5_USE_FILE_LOCKING=FALSE
MOLINO_DIR=/home/jovyan/DataFiles/Molino/Data

# Python related
PYTHONPATH=/home/jovyan
CONDA_DIR=/opt/conda
```

---

## Version History

**Version 1.0** - October 13, 2025
- Initial comprehensive guide
- Covers full workflow from build to usage
- Includes troubleshooting section
- Added advanced tips and quick reference

---

## License and Credits

This guide is provided as-is for educational purposes.

**Created for:** SBU JupyterHub Users
**Maintained by:** CCG Lab
**Last Updated:** October 13, 2025

For updates and corrections, please contact the system administrator.

---

**End of Guide**



