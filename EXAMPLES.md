# Usage Examples

## 🎬 Comprehensive FFmpeg CUDA API Examples

This document provides detailed examples for all video processing capabilities of the FFmpeg CUDA Docker API.

## 📋 Table of Contents

- [Basic Operations](#basic-operations)
- [Video Effects](#video-effects)
- [Audio Processing](#audio-processing)
- [Format Conversion](#format-conversion)
- [Advanced Techniques](#advanced-techniques)
- [Batch Processing](#batch-processing)
- [Performance Optimization](#performance-optimization)

## 🎯 Basic Operations

### List Available Files
```bash
curl http://localhost:15959/files
```

### Check API Health
```bash
curl http://localhost:15959/health
```

### Get System Information
```bash
curl http://localhost:15959/info
```

### View Performance Statistics
```bash
curl http://localhost:15959/stats
```

## 🎬 Video Encoding

### Basic H.264 Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "input_video.mp4",
    "output": "encoded_output.mp4",
    "preset": "fast"
  }'
```

### High Quality Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "source.mp4",
    "output": "high_quality.mp4",
    "preset": "slow",
    "crf": "18"
  }'
```

### HEVC (H.265) Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "hevc_video.mp4",
    "video_codec": "hevc_nvenc",
    "preset": "medium",
    "crf": "23"
  }'
```

### Custom Bitrate Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "custom_bitrate.mp4",
    "bitrate": "10M",
    "preset": "fast"
  }'
```

## 📐 Resolution and Scaling

### 4K Upscaling
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "1080p_video.mp4",
    "output": "4k_video.mp4",
    "scale": "3840x2160",
    "preset": "medium",
    "crf": "20"
  }'
```

### 1080p Downscaling
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "4k_video.mp4",
    "output": "1080p_video.mp4",
    "scale": "1920x1080",
    "preset": "fast"
  }'
```

### Mobile-Friendly Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "mobile_video.mp4",
    "scale": "1280x720",
    "bitrate": "2M",
    "preset": "fast"
  }'
```

## 🎭 Video Effects

### Fade Effects

#### Fade In from Black (3 seconds)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "fade_in.mp4",
    "video_filter": "fade=t=in:st=0:d=3"
  }'
```

#### Fade Out to Black (last 3 seconds)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "fade_out.mp4",
    "video_filter": "fade=t=out:st=27:d=3"
  }'
```

#### Crossfade Between Scenes
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video1.mp4",
    "input2": "video2.mp4",
    "output": "crossfade.mp4",
    "complex_filter": "[0:v][1:v]xfade=transition=fade:duration=2:offset=8[v]",
    "video_filter": "[v]"
  }'
```

### Text Overlays

#### Simple Centered Text
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "with_title.mp4",
    "video_filter": "drawtext=text='\''My Video Title'\'':fontsize=48:fontcolor=white:x=(w-text_w)/2:y=(h-text_h)/2"
  }'
```

#### Styled Text with Custom Font
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "styled_text.mp4",
    "video_filter": "drawtext=text='\''Professional Title'\'':fontfile=/workspace/fonts/arial.ttf:fontsize=64:fontcolor=white:borderw=3:bordercolor=black:x=(w-text_w)/2:y=50"
  }'
```

#### Animated Text (Scrolling)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "scrolling_text.mp4",
    "video_filter": "drawtext=text='\''Breaking News'\'':fontsize=36:fontcolor=red:x=w-mod(max(t-1\\,0)*(w+tw)/10\\,(w+tw)):y=h-th-10"
  }'
```

#### Timestamp Overlay
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "timestamped.mp4",
    "video_filter": "drawtext=text='\''%{localtime\\:%Y-%m-%d %H\\\\:%M\\\\:%S}'\'':fontsize=24:fontcolor=yellow:x=10:y=10"
  }'
```

### Blur and Focus Effects

#### Blur Specific Time Range
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "timed_blur.mp4",
    "video_filter": "gblur=sigma=10:enable='\''between(t,10,15)'\''"
  }'
