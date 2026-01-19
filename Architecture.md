🏗️ AgriSmart – System Architecture (Mermaid)
flowchart TB
    %% ===== Mobile App =====
    subgraph Mobile["📱 Mobile App (React Native + Expo)"]
        UI["Screens & Tabs\nDashboard | Farms | Tasks | Weather\nMarket | Scan | Profile"]
        Hooks["Hooks & Lib\nuseDashboard | api.ts | auth.ts\nCentral Error Handling"]
        UI --> Hooks
    end

    %% ===== Backend =====
    subgraph Backend["⚙️ Spring Boot Backend (Java 17)"]
        direction TB

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

        %% Persistence Layer
        subgraph Repo["Persistence Layer (JPA)"]
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
    end

    %% ===== Database =====
    subgraph DB["🗄️ MySQL Database"]
        Tables["Core Tables\nFarm | Crop | Task | Sale | Cost"]
        HistoryTables["History Tables"]
        SnapshotTables["Snapshots\nWeather | Market"]
        AuditTables["Audit & Notification Logs"]
    end

    %% ===== External Services =====
    subgraph External["🌐 External Integrations"]
        WeatherAPI["Weather API"]
        MarketAPI["Market Price API"]
        AIAPI["AI Disease Detection\n(PlantId / Custom ML)"]
    end

    %% ===== Infrastructure =====
    subgraph Infra["☁️ AWS Infrastructure"]
        S3["S3 (Media Storage)"]
        ECS["ECS + ECR"]
        Docker["Docker"]
        CI["GitHub Actions CI/CD"]
    end

    %% ===== Connections =====
    Mobile -->|HTTPS + JWT| API

    API --> Service
    Service --> Repo
    Repo --> DB

    WeatherS --> WeatherAPI
    MarketS --> MarketAPI
    ScanS --> AIAPI

    MediaS --> S3
    Backend --> ECS
    CI --> Docker
