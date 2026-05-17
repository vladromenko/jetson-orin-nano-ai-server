# Jetson Orin Nano AI Server

A local AI server built on **NVIDIA Jetson Orin Nano 8GB** using **Docker**, **Ollama**, and **Open WebUI**.

The goal of this project was to turn a small edge AI device into a practical local LLM server that can be accessed from a browser in the local network. This setup is also intended as a foundation for future robotics projects, local assistants, and Raspberry Pi / Hailo integrations.

---

## Overview

This project documents the setup, testing, and current limitations of running local language models on Jetson Orin Nano 8GB.

The system provides:

- a local LLM backend with Ollama;
- a browser-based chat interface with Open WebUI;
- Docker-based deployment;
- local network access from another computer;
- SSD-based storage for heavier data;
- practical notes about model sizes that work realistically on 8GB RAM.

---

## Hardware

- NVIDIA Jetson Orin Nano 8GB
- SSD / NVMe storage
- Local network connection
- Laptop for access via browser, SSH, and VS Code Remote SSH

---

## Software Stack

- Linux / JetPack on Jetson
- Docker
- Ollama
- Open WebUI
- Local LLM models

---

## Current Status

Implemented:

- Docker is installed and working.
- Open WebUI is running in Docker.
- Open WebUI is accessible from the local network.
- Ollama API was tested.
- Basic local models were tested.
- Practical model limits for Jetson Orin Nano 8GB were evaluated.
- SSD storage is used for heavier data such as Docker volumes and model files.

Planned improvements:

- Add final model benchmarks.
- Add screenshots.
- Add a short demo video.
- Add VPN / remote access.
- Add monitoring for RAM, CPU, GPU, temperature, and storage.
- Integrate the server with robotics projects.

---

## Architecture

```text
Laptop / Browser
       |
       | Local Network
       v
Jetson Orin Nano
       |
       | Docker
       v
Open WebUI  --->  Ollama  --->  Local LLM Models
       |
       v
SSD Storage
```

Open WebUI provides the browser interface. Ollama runs the local language models. Docker is used to deploy Open WebUI. SSD storage is used to avoid filling the main system storage with containers, volumes, and model files.

---

## Basic System Checks

Check system information:

```bash
uname -a
cat /etc/os-release
```

Check memory:

```bash
free -h
```

Check storage:

```bash
df -h
lsblk
```

These commands help confirm the system version, available RAM, mounted drives, and SSD usage.

---

## Docker Checks

Check Docker version:

```bash
docker --version
```

Check running containers:

```bash
docker ps
```

Check all containers:

```bash
docker ps -a
```

Check Docker storage location and runtime:

```bash
docker info | grep -E "Docker Root Dir|Default Runtime|Runtimes"
```

This is useful for confirming that Docker is working and that heavy Docker data can be stored on SSD.

---

## Ollama Check

Check whether the Ollama API responds:

```bash
curl http://127.0.0.1:11434/api/tags
```

This command returns the list of locally available Ollama models.

Example purpose:

```text
Confirm that Ollama is running and that local models are available.
```

---

## Open WebUI Check

Check Open WebUI from the Jetson itself:

```bash
curl -I http://127.0.0.1:8080
```

Access Open WebUI from another device in the same local network:

```text
http://JETSON_IP:8080
```

Example:

```text
http://192.168.1.19:8080
```

---

## Open WebUI Docker Command

Example command used to run Open WebUI:

```bash
docker run -d \
  --network=host \
  --name open-webui \
  --restart always \
  -e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
  -v /mnt/ssd/open-webui:/app/backend/data \
  ghcr.io/open-webui/open-webui:main
```

Explanation:

- `--network=host` allows Open WebUI to use the Jetson network directly.
- `--restart always` makes the container restart automatically.
- `OLLAMA_BASE_URL=http://127.0.0.1:11434` connects Open WebUI to local Ollama.
- `/mnt/ssd/open-webui:/app/backend/data` stores Open WebUI data on SSD.

---

## Useful Docker Commands

Check running containers:

```bash
docker ps
```

View Open WebUI logs:

```bash
docker logs open-webui
```

Restart Open WebUI:

```bash
docker restart open-webui
```

Stop Open WebUI:

```bash
docker stop open-webui
```

Remove Open WebUI container:

```bash
docker rm open-webui
```

---

## Model Testing Notes

Jetson Orin Nano 8GB has limited memory for large language models. Because of this, model size and quantization matter a lot.

Practical observations:

| Model Size | Result |
|---|---|
| 1B | Comfortable for lightweight local tasks |
| 3B | Realistic for local chat |
| 4B | Possible depending on quantization and runtime |
| 7B | Often too heavy or unstable on this setup |

Observed problem with larger models:

```text
cudaMalloc failed: out of memory
llama runner process has terminated
```

Practical conclusion:

For Jetson Orin Nano 8GB, smaller models are usually better for stability. The most realistic range is around **1B–4B parameters**, depending on the exact model, quantization, and runtime configuration.

---

## Troubleshooting

### Open WebUI is not opening

Check whether the container is running:

```bash
docker ps
```

Check logs:

```bash
docker logs open-webui
```

Restart the container:

```bash
docker restart open-webui
```

---

### Ollama API does not respond

Check the Ollama API:

```bash
curl http://127.0.0.1:11434/api/tags
```

If Ollama is running as a system service, check its status:

```bash
systemctl status ollama
```

Restart Ollama:

```bash
sudo systemctl restart ollama
```

---

### Open WebUI container already exists

If the container already exists and needs to be recreated:

```bash
docker stop open-webui
docker rm open-webui
```

Then run the Open WebUI Docker command again.

---

### Model fails with out of memory

Use a smaller model.

On Jetson Orin Nano 8GB, 7B models are often not practical. Smaller models are more stable and better suited for this device.

---

## Use Cases

This setup can be used for:

- local AI chatbot;
- private local LLM experiments;
- robotics assistant backend;
- local coding assistant experiments;
- offline AI testing;
- edge AI portfolio project;
- future integration with Raspberry Pi + Hailo devices.

---

## Future Improvements

- Add screenshots of Open WebUI and terminal checks.
- Add model benchmark results.
- Add demo video.
- Add VPN access for remote usage.
- Add monitoring scripts.
- Add integration examples with robotics platforms.
- Add automatic startup validation.

---

## Why This Project Matters

This project shows how a compact edge AI device can be configured as a practical local AI server.

It is not just a simple web interface installation. The project includes hardware setup, Docker deployment, local model testing, SSD storage planning, network access, and real-world limitations of running LLMs on embedded AI hardware.

The main value of this project is that it creates a reusable base for future AI and robotics experiments.

