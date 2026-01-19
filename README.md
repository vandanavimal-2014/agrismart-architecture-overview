# AgriSmart - Smart Farming Management App 🌾

A full-stack React Native (Expo) mobile application with Spring Boot backend for managing farms, crops, tasks, weather, and market prices.

## Architecture

- **Frontend**: React Native with Expo Router, NativeWind (Tailwind CSS), TypeScript
- **Backend**: Spring Boot 3.4.1 (Java 17), JPA/Hibernate, MySQL, Flyway migrations
- **Infrastructure**: Docker, Docker Compose, AWS ECS/ECR for deployment
- **CI/CD**: GitHub Actions with Checkstyle, Maven tests, Docker builds, AWS deployment

## Quick Start

### Frontend Setup

1. Install dependencies:
   ```bash
   npm install
   ```

2. Configure API URL (create `.env` file):
   ```bash
   # For local development
   EXPO_PUBLIC_API_URL=http://localhost:8080/api
   
   # For Android emulator
   EXPO_PUBLIC_API_URL=http://10.0.2.2:8080/api
   
   # For AWS production
   EXPO_PUBLIC_API_URL=https://your-alb-dns-name.us-east-1.elb.amazonaws.com/api
   ```

3. Start the app:
   ```bash
   npx expo start
   ```

### Backend Setup

1. Navigate to backend directory:
   ```bash
   cd backend
   ```

2. Start with Docker Compose:
   ```bash
   cd ..
   docker-compose up --build
   ```

   This starts:
   - MySQL database on port 3306
   - Spring Boot backend on port 8080

3. Or run locally:
   ```bash
   cd backend
   mvn spring-boot:run
   ```

   Make sure MySQL is running locally with:
   - Database: `agrismart`
   - Username: `agrismart`
   - Password: `agrismart`

## Environment Configuration

### Frontend Environment Variables

Create a `.env` file in the root directory:

```env
# API Base URL
# Local: http://localhost:8080/api
# Android Emulator: http://10.0.2.2:8080/api
# Physical Device: http://YOUR_COMPUTER_IP:8080/api
# AWS Production: https://your-alb-dns-name.us-east-1.elb.amazonaws.com/api
EXPO_PUBLIC_API_URL=http://localhost:8080/api
```

### Backend Environment Variables

The backend uses Spring Boot's `application.yml` with environment variable overrides:

```bash
# Database Configuration
SPRING_DATASOURCE_URL=jdbc:mysql://localhost:3306/agrismart?useSSL=false
SPRING_DATASOURCE_USERNAME=agrismart_db_username
SPRING_DATASOURCE_PASSWORD=agrismart_db_password
```

## AWS Deployment

### Prerequisites

1. AWS Account with appropriate permissions
2. GitHub Secrets configured (see below)
3. AWS CLI installed and configured

### GitHub Secrets Required

Configure these secrets in your GitHub repository settings:

- `AWS_ACCESS_KEY_ID`: AWS access key
- `AWS_SECRET_ACCESS_KEY`: AWS secret key
- `AWS_REGION`: AWS region (e.g., `us-east-1`)
- `AWS_ECS_CLUSTER`: ECS cluster name
- `AWS_ECS_SERVICE`: ECS service name
- `AWS_ECS_TASK_DEFINITION`: Task definition family name
- `DB_PASSWORD`: Database password
- `MYSQL_ROOT_PASSWORD`: MySQL root password

### Deployment Steps

1. **Create AWS Resources** (see `backend/aws-deployment-guide.md` for detailed steps):
   - ECR repository
   - RDS MySQL database
   - ECS cluster
   - Application Load Balancer
   - IAM roles

2. **Push to main branch**: The GitHub Actions workflow will automatically:
   - Build and test the backend
   - Run Checkstyle code quality checks
   - Build Docker image
   - Push to ECR
   - Deploy to ECS

3. **Update Frontend API URL**: Set `EXPO_PUBLIC_API_URL` to your ALB DNS name

