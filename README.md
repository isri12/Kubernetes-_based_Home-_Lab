# Kubernetes-_based_Home-_Lab
 Home lab system Kubernetes cluster (K3s) 

# Home Lab Setup

This repository contains configuration and deployment files for my personal home lab setup, featuring Kubernetes, monitoring, and media server capabilities on repurposed hardware.

## Overview

This home lab system integrates:
- Kubernetes cluster (K3s) running on a repurposed laptop
- Prometheus and Grafana for monitoring
- Plex/Jellyfin media server
- Integration with existing Raspberry Pi devices (Pi-hole, Home Assistant)

# Homelab Architecture (Color-Coded)

```mermaid
graph TD
    %% MAIN SERVER
    subgraph A[Powerful PC - Proxmox]
        style A fill:#fff3e0,stroke:#ff9800,stroke-width:2px,color:#000

        subgraph Media[Media Services]
            style Media fill:#fff8e1,stroke:#ffb300,color:#000
            A1[TrueNAS VM - NAS Storage]
            A2[Plex Media Server VM - GPU Transcoding]
        end

        subgraph Infra[Infrastructure & Automation]
            style Infra fill:#e3f2fd,stroke:#2196f3,color:#000
            A3[Kubernetes Master Node VM]
            A5[Docker Playground VM]
            A6[Terraform + Ansible Control Node]
        end

        subgraph Monitoring[Monitoring & Analytics]
            style Monitoring fill:#f1f8e9,stroke:#4caf50,color:#000
            A4[Prometheus + Grafana VM]
        end


```mermaid
graph TB
    %% Main Components
    Internet[Internet]
    Router[Home Router/Network Switch]
    
    Laptop[Used Laptop]
    subgraph "Laptop Services"
        K8s[Kubernetes - K3s]
        Monitoring[Prometheus & Grafana]
        Media[Plex/Jellyfin]
    end
    
    PiHole[Raspberry Pi - Pi-hole]
    HomeAssist[Raspberry Pi - Home Assistant]
    OtherPi[Raspberry Pi - Future Use]
    
    HostingerVPS[Hostinger VPS<br>Website]
    
    Devices[Client Devices<br>TV, Phones, Laptops]
    
    %% Connections
    Internet --- Router
    Router --- Laptop
    Router --- PiHole
    Router --- HomeAssist
    Router --- OtherPi
    Router --- Devices
    Internet --- HostingerVPS
    
    %% Service Data Flows
    PiHole -.DNS Filtering.-> Router
    Laptop -.Media Streaming.-> Devices
    HomeAssist -.Smart Home Control.-> Devices
    Monitoring -.Website Monitoring.-> HostingerVPS
    
    %% Styling
    classDef laptop fill:#326CE5,color:white,stroke-width:2px;
    classDef raspberry fill:#c42525,color:white,stroke-width:2px;
    classDef network fill:#3498db,color:white,stroke-width:2px;
    classDef client fill:#f39c12,color:white,stroke-width:2px;
    classDef vps fill:#16a085,color:white,stroke-width:2px;
    
    class Laptop,K8s,Monitoring,Media laptop;
    class PiHole,HomeAssist,OtherPi raspberry;
    class Internet,Router network;
    class Devices client;
    class HostingerVPS vps;
```


## Hardware

### Main Server (Repurposed Laptop)
- Used laptop (recommended specs: i5/i7 CPU, 16GB+ RAM, 512GB+ SSD)
- Running Linux (Ubuntu Server 22.04 LTS recommended)
- Connected via Ethernet to home network (recommended)

### Existing Raspberry Pi Devices
- Raspberry Pi running Pi-hole (DNS ad-blocking)
- Raspberry Pi running Home Assistant
- Additional Raspberry Pi devices for other services

## Software Architecture

### Kubernetes Layer (K3s)
K3s is a lightweight Kubernetes distribution perfect for this environment:
- Low resource requirements
- Simple setup
- Production-ready features

### Monitoring Stack
- **Prometheus**: Collects metrics from all systems
- **Grafana**: Visualizes metrics and creates dashboards
- Configured to monitor:
  - Kubernetes components
  - Node metrics (CPU, memory, disk)
  - Pi-hole statistics
  - Home Assistant devices
  - Media server performance
  - **Hostinger VPS** (website uptime, performance, resource usage)

### Media Server
- **Option 1: Plex** (more polished UI, transcoding capabilities)
- **Option 2: Jellyfin** (fully open-source, no premium features)
- Deployed as containerized application within Kubernetes

## Directory Structure
```
├── kubernetes/
│   ├── k3s-install/           # K3s installation scripts
│   ├── monitoring/            # Prometheus & Grafana configs
│   │   ├── prometheus/        # Prometheus configuration
│   │   ├── grafana/           # Grafana dashboards
│   │   └── alertmanager/      # Alert configuration
│   ├── media-server/          # Plex/Jellyfin deployment
│   └── storage/               # PersistentVolume configurations
├── integration/
│   ├── pihole/                # Pi-hole integration scripts
│   ├── homeassistant/         # Home Assistant integration
│   └── metrics-exporters/     # Custom metrics collection
│   ├── hostinger-vps/         # Hostinger VPS monitoring
│   │   ├── exporters/         # Node exporter configuration
│   │   ├── dashboards/        # Grafana dashboards for VPS
│   │   └── alerts/            # Alert rules for VPS monitoring
├── scripts/
│   ├── backup/                # Backup automation
│   └── maintenance/           # Maintenance utilities
└── docs/
    ├── setup.md               # Detailed setup instructions
    ├── monitoring.md          # Monitoring configuration guide
    └── troubleshooting.md     # Common issues and solutions
