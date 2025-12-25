graph TD
    %% 样式定义
    classDef front fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef business fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef algo fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef db fill:#e0f2f1,stroke:#00695c,stroke-width:2px;
    classDef redis fill:#ffebee,stroke:#c62828,stroke-width:2px;

    %% 第一层：前端层
    subgraph Frontend["前端层（Vue + Element-Plus）"]
        F1[食材录入页]:::front
        F2[推荐结果页]:::front
        F3[偏好设置页]:::front
    end

    %% 第二层：后端与算法层
    subgraph Backend["后端服务层"]
        B1[用户/权限管理]:::business
        B2[食谱基础服务]:::business
        B3[业务逻辑编排]:::business
        A1[食材筛选算法]:::algo
        A2[协同过滤推荐算法]:::algo
    end

    %% 第三层：数据层
    subgraph DataLayer["数据支撑层"]
        D1[(用户信息表)]:::db
        D2[(食谱数据表)]:::db
        D3[(用户行为日志)]:::db
        R1[(Redis 倒排索引)]:::redis
        R2[(热门食谱缓存)]:::redis
    end

    %% 连接关系
    F1 --> B3
    F2 --> B3
    F3 --> B3

    B3 --> B1
    B3 --> B2
    B3 --> A1
    B3 --> A2

    B1 --> D1
    B2 --> D2
    A2 --> D3
    A1 --> R1
    A2 --> R2