See `backend/aws-deployment-guide.md` for complete deployment instructions.

## API Endpoints

All endpoints are prefixed with `/api` (except `/auth`):

### Authentication
- `POST /auth/register` - Register new user
- `POST /auth/login` - Login and get JWT token

### User Profile
- `GET /api/users/me` - Get current user profile
- `PUT /api/users/me` - Update current user profile

### Farms
- `GET /api/farms` - Get all farms for current user
- `GET /api/farms/{id}` - Get farm by ID
- `POST /api/farms` - Create new farm
- `PUT /api/farms/{id}` - Update farm
- `DELETE /api/farms/{id}` - Delete farm

### Crops
- `GET /api/crops` - Get all crops (with optional `?category=` and `?q=` filters)
- `GET /api/crops/{id}` - Get crop by ID
- `POST /api/crops` - Create new crop
- `PUT /api/crops/{id}` - Update crop
- `DELETE /api/crops/{id}` - Delete crop

### Tasks
- `GET /api/tasks` - Get all tasks (with optional `?status=` filter)
- `GET /api/tasks/{id}` - Get task by ID
- `POST /api/tasks` - Create new task
- `PUT /api/tasks/{id}` - Update task
- `DELETE /api/tasks/{id}` - Delete task

### Dashboard
- `GET /api/dashboard/summary?period=MONTH` - Get overall summary (period: MONTH, QUARTER, YEAR)
- `GET /api/dashboard/farm/{farmId}?period=MONTH` - Get farm-specific summary

### Weather Integration
- `GET /api/weather/farm/{farmId}` - Get current weather for farm

### Market Prices
- `GET /api/market/price/{cropId}` - Get latest price for crop (from integration API)
- `GET /api/market-prices` - Get all market prices for current user
- `POST /api/market-prices` - Create market price entry
- `GET /api/market-prices/{id}` - Get market price by ID
- `PUT /api/market-prices/{id}` - Update market price
- `DELETE /api/market-prices/{id}` - Delete market price

### Media Files (S3)
- `POST /api/media/upload` - Upload file (multipart/form-data: entityType, entityId, file)
- `GET /api/media/{id}` - Get media file metadata
- `GET /api/media/{id}/view` - Get presigned URL for viewing
- `DELETE /api/media/{id}` - Delete media file

### Disease Detection
- `POST /api/scan/analyze` - Analyze image for disease detection

### Notifications
- `GET /api/notifications` - Get all notifications for current user
- `GET /api/notifications/{id}` - Get notification by ID
- `PATCH /api/notifications/{id}/read` - Mark notification as read
- `POST /api/notifications` - Create notification
- `DELETE /api/notifications/{id}` - Delete notification

### Error Handling

