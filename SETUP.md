# Setup Instructions

## 🎯 Complete Setup Guide for FFmpeg CUDA Docker API

This guide provides step-by-step instructions for setting up the FFmpeg CUDA Docker API on Windows 11 with WSL2 and Docker Desktop.

## 📋 Prerequisites

### System Requirements
- **Operating System**: Windows 11 (latest updates)
- **CPU**: Intel Core i5 or better (tested with i9 13th gen)
- **GPU**: NVIDIA RTX series (RTX 20/30/40 series recommended)
- **RAM**: 16GB+ recommended
- **Storage**: 20GB+ free space
- **Internet**: Stable connection for downloads

### Software Requirements
- **WSL2**: Windows Subsystem for Linux 2
- **Docker Desktop**: Latest version with GPU support
- **NVIDIA Drivers**: Latest GeForce drivers (576.52+ recommended)
- **Git**: For cloning the repository

## 🔧 Step 1: Enable WSL2

1. **Open PowerShell as Administrator**
   ```powershell
   # Enable WSL and Virtual Machine Platform
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   
   # Restart your computer
   ```

2. **Install WSL2 Linux Kernel Update**
   - Download from: https://aka.ms/wsl2kernel
   - Install the downloaded package

3. **Set WSL2 as Default**
   ```powershell
   wsl --set-default-version 2
   ```

4. **Install Ubuntu**
   ```powershell
   wsl --install -d Ubuntu
   ```

## 🐳 Step 2: Install Docker Desktop

1. **Download Docker Desktop**
   - Visit: https://www.docker.com/products/docker-desktop/
   - Download Docker Desktop for Windows

2. **Install Docker Desktop**
   - Run the installer
   - Enable "Use WSL 2 instead of Hyper-V" option
   - Complete installation and restart

3. **Configure Docker Desktop**
   - Open Docker Desktop
   - Go to Settings → General
   - ✅ Enable "Use the WSL 2 based engine"
   - Go to Settings → Resources → WSL Integration
   - ✅ Enable integration with Ubuntu
   - Click "Apply & Restart"

## 🎮 Step 3: Install NVIDIA Drivers

1. **Download Latest Drivers**
   - Visit: https://www.nvidia.com/drivers/
   - Select your GPU model (e.g., RTX 4090)
   - Download and install latest drivers

2. **Verify Installation**
   ```bash
   # In WSL2 Ubuntu terminal
   nvidia-smi
   ```
   
   Expected output:
   ```
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 576.52       Driver Version: 576.52       CUDA Version: 12.9   |
   |-------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |===============================+======================+======================|
   |   0  NVIDIA GeForce ...  On   | 00000000:01:00.0  On |                  N/A |
   |  0%   36C    P8    34W / 450W |  21647MiB / 24564MiB |      2%      Default |
   +-------------------------------+----------------------+----------------------+
   ```

## 📁 Step 4: Prepare Workspace

1. **Create Project Directory**
   ```bash
   # In WSL2 Ubuntu terminal
   cd /mnt/c/Users/yourusername/
   mkdir ffmpeg-cuda-project
   cd ffmpeg-cuda-project
   ```

2. **Create Fonts Directory (Optional)**
   ```bash
   # For custom fonts support
   mkdir -p /mnt/h/Downloads/fonts
   # Copy your .ttf font files here
   ```

## 📥 Step 5: Clone and Build

1. **Clone Repository**
   ```bash
   git clone https://github.com/yourusername/ffmpeg-cuda-docker-api.git
   cd ffmpeg-cuda-docker-api
   ```

2. **Build Docker Image**
   ```bash
   # This takes 30-45 minutes on first build
   docker build -t ffmpeg-cuda-api:latest .
   ```

3. **Verify Build**
   ```bash
   # Check if image was created
   docker images | grep ffmpeg-cuda-api
   ```

## 🚀 Step 6: Run the Container

1. **Start the API Container**
   ```bash
   docker run -d \
     --name ffmpeg-cuda-api \
     --gpus all \
     -p 15959:5000 \
     -v /mnt/h/Downloads:/workspace \
     --restart unless-stopped \
     --health-cmd="curl -f http://localhost:5000/health || exit 1" \
     --health-interval=30s \
     --health-timeout=10s \
     --health-retries=3 \
     ffmpeg-cuda-api:latest
   ```

2. **Verify Container is Running**
   ```bash
   # Check container status
   docker ps | grep ffmpeg-cuda-api
   
   # Check logs
   docker logs ffmpeg-cuda-api
   
   # Check health
   docker inspect ffmpeg-cuda-api --format='{{.State.Health.Status}}'
   ```

## 🧪 Step 7: Test the API

1. **Test Health Endpoint**
   ```bash
   curl http://localhost:15959/health
   ```

2. **Test File Listing**
   ```bash
   curl http://localhost:15959/files
   ```

3. **Test Video Encoding**
   ```bash
   # Place a test video in H:\Downloads first
   curl -X POST http://localhost:15959/encode \
     -H "Content-Type: application/json" \
     -d '{
       "input": "test_video.mp4",
       "output": "encoded_test.mp4",
       "preset": "fast"
     }'
   ```

4. **Access Web Interface**
   - Open browser: http://localhost:15959/
   - Verify all endpoints are working

## 🌐 Step 8: Docker Network Access

