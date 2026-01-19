🌾 AgriSmart Architecture

flowchart TB

%% ================= AGRO STYLES =================
classDef mobile fill:#E8F5E9,stroke:#2E7D32,color:#1B5E20;
classDef api fill:#F1F8E9,stroke:#558B2F,color:#33691E;
classDef service fill:#FFFDE7,stroke:#9E9D24,color:#827717;
classDef repo fill:#EFEBE9,stroke:#6D4C41,color:#4E342E;
classDef db fill:#E3F2FD,stroke:#1565C0,color:#0D47A1;
classDef external fill:#F3E5F5,stroke:#6A1B9A,color:#4A148C;
classDef infra fill:#ECEFF1,stroke:#455A64,color:#263238;

%% ================= MOBILE =================
subgraph Mobile["📱 Mobile App (React Native + Expo)"]
    UI["Screens & Tabs\nDashboard | Farms | Tasks\nWeather | Market | Scan | Profile"]
    Hooks["Hooks & Lib\nuseDashboard | api.ts | auth.ts\nCentral Error Handling"]
    UI --> Hooks
end
class UI,Hooks mobile

%% ================= BACKEND =================
subgraph Backend["⚙️ Spring Boot Backend (Java 17)"]

    %% API Layer
    subgraph API["REST API Layer"]
        AuthC["AuthController"]
        FarmC["FarmController"]
        CropC["CropController"]
        TaskC["TaskController"]
        SaleC["SaleController"]
        CostC["CostController"]
        DashC["DashboardController"]
        ScanC["ScanController"]
        WeatherC["WeatherController"]
        MarketC["MarketController"]
        MediaC["MediaController"]
        NotifC["NotificationController"]
    end
    class AuthC,FarmC,CropC,TaskC,SaleC,CostC,DashC,ScanC,WeatherC,MarketC,MediaC,NotifC api

    %% Service Layer
    subgraph Service["Service Layer"]
        AuthS["AuthService"]
        FarmS["FarmService"]
        CropS["CropService"]
        TaskS["TaskService"]
        SaleS["SaleService"]
        CostS["CostService"]
        DashS["DashboardService\n(Aggregations)"]
        ScanS["DiseaseDetectionService\n(Strategy Pattern)"]
        WeatherS["WeatherIntegrationService"]
        MarketS["MarketPriceIntegrationService"]
        MediaS["MediaService"]
        NotifS["NotificationService"]
        HistoryS["HistoryService"]
        AuditS["AuditLogger"]
    end
    class AuthS,FarmS,CropS,TaskS,SaleS,CostS,DashS,ScanS,WeatherS,MarketS,MediaS,NotifS,HistoryS,AuditS service

    %% Persistence Layer
    subgraph Repo["Persistence Layer (JPA / Hibernate)"]
        FarmR["FarmRepository"]
        CropR["CropRepository"]
        TaskR["TaskRepository"]
        SaleR["SaleRepository"]
        CostR["CostRepository"]
        DashR["DashboardRepository"]
        HistoryR["History Repositories"]
        AuditR["AuditLogRepository"]
        NotifR["NotificationLogRepository"]
        MediaR["MediaFileRepository"]
    end
    class FarmR,CropR,TaskR,SaleR,CostR,DashR,HistoryR,AuditR,NotifR,MediaR repo
end

%% ================= DATABASE =================
subgraph DB["🗄️ MySQL Database"]
    CoreTables["Core Tables\nFarm | Crop | Task | Sale | Cost"]
    HistoryTables["History Tables"]
    SnapshotTables["Snapshots\nWeather | Market"]
    AuditTables["Audit & Notification Logs"]
end
class CoreTables,HistoryTables,SnapshotTables,AuditTables db

%% ================= EXTERNAL =================
subgraph External["🌐 External Integrations"]
    WeatherAPI["Weather API"]
    MarketAPI["Market Price API"]
    AIAPI["AI Disease Detection\nPlantId / Custom ML"]
end
class WeatherAPI,MarketAPI,AIAPI external

%% ================= INFRA =================
subgraph Infra["☁️ AWS Infrastructure"]
    S3["S3 (Media Storage)"]
    ECS["ECS + ECR"]
    CI["GitHub Actions CI/CD"]
end
class S3,ECS,CI infra

%% ================= CONNECTIONS =================
Mobile -->|HTTPS + JWT| API
API --> Service
Service --> Repo
Repo --> DB

ScanS --> AIAPI
WeatherS --> WeatherAPI
MarketS --> MarketAPI
MediaS --> S3
Backend --> ECS
CI --> ECS
