
# Local AI

Local AI is a lightweight, fast, and secure AI inference service that can run anywhere.

## Table of Contents

- [Download Binary](#download-binary)
- [Run Local AI](#run-local-ai)
- [Web UI](#web-ui)
- [List Models](#list-models)
- [Install Models](#install-models)
- [With Docker Compose](#with-docker-compose)
- [Try It Out!](#try-it-out)
- [An Actual Use Case: Troubleshooting K8s](#an-actual-use-case-troubleshooting-k8s)
  - [Creating a Test Cluster with an Issue](#creating-a-test-cluster-with-an-issue)
  - [Install K8sGPT](#install-k8sgpt)
  - [Setup LocalAI as the AI Backend](#setup-localai-as-the-ai-backend)
  - [Analyze Our Issue with Local AI](#analyze-our-issue-with-local-ai)
  - [Debugging with Log Level](#debugging-with-log-level)

## Download Binary

```bash
curl -Lo local-ai "https://github.com/mudler/LocalAI/releases/download/v2.22.0/local-ai-$(uname -s)-$(uname -m)" && chmod +x local-ai
```

## Run Local AI

```bash
./local-ai
```

## Web UI

Open a browser tab and head over to [http://localhost:8080](http://localhost:8080) to check out the model gallery.

## List Models

```bash
local-ai models list
```

Or

```bash
curl http://localhost:8080/models
```

## Install Models

You can either use the web UI to install the models or the CLI:

```bash
local-ai models install llama-3.2-3b-instruct:q4_k_m
```

## With Docker Compose

```yaml
# Prepare the models into the `models` directory
mkdir models

# Copy your models to the directory
cp your-model.gguf models/

# Run the LocalAI container
docker run -p 8080:8080 -v $PWD/models:/models -ti --rm quay.io/go-skynet/local-ai:latest --models-path /models --context-size 700 --threads 4

# Expected output:
# ┌───────────────────────────────────────────────────┐
# │                   Fiber v2.42.0                   │
# │               http://127.0.0.1:8080               │
# │       (bound on host 0.0.0.0 and port 8080)       │
# │                                                   │
# │ Handlers ............. 1  Processes ........... 1 │
# │ Prefork ....... Disabled  PID ................. 1 │
# └───────────────────────────────────────────────────┘

# Test the endpoint with curl
curl http://localhost:8080/v1/completions -H "Content-Type: application/json" -d '{
     "model": "your-model.gguf",
     "prompt": "A long time ago in a galaxy far, far away",
     "temperature": 0.7
   }'
```

## Try It Out!

```bash
curl http://localhost:8080/v1/completions -H "Content-Type: application/json" -d '{
     "model": "llama-3.2-3b-instruct:q4_k_m.gguf",
     "prompt": "Hey, how are you today?",
     "temperature": 0.7
   }'
```

## An Actual Use Case: Troubleshooting K8s

### Creating a Test Cluster with an Issue

```bash
kind create cluster
kubectl run nginx --image nginx2
```

### Install K8sGPT

```bash
brew tap k8sgpt-ai/k8sgpt
brew install k8sgpt
```

### Setup LocalAI as the AI Backend

```bash
k8sgpt auth add -b localai -u http://localhost:8080/v1 --model llama-3.2-3b-instruct-q4_k_m.gguf
```

### Analyze Our Issue with Local AI

```bash
k8sgpt analyze --explain --backend localai 
```

### Debugging with Log Level

Set the log level to debug and rerun the solution:

```bash
./local-ai --log-level=debug
```

```bash
k8sgpt analyze --explain --backend localai
```

## Resources

- [LocalAI](https://localai.io)
- [K8sGPT](https://k8sgpt.ai)
- [LocalAI GitHub](https://github.com/mudler/LocalAI)
- [K8sGPT GitHub](https://github.com/k8sgpt-ai)
