### KV Pool 部署图

``` mermaid
flowchart TD
    %% 全局美化样式
    classDef client fill:#e6f7ff,stroke:#1890ff,stroke-width:2px
    classDef master fill:#fff7e6,stroke:#fa8c16,stroke-width:2px
    classDef store fill:#f0f2ff,stroke:#5856d6,stroke-width:2px
    classDef cann fill:#f9f0ff,stroke:#722ed1,stroke-width:2px
    classDef subgraphStyle fill:#fafafa,stroke:#999,stroke-width:1px

    %% RL 客户端层
    subgraph RL ["🔹 RL Framework"]
        direction LR
        RL1[RL Node 1<br/>KV Cache]
        RL2[RL Node 2<br/>KV Cache]
        RL3[RL Node 3<br/>KV Cache]
        RLN[RL Node N<br/>KV Cache]
    end

    %% 中心化控制层
    subgraph MC ["🔸 Mooncake Master"]
        Master[Mooncake Master<br/>segment manage + metadata]
    end

    %% 存储节点层
    subgraph ST ["🔹 Mooncake Store Nodes"]
        direction LR
        Store1[Mooncake Store 1<br/>Transfer Engine<br/>ascend direct transport]
        Store2[Mooncake Store 2<br/>Transfer Engine<br/>ascend direct transport]
        Store3[Mooncake Store 3<br/>Transfer Engine<br/>ascend direct transport]
        StoreN[Mooncake Store N<br/>Transfer Engine<br/>ascend direct transport]
    end

    %% 硬件加速层（Full Mesh）
    subgraph HW ["🔸 CANN Full Mesh"]
        direction LR
        CANN1[CANN Node 1<br/>HIXL + HCOMM]
        CANN2[CANN Node 2<br/>HIXL + HCOMM]
        CANN3[CANN Node 3<br/>HIXL + HCOMM]
        CANNN[CANN Node N<br/>HIXL + HCOMM]
    end

    %% 业务连接
    RL1 -->|Put/Get/Remove| Store1
    RL2 -->|Put/Get/Remove| Store2
    RL3 -->|Put/Get/Remove| Store3
    RLN -->|Put/Get/Remove| StoreN

    Master <-->|RPC + metadata| Store1
    Master <-->|RPC + metadata| Store2
    Master <-->|RPC + metadata| Store3
    Master <-->|RPC + metadata| StoreN

    Store1 -->|One Side Transfer| CANN1
    Store2 -->|One Side Transfer| CANN2
    Store3 -->|One Side Transfer| CANN3
    StoreN -->|One Side Transfer| CANNN

    %% CANN Full Mesh 全互联
    CANN1 <-->|Full Mesh| CANN2
    CANN1 <-->|Full Mesh| CANN3
    CANN1 <-->|Full Mesh| CANNN

    CANN2 <-->|Full Mesh| CANN3
    CANN2 <-->|Full Mesh| CANNN

    CANN3 <-->|Full Mesh| CANNN

    %% 绑定颜色
    class RL1,RL2,RL3,RLN client
    class Master master
    class Store1,Store2,Store3,StoreN store
    class CANN1,CANN2,CANN3,CANNN cann
```