All errors follow a consistent format:
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "status": 400,
  "errorCode": "AGR-VAL-001",
  "error": "Validation Error",
  "message": "Crop name must not be empty",
  "path": "/api/crops",
  "requestId": "550e8400-e29b-41d4-a716-446655440000",
  "fieldErrors": {
    "name": "must not be empty"
  }
}
```

Error codes:
- `AGR-VAL-xxx` - Validation errors (400)
- `AGR-AUTH-xxx` - Authentication/Authorization errors (401, 403)
- `AGR-NOTF-xxx` - Resource not found (404)
- `AGR-CONF-xxx` - Conflict/duplicate (409)
- `AGR-SYS-xxx` - Internal server errors (500)

## Development

### Running Tests

```bash
cd backend
mvn test
```

### Code Quality

```bash
cd backend
mvn checkstyle:check
```

### Database Migrations

Flyway automatically runs migrations on startup. Migration files are in:
```
backend/src/main/resources/db/migration/
```

To create a new migration:
1. Create file: `V2__description.sql`
2. Add SQL statements
3. Flyway will apply it on next startup

## Project Structure

```
agrismart/
├── app/                    # React Native screens (Expo Router)
├── components/             # Reusable React components
├── constants/              # Constants (API URLs, etc.)
├── backend/                # Spring Boot backend
│   ├── src/main/java/      # Java source code
│   ├── src/main/resources/ # Configuration & migrations
│   └── pom.xml             # Maven dependencies
├── .github/workflows/      # CI/CD pipelines
├── docker-compose.yml      # Local development setup
└── README.md
```

## Features

### Core Features
- ✅ **Farm Management** - CRUD operations for farms with location, area, soil type tracking
- ✅ **Crop Management** - Crop library with search, filtering by category, scientific names
- ✅ **Task Management** - Task creation, status tracking (pending/completed), priority levels
- ✅ **User Profile** - User registration, authentication (JWT), profile management

### Analytics & Reporting
- ✅ **Dashboard** - Overall and farm-level analytics with profit/loss, sales, costs
- ✅ **Time Series Analysis** - Monthly, quarterly, yearly aggregations
- ✅ **Farm Performance** - Top performing farms, crop-wise sales analysis

### Integrations
- ✅ **Weather Integration** - Real-time weather data for farms (OpenWeather API)
- ✅ **Market Price Integration** - Latest crop prices from Agmarknet API
- ✅ **AI Disease Detection** - Plant.id API integration for crop disease detection

### Media Management
- ✅ **S3 File Upload** - Secure file uploads to AWS S3
- ✅ **Presigned URLs** - Secure, time-limited access to media files
- ✅ **Media Metadata** - Track photos for farms, crops, disease scans

### Notifications & Audit
- ✅ **Notification System** - Event-driven notifications (task status, disease detected, harvest completed)
- ✅ **Audit Logging** - Complete audit trail of all data changes with before/after snapshots
- ✅ **Notification Log** - Event store for notification delivery tracking

### Security & Error Handling
- ✅ **JWT Authentication** - Secure token-based authentication
- ✅ **User Isolation** - All data scoped to authenticated user
- ✅ **Standardized Error Handling** - Consistent error responses with error codes
- ✅ **Request ID Tracking** - Unique request IDs for debugging and traceability
- ✅ **Field-Level Validation** - Detailed validation error messages

### Frontend Features (React Native/Expo)
- ✅ **Dashboard UI** - Farm analytics dashboard with charts and metrics
- ✅ **Error Handling** - Centralized error handling with user-friendly messages
- ✅ **Offline Support** - React Query caching and offline fallback
- ✅ **Pull-to-Refresh** - Data refresh functionality
- ✅ **Responsive UI** - Modern UI with NativeWind (Tailwind CSS)

## Documentation

- **[API Reference](API_REFERENCE.md)** - Complete API endpoint documentation with examples
- **[Error Handling Guide](ERROR_HANDLING_GUIDE.md)** - Complete guide to error handling system
- **[Integration APIs Guide](backend/INTEGRATION_APIS.md)** - Weather, Market, Disease Detection APIs
- **[Notification Microservice Guide](backend/NOTIFICATION_MICROSERVICE.md)** - Notification service integration
- **[Postman Collection](AgriSmart.postman_collection.json)** - Complete API test collection (import into Postman)

## Testing

### Postman Collection

Import `AgriSmart.postman_collection.json` into Postman to test all endpoints:

1. Set `baseUrl` variable (default: `http://localhost:8080`)
2. Run "Login" request to get JWT token (auto-saved to `token` variable)
3. All other requests will use the token automatically

The collection includes:
- All CRUD operations for all resources
- Error handling test cases
- Request ID validation tests
- Authentication flow tests

### Backend Tests

```bash
cd backend
mvn test
```

### Frontend Tests

```bash
npm test
```

## Learn More

- [Expo Documentation](https://docs.expo.dev/)
- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [AWS ECS Documentation](https://docs.aws.amazon.com/ecs/)
- [React Query Documentation](https://tanstack.com/query/latest)
- [NativeWind Documentation](https://www.nativewind.dev/)

