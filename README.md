# tempo-docker-compose
Standalone docker setup to create a tempo datasource for Grafana

### Running locally
- setup tempo configuration in `config/tempo.yaml`. 
- setup number of instances targets in `compose.yaml`
- setup listen ports for tempo1 in `compose.yaml`
- start the containers e.g. `podman-compose up -d`

## Architecture
```mermaid
graph LR
    OTELCollector -->|Send traces| tempo
    
    subgraph LokiRead["tempo-instances"]

        tempo -.-> |read/write path| Tempo1
        tempo -.-> |read/write path| Tempo2
        tempo -.-> |read/write path| Tempo3

        Tempo1 -.-> Ingester["ingester"]
        Tempo1 -.-> Querier["query"]
   
        Tempo2 -.-> Ingester["ingester"]
        Tempo2 -.-> Querier["query"]
    
        Tempo3 -.-> Ingester["ingester"]
        Tempo3-.-> Querier["query"]
    end

    subgraph S3["Cloud Storage"]
        Chunks
        Indexes
    end


    Querier --> |reads| Chunks & Indexes
    Ingester --> |writes| Chunks & Indexes
```

Ref : https://github.com/grafana/tempo/tree/main/example/docker-compose