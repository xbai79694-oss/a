# 个性化食谱推荐系统

## 1. 系统功能结构图
```mermaid
mindmap
  root((个性化食谱推荐系统))
    平台基础能力
      用户管理
        注册登录
        口味标签初始化
        饮食偏好档案
      权限与角色管理
        RBAC角色权限映射
        功能访问控制
      系统日志
        用户行为日志
        系统运行日志
    业务处理能力
      食谱服务
        分类检索
        食谱详情
        食谱收藏
      食材筛选服务
        Redis倒排索引构建
        多食材组合检索
        缺失度降级匹配
      协同过滤推荐
        Item-based CF算法
        食材相似度补充
        冷启动热度过渡
      结果展示与反馈
        推荐食谱排序展示
        食材缺口提示
        用户评分反馈提交
```

## 2. 技术架构图
```mermaid
graph TD
    %% 样式定义
    classDef front fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef business fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef algo fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef db fill:#e0f2f1,stroke:#00695c,stroke-width:2px;
    classDef redis fill:#ffebee,stroke:#c62828,stroke-width:2px;

    %% 第一层：前端层
    subgraph Frontend [第一层：前端层 (Vue.js + Element-Plus)]
        direction TB
        F1[食材录入页]:::front
        F2[推荐结果页]:::front
        F3[个人偏好设置页]:::front
    end

    %% 第二层：后端服务层
    subgraph Backend [第二层：后端服务层]
        direction TB
        subgraph JavaService [后端业务服务 (Java Spring Boot)]
            B1[用户/权限管理]:::business
            B2[食谱基础服务]:::business
            B3[业务逻辑编排]:::business
        end
        
        subgraph PyService [算法服务 (Python Flask)]
            A1[食材筛选逻辑]:::algo
            A2[协同过滤CF算法]:::algo
            A3[相似度计算引擎]:::algo
        end
    end

    %% 第三层：数据支撑层
    subgraph DataLayer [第三层：数据支撑层]
        direction TB
        subgraph DB [数据层 (MySQL)]
            D1[(用户基础信息)]:::db
            D2[(标准化食谱数据)]:::db
            D3[(用户行为日志)]:::db
        end
        
        subgraph Cache [缓存层 (Redis)]
            R1[(倒排索引数据)]:::redis
            R2[(计算中间结果)]:::redis
            R3[(热门食谱缓存)]:::redis
        end
    end

    %% 交互关系连线
    F1 & F2 & F3 -- HTTP请求 --> B3
    
    B3 -- 调用基础数据 --> B1 & B2
    B3 -- RPC/HTTP算法调用 --> A1 & A2
    
    B1 & B2 -- CRUD操作 --> D1 & D2 & D3
    A1 -- 高速查询 --> R1 & R2
    A2 -- 读取行为数据 --> D3
    A3 -- 写入/读取 --> R3
    
    %% 补充连线
    A2 -. 读取基础数据 .-> D2
```
