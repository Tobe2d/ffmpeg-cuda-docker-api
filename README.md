# FFmpeg CUDA Docker API

🚀 **Production-ready FFmpeg with NVIDIA CUDA acceleration in Docker**

A comprehensive Docker solution providing FFmpeg with NVIDIA GPU acceleration through a beautiful web interface and RESTful API. Built for high-performance video processing with hardware-accelerated encoding, decoding, and advanced video effects.

![FFmpeg CUDA API](https://img.shields.io/badge/FFmpeg-6.1.1-green) ![CUDA](https://img.shields.io/badge/CUDA-12.3.1-blue) ![Docker](https://img.shields.io/badge/Docker-Production%20Ready-blue) ![GPU](https://img.shields.io/badge/GPU-NVIDIA%20RTX%204090-green)

## ✨ Features

- 🎬 **Hardware Accelerated**: NVENC/NVDEC for lightning-fast processing
- 🌐 **Beautiful Web Interface**: Professional dashboard with real-time monitoring
- 🔧 **RESTful API**: Complete video processing through HTTP endpoints
- 📊 **Production Ready**: Gunicorn WSGI server with health checks
- 🎭 **Advanced Effects**: Concatenation, fades, text overlays, blur, mixing
- 🎵 **Audio Processing**: Extract, convert, and enhance audio
- 🔄 **Format Conversion**: Support for MP4, MOV, AVI, WebM, and more
- 📈 **Performance Monitoring**: Real-time statistics and GPU monitoring
- 🛡️ **Enterprise Security**: Non-root execution, input validation, resource limits

## 🎯 Tested Configuration

This solution has been thoroughly tested and optimized for:

- **CPU**: Intel Core i9 13th Generation
- **GPU**: NVIDIA GeForce RTX 4090 (24GB VRAM)
- **OS**: Windows 11 (latest updates)
- **Docker**: Docker Desktop with WSL2
- **CUDA**: Version 12.3.1
- **Driver**: NVIDIA Driver 576.52
- **Performance**: 300-500+ FPS (1080p H.264 encoding)

## 🚀 Quick Start

### Prerequisites

- Windows 11 with WSL2 enabled
- Docker Desktop with GPU support
- NVIDIA RTX series GPU (RTX 20/30/40 series recommended)
- Latest NVIDIA drivers
- At least 8GB free disk space

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/ffmpeg-cuda-docker-api.git
   cd ffmpeg-cuda-docker-api
   ```

2. **Build the Docker image**
   ```bash
   docker build -t ffmpeg-cuda-api:latest .
   ```

3. **Run the container**
   ```bash
   docker run -d --name ffmpeg-cuda-api --gpus all \
     -p 15959:5000 \
     -v /mnt/h/Downloads:/workspace \
     --restart unless-stopped \
     ffmpeg-cuda-api:latest
   ```

4. **Access the web interface**
   - Open: http://localhost:15959/
   - From other containers: http://host.docker.internal:15959/

## 🌐 API Access

### Local Access
- **Web Interface**: http://localhost:15959/
- **Health Check**: http://localhost:15959/health
- **API Documentation**: http://localhost:15959/

### Docker Container Access
When accessing from other Docker containers, use:
```
http://host.docker.internal:15959/
```

### Available Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Web interface and documentation |
| `/health` | GET | API health and system status |
| `/files` | GET | List available video files |
| `/info` | GET | FFmpeg capabilities and GPU info |
| `/stats` | GET | Performance statistics |
| `/encode` | POST | Video processing and encoding |

## 🎬 Usage Examples

### Basic Video Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "encoded.mp4",
    "preset": "fast"
  }'
```

### Advanced Effects
```bash
# Add text overlay with custom font
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "titled.mp4",
    "video_filter": "drawtext=text='\''My Video'\'':fontfile=/workspace/fonts/arial.ttf:fontsize=64:fontcolor=white:x=(w-text_w)/2:y=50"
  }'

# Concatenate multiple videos
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "concat:video1.mp4|video2.mp4|video3.mp4",
    "output": "combined.mp4"
  }'

# 4K upscaling with blur effect
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "1080p.mp4",
    "output": "4k_blurred.mp4",
    "scale": "3840x2160",
    "video_filter": "gblur=sigma=5:enable='\''between(t,10,15)'\''"
  }'
```

## 📁 File Structure

```
ffmpeg-cuda-docker-api/
├── Dockerfile              # Production Docker configuration
├── ffmpeg_api.py           # Enhanced Flask API with web interface
├── start_api.sh            # Production startup script
├── gunicorn.conf.py        # Gunicorn production configuration
├── README.md               # This file
├── SETUP.md                # Detailed setup instructions
├── EXAMPLES.md             # Comprehensive usage examples
├── .gitignore              # Git ignore rules
└── docs/                   # Additional documentation
    ├── API.md              # API reference
    ├── PERFORMANCE.md      # Performance tuning guide
    └── TROUBLESHOOTING.md  # Common issues and solutions
```

## 🔧 Configuration

### Custom Fonts
Place custom fonts in your mounted directory:
```
H:\Downloads\fonts\arial.ttf
H:\Downloads\fonts\custom_font.ttf
```

### Environment Variables
```bash
# Optional environment variables
-e FFMPEG_THREADS=4          # Number of encoding threads
-e CUDA_VISIBLE_DEVICES=0    # Specific GPU to use
-e API_WORKERS=2             # Number of Gunicorn workers
```

## 📊 Performance

### Expected Performance (RTX 4090)
- **1080p H.264**: 300-500+ FPS
- **4K H.264**: 100-200+ FPS  
- **HEVC encoding**: 150-300+ FPS
- **Memory usage**: 2-4GB VRAM typical
- **Processing time**: 2-5 seconds for typical videos

### Optimization Tips
- Use `preset=fast` for speed, `preset=slow` for quality
- Enable hardware acceleration with `hwaccel=cuda`
- Monitor GPU usage with `/info` endpoint
- Use appropriate CRF values (18-28)

## 🛠️ Development

### Building from Source
```bash
# Clone repository
git clone https://github.com/yourusername/ffmpeg-cuda-docker-api.git
cd ffmpeg-cuda-docker-api

# Build development image
docker build -t ffmpeg-cuda-api:dev .

# Run with development settings
docker run -it --gpus all \
  -p 15959:5000 \
  -v $(pwd):/app \
  -v /mnt/h/Downloads:/workspace \
  ffmpeg-cuda-api:dev bash
```

### API Development
The Flask API supports hot-reloading for development:
```bash
# Inside container
python3 ffmpeg_api.py
```

## 🔍 Monitoring

### Health Checks
```bash
# Container health
docker inspect ffmpeg-cuda-api --format='{{.State.Health.Status}}'

# API health
curl http://localhost:15959/health
```

### Performance Monitoring
```bash
# API statistics
curl http://localhost:15959/stats

# GPU monitoring
curl http://localhost:15959/info
```

### Logs
```bash
# Follow logs
docker logs -f ffmpeg-cuda-api

# Recent logs
docker logs --tail 100 ffmpeg-cuda-api
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- FFmpeg team for the incredible multimedia framework
- NVIDIA for CUDA and hardware acceleration support
- Docker community for containerization best practices
- Flask team for the excellent web framework

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/ffmpeg-cuda-docker-api/issues)
- **Discussions**: [GitHub Discussions](https://github.com/yourusername/ffmpeg-cuda-docker-api/discussions)
- **Documentation**: [Wiki](https://github.com/yourusername/ffmpeg-cuda-docker-api/wiki)

## 🔗 Related Projects

- [FFmpeg](https://ffmpeg.org/) - Multimedia framework
- [NVIDIA Video Codec SDK](https://developer.nvidia.com/video-codec-sdk) - Hardware acceleration
- [Docker](https://www.docker.com/) - Containerization platform

---

**⚡ Built for speed, designed for production, optimized for NVIDIA RTX 4090** ⚡