```

## Setup Guide

### 1. Laptop Preparation
1. Install Ubuntu Server 22.04 LTS
2. Configure static IP address
3. Enable SSH access
4. Update system packages

### 2. K3s Installation
```bash
# Install K3s in server mode
curl -sfL https://get.k3s.io | sh -

# Get kubeconfig file
sudo cat /etc/rancher/k3s/k3s.yaml > ~/.kube/config
sudo chmod 600 ~/.kube/config
```

### 3. Deploy Monitoring Stack
```bash
# Deploy Prometheus and Grafana
kubectl apply -f kubernetes/monitoring/

# Check deployment status
kubectl get pods -n monitoring
```

### 4. Deploy Media Server
```bash
# Deploy Plex or Jellyfin
kubectl apply -f kubernetes/media-server/jellyfin/  # or plex/

# Check deployment status
kubectl get pods -n media
```

### 5. Integration with Raspberry Pi Devices
1. Configure Pi-hole exporter to expose metrics to Prometheus
2. Set up Home Assistant integration with Grafana
3. Configure network to ensure all devices can communicate

## Maintenance

### Backups
Automatic backups are configured for:
- Kubernetes configurations
- Prometheus data
- Media server metadata
- Media content (optional, due to size considerations)

### Updates
To update components:
```bash
# Update K3s
curl -sfL https://get.k3s.io | sh -

# Update deployments
kubectl apply -f kubernetes/monitoring/
kubectl apply -f kubernetes/media-server/
```

## Troubleshooting

Common issues and solutions:
- Insufficient resources: Adjust resource limits in deployment files
- Network connectivity: Ensure proper network configuration between devices
- Storage issues: Monitor disk usage and expand storage as needed

## Future Enhancements
- Add CI/CD pipeline for automating deployments
- Implement automatic rolling updates
- Add external access via reverse proxy with proper security
- Expand monitoring to include more detailed metrics
- Integrate with cloud backup solutions

## License
This project is licensed under the MIT License - see the LICENSE file for details.


,,,
graph TD
    %% Main Server
    subgraph A[Powerful PC - Proxmox]
        style A fill:#ffe0cc,stroke:#ff6600,stroke-width:2px
        subgraph Media[Media Services]
            style Media fill:#fff5e6,stroke:#ff9900
            A1[TrueNAS VM - NAS Storage]
            A2[Plex Media Server VM - GPU Transcoding]
        end

        subgraph Infra[Infrastructure & Automation]
            style Infra fill:#e6f7ff,stroke:#3399ff
            A3[Kubernetes Master Node VM]
            A5[Docker Playground VM]
            A6[Terraform + Ansible Control Node]
        end

        subgraph Monitoring[Monitoring & Analytics]
            style Monitoring fill:#f2ffe6,stroke:#66cc33
            A4[Prometheus + Grafana VM]
        end

        subgraph Dev[Development Sandbox]
            style Dev fill:#f9e6ff,stroke:#cc33ff
            A7[Linux Dev Environment Sandbox VM]
        end
    end

    %% Mini-PC
    subgraph B[Mini-PC - Proxmox or Debian]
        style B fill:#e6f7ff,stroke:#3399ff,stroke-width:2px
        B1[Pi-hole LXC - Ad Blocking]
        B2[Website Monitor - Uptime Kuma]
        B3[Backup Target for Main PC VMs]
    end

    %% Raspberry Pi Cluster
    subgraph C[Raspberry Pi Cluster (x4)]
        style C fill:#fff0f0,stroke:#cc0000,stroke-width:2px
        C1[Kubernetes Worker Node #1]
        C2[Kubernetes Worker Node #2]
        C3[Kubernetes Worker Node #3]
        C4[Backup Pi-hole or Test Node]
    end

    %% Connections
    A1 --- A2
    A1 --- A4
    A3 --- C1
    A3 --- C2
    A3 --- C3
    A6 --- A3
    A6 --- B1
    A6 --- C1
    A6 --- C2
    A6 --- C3
    A4 --- A
    A4 --- B
    A4 --- C
,,,