```

#### Background Blur (Portrait Mode)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "portrait_blur.mp4",
    "video_filter": "split[main][bg];[bg]gblur=sigma=20[blurred];[blurred][main]overlay"
  }'
```

#### Motion Blur Effect
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "motion_blur.mp4",
    "video_filter": "minterpolate=fps=60:mi_mode=mci:mc_mode=aobmc:me_mode=bidir:vsbmc=1"
  }'
```

## 🎵 Audio Processing

### Extract Audio

#### Extract to WAV
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "extracted_audio.wav",
    "audio_only": true,
    "audio_codec": "pcm_s16le"
  }'
```

#### Extract to MP3
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "extracted_audio.mp3",
    "audio_only": true,
    "audio_codec": "mp3",
    "audio_bitrate": "320k"
  }'
```

### Audio Conversion

#### WAV to MP3
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "audio.wav",
    "output": "converted.mp3",
    "audio_only": true,
    "audio_codec": "mp3",
    "audio_bitrate": "192k"
  }'
```

#### MP3 to AAC
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "audio.mp3",
    "output": "converted.aac",
    "audio_only": true,
    "audio_codec": "aac",
    "audio_bitrate": "256k"
  }'
```

### Audio Effects

#### Volume Adjustment
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "louder_video.mp4",
    "audio_filter": "volume=2.0"
  }'
```

#### Audio Normalization
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "normalized_audio.mp4",
    "audio_filter": "loudnorm=I=-16:TP=-1.5:LRA=11"
  }'
```

#### Audio Fade In/Out
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "audio_fade.mp4",
    "audio_filter": "afade=t=in:ss=0:d=3,afade=t=out:st=27:d=3"
  }'
```

## 🔄 Format Conversion

### Video Format Conversion

#### MP4 to MOV
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "video.mov",
    "video_codec": "h264_nvenc",
    "audio_codec": "aac"
  }'
```

#### MOV to MP4
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mov",
    "output": "video.mp4",
    "video_codec": "h264_nvenc",
    "audio_codec": "aac"
  }'
```

#### MP4 to WebM
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "video.webm",
    "video_codec": "libvpx-vp9",
    "audio_codec": "libopus",
    "bitrate": "2M"
  }'
```

#### AVI to MP4
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.avi",
    "output": "video.mp4",
    "video_codec": "h264_nvenc",
    "audio_codec": "aac"
  }'
```

## 🎬 Advanced Video Operations

### Video Concatenation

#### Simple Concatenation
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "concat:video1.mp4|video2.mp4|video3.mp4",
    "output": "concatenated.mp4"
  }'
```

#### Concatenation with Transitions
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video1.mp4",
    "input2": "video2.mp4",
    "output": "smooth_concat.mp4",
    "complex_filter": "[0:v][1:v]xfade=transition=slideright:duration=1:offset=9[v];[0:a][1:a]acrossfade=d=1[a]"
  }'
```

### Picture-in-Picture

#### Basic PiP
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "background.mp4",
    "input2": "overlay.mp4",
    "output": "pip_video.mp4",
    "complex_filter": "[1:v]scale=320:240[ovrl];[0:v][ovrl]overlay=10:10"
  }'
```

#### Animated PiP (Moving)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "background.mp4",
    "input2": "overlay.mp4",
    "output": "moving_pip.mp4",
    "complex_filter": "[1:v]scale=320:240[ovrl];[0:v][ovrl]overlay='\''if(gte(t,0), -w+(t)*150, NAN)'\'':10"
  }'
```

### Speed and Time Effects

#### Speed Up (2x)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "fast_video.mp4",
    "video_filter": "setpts=0.5*PTS",
    "audio_filter": "atempo=2.0"
  }'
```

#### Slow Motion (0.5x)
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "slow_motion.mp4",
    "video_filter": "setpts=2.0*PTS",
    "audio_filter": "atempo=0.5"
  }'
```

#### Time-lapse Effect
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "timelapse.mp4",
    "video_filter": "setpts=0.1*PTS",
    "audio_filter": "atempo=10.0"
  }'
