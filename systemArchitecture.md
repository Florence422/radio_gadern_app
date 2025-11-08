# System Architecture - Radio Garden Enhanced

Technical blueprint and architecture documentation

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Frontend Stack](#frontend-stack)
3. [Backend Stack](#backend-stack)
4. [Database Stack](#database-stack)
5. [How Components Communicate](#how-components-communicate)
6. [Why This Approach Is Technically Feasible](#why-this-approach-is-technically-feasible)

---

## System Overview

Radio Garden Enhanced is a full-stack web application built with a modern client-server architecture. The system consists of a React-based frontend, a Node.js backend API server, a Python recommendation microservice and a PostgreSQL database with Redis caching.

The application follows a three-tier architecture pattern:
- Presentation Layer (Frontend)
- Application Layer (Backend APIs)
- Data Layer (Database and Cache)

---


##  Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │          React.js Frontend Application                    │  │
│  │  • Three.js (3D Globe)  • Redux (State Management)       │  │
│  │  • Axios (HTTP Client)  • Tailwind CSS (Styling)         │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │ HTTPS/REST API
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                       API GATEWAY LAYER                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              NGINX Reverse Proxy                          │  │
│  │  • Load Balancing  • SSL Termination  • Rate Limiting    │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │
            ┌────────────┴────────────┐
            ▼                         ▼
┌─────────────────────┐    ┌──────────────────────┐
│   BACKEND SERVICES  │    │  RECOMMENDATION      │
│                     │    │     ENGINE           │
│  Node.js/Express    │◄───┤   Python/Flask       │
│  • Auth API         │    │  • ML Models         │
│  • Station API      │    │  • Trending Algo     │
│  • User API         │    │  • Location Logic    │
│  • Spotify Proxy    │    └──────────────────────┘
└──────────┬──────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────────────┐
│                        DATA LAYER                                │
│  ┌──────────────────┐         ┌──────────────────┐             │
│  │   PostgreSQL     │         │      Redis       │             │
│  │  • User Data     │         │  • Session Cache │             │
│  │  • Stations      │         │  • Station Cache │             │
│  │  • Favorites     │         │  • Rate Limiting │             │
│  └──────────────────┘         └──────────────────┘             │
└─────────────────────────────────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐ │
│  │  Spotify API │  │  Geolocation │  │  Radio Streaming APIs│ │
│  │  • OAuth     │  │  • IP Lookup │  │  • Station Metadata  │ │
│  │  • Track Info│  │  • Coords    │  │  • Stream URLs       │ │
│  └──────────────┘  └──────────────┘  └──────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

---
## Frontend Stack

### Core Technologies

**React 18**
- Purpose: UI framework for building the user interface
- Justification: Component-based architecture enables code reusability and maintainability. The virtual DOM provides excellent performance for dynamic updates when users interact with the globe and station listings.

**Three.js**
- Purpose: 3D rendering library for the interactive globe
- Justification: Industry standard for WebGL-based graphics. Provides robust tools for rendering Earth textures, station markers and smooth rotation controls.

**Redux Toolkit**
- Purpose: State management
- Justification: Centralizes application state including user data, station information, playback status and favorites. Makes state predictable and debuggable.

**Axios**
- Purpose: HTTP client for API communication
- Justification: Clean API for making requests, built-in support for request/response interceptors and automatic JSON transformation.

**Tailwind CSS**
- Purpose: Styling framework
- Justification: Utility-first approach speeds up development. Pre-built responsive utilities ensure the application works seamlessly across devices.

**Vite**
- Purpose: Build tool and development server
- Justification: Fast hot module replacement during development and optimized production builds with code splitting.

### Frontend Architecture

The frontend follows a component-based structure organized into logical modules:

```
src/
├── components/
│   ├── Globe/
│   │   ├── Globe3D.jsx
│   │   ├── StationMarkers.jsx
│   │   └── GlobeControls.jsx
│   ├── Discover/
│   │   ├── DiscoverFeed.jsx
│   │   ├── StationCard.jsx
│   │   └── TrendingList.jsx
│   ├── Player/
│   │   ├── RadioPlayer.jsx
│   │   └── NowPlaying.jsx
│   └── Search/
│       └── SearchBar.jsx
├── services/
│   ├── api.js
│   ├── spotify.js
│   └── geolocation.js
├── store/
│   ├── slices/
│   │   ├── userSlice.js
│   │   ├── stationSlice.js
│   │   └── playerSlice.js
│   └── store.js
└── utils/
    ├── spotifyAuth.js
    └── formatters.js
```

### Key Frontend Features

**3D Globe Visualization**
The globe component uses Three.js to render an interactive Earth model. Users can click and drag to rotate the globe and click on station markers to start playback. The implementation uses OrbitControls for smooth interaction and raycasting for detecting marker clicks.

**Real-time Audio Streaming**
The application uses the HTML5 Audio API to stream live radio. Audio elements are dynamically created and managed through React state to handle playback controls and station switching.

**Responsive Design**
Tailwind breakpoints ensure the interface adapts to different screen sizes. Mobile users can navigate the globe using touch gestures while desktop users benefit from additional screen space for simultaneous browsing.

---

## Backend Stack

### Core Technologies

**Node.js 20**
- Purpose: JavaScript runtime environment
- Justification: Non-blocking I/O model handles concurrent requests efficiently. JavaScript on both frontend and backend reduces context switching for developers.

**Express.js 4**
- Purpose: Web application framework
- Justification: Lightweight and flexible with extensive middleware ecosystem. Simplifies routing, request handling and API development.

**Python 3.11**
- Purpose: Recommendation engine microservice
- Justification: Excellent libraries for data processing and geospatial calculations. NumPy and Pandas enable efficient computation for location-based recommendations.

**Flask**
- Purpose: Python web framework for microservice
- Justification: Lightweight REST API framework that integrates easily with the Node.js backend.

**JWT (JSON Web Tokens)**
- Purpose: Authentication mechanism
- Justification: Stateless authentication works well with single-page applications. Tokens can be easily validated without database lookups on every request.

### Backend Architecture

The backend follows a modular structure with clear separation of concerns:

```
backend/
├── middleware/
│   ├── auth.js
│   ├── rateLimiter.js
│   └── errorHandler.js
├── routes/
│   ├── auth.routes.js
│   ├── station.routes.js
│   ├── user.routes.js
│   └── spotify.routes.js
├── controllers/
│   ├── auth.controller.js
│   ├── station.controller.js
│   ├── user.controller.js
│   └── spotify.controller.js
├── services/
│   ├── auth.service.js
│   ├── station.service.js
│   ├── recommendation.service.js
│   └── spotify.service.js
└── models/
    ├── User.js
    ├── Station.js
    └── Favorite.js
```

### API Endpoints

**Authentication**
- POST /api/auth/register - Create new user account
- POST /api/auth/login - Authenticate user and return JWT
- POST /api/auth/refresh - Refresh expired token
- GET /api/auth/spotify/callback - Handle Spotify OAuth callback

**Stations**
- GET /api/stations - Retrieve all stations with pagination
- GET /api/stations/:id - Get specific station details
- GET /api/stations/nearby - Get stations near user location
- GET /api/stations/trending - Get currently trending stations
- GET /api/stations/search - Search stations by country, genre or language

**User**
- GET /api/user/profile - Retrieve user profile information
- PUT /api/user/profile - Update user profile
- GET /api/user/favorites - Get user's favorite stations
- POST /api/user/favorites/:id - Add station to favorites
- DELETE /api/user/favorites/:id - Remove station from favorites

**Spotify Integration**
- GET /api/spotify/track/:id - Retrieve track information
- POST /api/spotify/save-track - Save track to user's Spotify library
- GET /api/spotify/current-song - Identify currently playing song

### Recommendation Engine

The Python microservice handles computational tasks for personalized recommendations:

```python
recommendation_service/
├── app.py
├── models/
│   └── recommender.py
├── utils/
│   ├── location.py
│   └── trending.py
└── requirements.txt
```

The recommendation algorithm combines three factors:
1. Geographic proximity (Haversine distance calculation)
2. Trending score (weighted by recent listen counts)
3. User preference matching (genre and language preferences)

---

## Database Stack

### PostgreSQL Database

PostgreSQL serves as the primary relational database storing structured data.

**Users Table**
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    username VARCHAR(100) NOT NULL,
    spotify_id VARCHAR(255),
    spotify_access_token TEXT,
    spotify_refresh_token TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Stations Table**
```sql
CREATE TABLE stations (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    country VARCHAR(100) NOT NULL,
    city VARCHAR(100),
    latitude DECIMAL(10, 8) NOT NULL,
    longitude DECIMAL(11, 8) NOT NULL,
    stream_url TEXT NOT NULL,
    genre VARCHAR(100),
    language VARCHAR(50),
    description TEXT,
    website_url TEXT,
    listen_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**Favorites Table**
```sql
CREATE TABLE favorites (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    station_id INTEGER REFERENCES stations(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, station_id)
);
```

**Listening History Table**
```sql
CREATE TABLE listening_history (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    station_id INTEGER REFERENCES stations(id) ON DELETE CASCADE,
    listened_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    duration_seconds INTEGER
);
```

**Database Indexes**
```sql
CREATE INDEX idx_stations_location ON stations(latitude, longitude);
CREATE INDEX idx_stations_country ON stations(country);
CREATE INDEX idx_favorites_user ON favorites(user_id);
CREATE INDEX idx_listening_history_station ON listening_history(station_id, listened_at);
```

These indexes optimize common query patterns including geospatial searches, country-based filtering and user-specific queries.

### Redis Cache

Redis provides in-memory caching to reduce database load and improve response times.

**Cache Structure**

| Key Pattern | Value Type | TTL | Purpose |
|-------------|------------|-----|---------|
| user:session:{userId} | String | 24h | User session data |
| station:data:{stationId} | Hash | 1h | Station metadata cache |
| trending:global | List | 30min | Global trending stations |
| nearby:{lat}:{lon} | List | 1h | Cached location queries |
| ratelimit:{ip}:{endpoint} | Counter | 1min | Rate limiting counters |
| spotify:token:{userId} | String | 50min | Cached Spotify access tokens |

Redis reduces repeated database queries for frequently accessed data and enables fast session validation without hitting the database on every request.

---

## How Components Communicate

### Client to Backend Communication

The frontend communicates with the backend through RESTful HTTP requests over HTTPS.

**Request Flow Example: Discovering Nearby Stations**

1. User opens the application
2. Frontend requests geolocation via browser Geolocation API
3. Frontend sends GET request to /api/stations/nearby with latitude and longitude parameters
4. Backend receives request and validates JWT token in Authorization header
5. Backend checks Redis cache for nearby:{lat}:{lon} key
6. If cache miss, backend calls Python recommendation service
7. Recommendation service queries PostgreSQL for stations within radius
8. Results are ranked and returned to Node.js backend
9. Backend caches results in Redis with 1-hour expiration
10. Backend sends JSON response to frontend
11. Frontend renders station cards in the Discover feed

**Authentication Flow**

1. User clicks "Connect Spotify" button
2. Frontend redirects to Spotify OAuth authorization URL
3. User authorizes application on Spotify
4. Spotify redirects to /api/auth/spotify/callback with authorization code
5. Backend exchanges code for access token and refresh token
6. Backend stores tokens in PostgreSQL (encrypted)
7. Backend generates JWT containing user ID
8. Backend stores session in Redis
9. Backend redirects to frontend with JWT in URL parameter
10. Frontend stores JWT in localStorage
11. Frontend includes JWT in Authorization header for subsequent requests


---

## Why This Approach Is Technically Feasible

### Proven Technology Stack

All technologies used in this architecture are mature, well-documented and battle-tested in production environments.

**React** powers millions of web applications including Facebook, Netflix and Airbnb. Its component model and virtual DOM have proven effective for building complex interactive interfaces.

**Three.js** is the de facto standard for WebGL applications with over 90,000 GitHub stars and extensive documentation. Numerous projects have successfully implemented 3D globes including the original Radio Garden.

**Node.js** handles millions of concurrent connections in production environments. Companies like LinkedIn, Netflix and Uber rely on Node.js for their backend services.

**PostgreSQL** is a proven database system with 30+ years of active development. It handles billions of rows and petabytes of data in production systems worldwide.

### Realistic Scope and Implementation

The core functionality builds on existing, accessible technologies and APIs.

**Radio Station Data** is freely available through the Radio Browser API, a community-maintained database of over 30,000 stations worldwide. The API requires no authentication and has generous rate limits.

**Spotify Integration** is straightforward using their well-documented Web API. The OAuth flow is standard and Spotify provides official SDKs and detailed guides. Rate limits (1,000 requests per user per hour) are sufficient for typical usage patterns.

**Geolocation** is built into modern browsers through the Geolocation API. For fallback scenarios, free IP-based geolocation services provide adequate accuracy for country and city-level recommendations.

### Scalable Architecture

The system architecture supports growth without requiring fundamental redesign.

**Stateless Backend** means any server can handle any request. JWT authentication eliminates session affinity requirements enabling horizontal scaling behind a load balancer.

**Caching Strategy** reduces database load significantly. Frequently accessed data (trending stations, station metadata) is cached in Redis. Cache invalidation is straightforward with time-based expiration.

**Database Optimization** includes proper indexing on frequently queried columns (latitude/longitude for geospatial queries, user_id for user-specific queries). PostgreSQL's query planner efficiently handles these indexed queries even with millions of records.

**Microservice Architecture** allows independent scaling of components. If recommendation processing becomes a bottleneck, additional Python service instances can be deployed without affecting the main API server.

### Performance Considerations

The application is designed to deliver fast response times under realistic load.

**Frontend Performance**
- Code splitting loads only necessary JavaScript initially
- Three.js Level of Detail (LOD) reduces rendering complexity for distant objects
- Station markers use instanced rendering for efficient GPU utilization
- Images and textures are optimized and served from CDN

**Backend Performance**
- Redis caching reduces database queries by 70-80% for common operations
- Connection pooling eliminates connection overhead
- Pagination limits response sizes (50 stations per page)
- Response compression (gzip) reduces bandwidth usage

**Database Performance**
- Geospatial queries use PostGIS extensions for efficient radius searches
- Indexed queries return results in milliseconds even with 100,000+ stations
- Connection pooling supports 20 concurrent database operations

**Expected Performance Metrics**
- Initial page load: < 3 seconds
- API response time: < 200ms (cached), < 500ms (uncached)
- Globe rendering: 60 FPS on desktop, 30 FPS on mobile
- Station switching: < 1 second including stream buffering

### Cost Effectiveness

The technology choices minimize infrastructure costs especially during initial deployment.

**Free Tier Availability**
- Vercel/Netlify provide free hosting for frontend applications
- Railway/Render offer free tiers for backend services
- PostgreSQL and Redis are open-source with no licensing fees
- Radio Browser API is completely free
- Spotify API requires no subscription for basic features

**Scaling Costs**
- Horizontal scaling of stateless backend is cost-effective
- Database can handle significant load on modest hardware
- Redis memory usage is predictable and manageable
- CDN costs are minimal for static assets