### From Other Containers
When accessing the API from other Docker containers, use:
```bash
# Instead of localhost:15959
http://host.docker.internal:15959/

# Example from another container
curl http://host.docker.internal:15959/health
```

### From External Networks
To access from other machines on your network:
```bash
# Run with external IP binding
docker run -d \
  --name ffmpeg-cuda-api \
  --gpus all \
  -p 0.0.0.0:15959:5000 \
  -v /mnt/h/Downloads:/workspace \
  --restart unless-stopped \
  ffmpeg-cuda-api:latest

# Access from other machines
http://YOUR_MACHINE_IP:15959/
```

## 🔧 Configuration Options

### Environment Variables
```bash
# Custom configuration
docker run -d \
  --name ffmpeg-cuda-api \
  --gpus all \
  -p 15959:5000 \
  -v /mnt/h/Downloads:/workspace \
  -e FFMPEG_THREADS=8 \
  -e CUDA_VISIBLE_DEVICES=0 \
  -e API_WORKERS=4 \
  --restart unless-stopped \
  ffmpeg-cuda-api:latest
```

### Volume Mounts
```bash
# Multiple volume mounts
docker run -d \
  --name ffmpeg-cuda-api \
  --gpus all \
  -p 15959:5000 \
  -v /mnt/h/Downloads:/workspace \
  -v /mnt/c/Videos:/input \
  -v /mnt/d/Output:/output \
  -v /mnt/c/Fonts:/workspace/fonts \
  --restart unless-stopped \
  ffmpeg-cuda-api:latest
```

## 📊 Performance Optimization

### GPU Memory Management
```bash
# Limit GPU memory usage
docker run -d \
  --name ffmpeg-cuda-api \
  --gpus '"device=0"' \
  --memory=8g \
  --memory-swap=16g \
  -p 15959:5000 \
  -v /mnt/h/Downloads:/workspace \
  ffmpeg-cuda-api:latest
```

### CPU Optimization
```bash
# Limit CPU usage
docker run -d \
  --name ffmpeg-cuda-api \
  --gpus all \
  --cpus="8.0" \
  --cpu-shares=1024 \
  -p 15959:5000 \
  -v /mnt/h/Downloads:/workspace \
  ffmpeg-cuda-api:latest
```

## 🔍 Monitoring and Maintenance

### Container Management
```bash
# Start/Stop/Restart
docker start ffmpeg-cuda-api
docker stop ffmpeg-cuda-api
docker restart ffmpeg-cuda-api

# Update container
docker pull ffmpeg-cuda-api:latest
docker stop ffmpeg-cuda-api
docker rm ffmpeg-cuda-api
# Run new container with same command
```

### Log Management
```bash
# View logs
docker logs ffmpeg-cuda-api

# Follow logs in real-time
docker logs -f ffmpeg-cuda-api

# Limit log size
docker run -d \
  --name ffmpeg-cuda-api \
  --log-opt max-size=100m \
  --log-opt max-file=3 \
  # ... other options
```

### Performance Monitoring
```bash
# Container stats
docker stats ffmpeg-cuda-api

# GPU monitoring
nvidia-smi -l 1

# API statistics
curl http://localhost:15959/stats
```

## 🚨 Troubleshooting

### Common Issues

1. **GPU Not Detected**
   ```bash
   # Check NVIDIA drivers
   nvidia-smi
   
   # Check Docker GPU support
   docker run --rm --gpus all nvidia/cuda:12.3.1-base nvidia-smi
   ```

2. **Container Won't Start**
   ```bash
   # Check logs
   docker logs ffmpeg-cuda-api
   
   # Check Docker daemon
   docker info
   ```

3. **API Not Responding**
   ```bash
   # Check container health
   docker inspect ffmpeg-cuda-api --format='{{.State.Health.Status}}'
   
   # Check port binding
   docker port ffmpeg-cuda-api
   ```

4. **Performance Issues**
   ```bash
   # Check GPU utilization
   nvidia-smi
   
   # Check container resources
   docker stats ffmpeg-cuda-api
   ```

### Getting Help

1. **Check Logs First**
   ```bash
   docker logs --tail 50 ffmpeg-cuda-api
   ```

2. **Verify System Requirements**
   - NVIDIA drivers updated
   - Docker Desktop running
   - WSL2 properly configured
   - Sufficient disk space

3. **Test Components Individually**
   ```bash
   # Test Docker
   docker run hello-world
   
   # Test GPU access
   docker run --rm --gpus all nvidia/cuda:12.3.1-base nvidia-smi
   
   # Test FFmpeg
   docker run --rm ffmpeg-cuda-api:latest ffmpeg -version
   ```

## ✅ Success Indicators

Your setup is working correctly when:

- ✅ `nvidia-smi` shows your GPU
- ✅ Docker Desktop shows GPU support enabled
- ✅ Container starts without errors
- ✅ Health check returns "healthy"
- ✅ Web interface loads at http://localhost:15959/
- ✅ Video encoding completes successfully
- ✅ GPU utilization visible during encoding

## 🎯 Next Steps

After successful setup:

1. **Explore the Web Interface**: http://localhost:15959/
2. **Read the Examples**: Check EXAMPLES.md for advanced usage
3. **Monitor Performance**: Use `/stats` and `/info` endpoints
4. **Customize Configuration**: Adjust settings for your workflow
5. **Integrate with Applications**: Use the API in your projects

---

**🎉 Congratulations! Your FFmpeg CUDA Docker API is ready for production use!**