```

## ✨ Creative Effects

### Color Grading

#### Increase Saturation
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "saturated.mp4",
    "video_filter": "eq=saturation=1.5:brightness=0.1:contrast=1.2"
  }'
```

#### Vintage Film Look
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "vintage.mp4",
    "video_filter": "curves=vintage,eq=brightness=-0.1:contrast=1.1"
  }'
```

#### Black and White
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "bw_video.mp4",
    "video_filter": "hue=s=0"
  }'
```

### Noise and Grain

#### Film Grain Effect
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "film_grain.mp4",
    "video_filter": "noise=alls=10:allf=t"
  }'
```

#### Old TV Effect
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "old_tv.mp4",
    "video_filter": "noise=alls=20:allf=t+u,eq=contrast=1.2:brightness=-0.1"
  }'
```

### Stabilization

#### Video Stabilization
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "shaky_video.mp4",
    "output": "stabilized.mp4",
    "video_filter": "vidstabdetect=shakiness=10:accuracy=10,vidstabtransform=smoothing=10"
  }'
```

## 📊 Batch Processing Examples

### Process Multiple Files
```bash
#!/bin/bash
# Batch processing script

FILES=("video1.mp4" "video2.mp4" "video3.mp4")

for file in "${FILES[@]}"; do
    echo "Processing $file..."
    curl -X POST http://localhost:15959/encode \
      -H "Content-Type: application/json" \
      -d "{
        \"input\": \"$file\",
        \"output\": \"processed_$file\",
        \"preset\": \"fast\",
        \"crf\": \"23\"
      }"
    echo "Completed $file"
done
```

### Quality Ladder Generation
```bash
#!/bin/bash
# Generate multiple quality versions

INPUT="source.mp4"
QUALITIES=("720p:1280x720:2M" "1080p:1920x1080:5M" "4k:3840x2160:15M")

for quality in "${QUALITIES[@]}"; do
    IFS=':' read -r name resolution bitrate <<< "$quality"
    echo "Generating $name version..."
    
    curl -X POST http://localhost:15959/encode \
      -H "Content-Type: application/json" \
      -d "{
        \"input\": \"$INPUT\",
        \"output\": \"${name}_${INPUT}\",
        \"scale\": \"$resolution\",
        \"bitrate\": \"$bitrate\",
        \"preset\": \"medium\"
      }"
done
```

## 🚀 Performance Optimization

### High-Speed Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "fast_encode.mp4",
    "preset": "ultrafast",
    "video_codec": "h264_nvenc"
  }'
```

### High-Quality Encoding
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "high_quality.mp4",
    "preset": "slow",
    "crf": "16",
    "video_codec": "h264_nvenc"
  }'
```

### Balanced Settings
```bash
curl -X POST http://localhost:15959/encode \
  -H "Content-Type: application/json" \
  -d '{
    "input": "video.mp4",
    "output": "balanced.mp4",
    "preset": "medium",
    "crf": "20",
    "video_codec": "h264_nvenc"
  }'
```

## 🔧 Custom Parameters Reference

### Video Codecs
- `h264_nvenc` - NVIDIA H.264 (fastest)
- `hevc_nvenc` - NVIDIA H.265 (best compression)
- `libx264` - Software H.264 (compatibility)
- `libx265` - Software H.265 (best quality)

### Audio Codecs
- `aac` - AAC (recommended)
- `mp3` - MP3 (compatibility)
- `pcm_s16le` - Uncompressed WAV
- `libopus` - Opus (web streaming)

### Presets (NVENC)
- `ultrafast` - Fastest encoding
- `fast` - Fast encoding (recommended)
- `medium` - Balanced speed/quality
- `slow` - Better quality
- `lossless` - No quality loss

### CRF Values
- `16-18` - Very high quality
- `20-23` - High quality (recommended)
- `24-28` - Medium quality
- `28+` - Lower quality, smaller files

---

**💡 Pro Tips:**
- Use `preset=fast` for real-time processing
- Use `crf=20` for high-quality archival
- Monitor GPU usage with `/info` endpoint
- Test settings with short clips first
- Combine multiple filters with commas

