# tempo-docker-compose
Standalone docker setup to create a tempo datasource for Grafana

### Running locally
- setup tempo configuration in `config/loki.yaml`. Important ones are 
- setup number of instances targets in `compose.yaml`
- setup listen ports for tempo1 in `compose.yaml`
- start the containers e.g. `podman-compose up -d`


## Architecture
```mermaid
graph LR
    OTELCollector -->|Send traces| tempo

    subgraph Tempo["tempo-gateway"]
        tempo
    end 
    
    tempo -.-> |read/write path| Tempo1
    tempo -.-> |read/write path| Tempo2
    tempo -.-> |read/write path| Tempo3
    
    subgraph LokiRead["loki -target=read (3)"]
        Tempo1 -.-> Ingester["ingester"]
        Tempo1 -.-> Querier["query"]
    end
    
    subgraph LokiRead["loki -target=read (3)"]
        Tempo2 -.-> Ingester["ingester"]
        Tempo2 -.-> Querier["query"]
    end
    
    subgraph LokiRead["loki -target=read (3)"]
        Tempo3 -.-> Ingester["ingester"]
        Tempo3-.-> Querier["query"]
    end
    subgraph Minio["Cloud Storage"]
        Chunks
        Indexes
    end


    Querier --> |reads| Chunks & Indexes
    Ingester --> |writes| Chunks & Indexes
```

Ref : https://github.com/grafana/loki/blob/main/production/docker/README.md