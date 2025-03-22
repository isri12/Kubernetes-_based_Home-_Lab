graph TB
    %% Main Components
    Internet[Internet]
    Router[Home Router]
    
    Laptop[Used Laptop]
    subgraph "Laptop Services"
        K8s[Kubernetes - K3s]
        Monitoring[Prometheus & Grafana]
        Media[Plex/Jellyfin]
    end
    
    PiHole[Raspberry Pi - Pi-hole]
    HomeAssist[Raspberry Pi - Home Assistant]
    OtherPi[Raspberry Pi - Future Use]
    
    Devices[Client Devices<br>TV, Phones, Laptops]
    
    %% Connections
    Internet --- Router
    Router --- Laptop
    Router --- PiHole
    Router --- HomeAssist
    Router --- OtherPi
    Router --- Devices
    
    %% Service Data Flows
    PiHole -.DNS Filtering.-> Router
    Laptop -.Media Streaming.-> Devices
    HomeAssist -.Smart Home Control.-> Devices
    
    %% Styling
    classDef laptop fill:#326CE5,color:white,stroke-width:2px;
    classDef raspberry fill:#c42525,color:white,stroke-width:2px;
    classDef network fill:#3498db,color:white,stroke-width:2px;
    classDef client fill:#f39c12,color:white,stroke-width:2px;
    
    class Laptop,K8s,Monitoring,Media laptop;
    class PiHole,HomeAssist,OtherPi raspberry;
    class Internet,Router network;
    class Devices client;
