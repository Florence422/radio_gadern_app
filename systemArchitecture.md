# 🏗️ System Architecture - Radio Garden Enhanced

> Technical blueprint and architecture documentation

---

## 📋 Table of Contents

1. [System Overview](#system-overview)
2. [Architecture Diagram](#architecture-diagram)
3. [Technology Stack](#technology-stack)
4. [Frontend Architecture](#frontend-architecture)
5. [Backend Architecture](#backend-architecture)
6. [Database Design](#database-design)
7. [External Integrations](#external-integrations)
8. [Data Flow](#data-flow)
9. [Security & Authentication](#security--authentication)
10. [Scalability & Performance](#scalability--performance)
11. [Deployment Strategy](#deployment-strategy)
12. [Technical Feasibility](#technical-feasibility)

---

## 🎯 System Overview

Radio Garden Enhanced is a full-stack web application built with a modern client-server architecture. The system consists of:

- **Frontend Client**: React-based SPA (Single Page Application)
- **Backend API Server**: Node.js/Express REST API
- **Recommendation Engine**: Python-based microservice
- **Database Layer**: PostgreSQL + Redis
- **External Services**: Spotify API, Geolocation API, Radio Streaming Services

**Architecture Pattern**: Microservices with RESTful APIs  
**Communication Protocol**: HTTP/HTTPS, WebSockets for live streaming  
**Deployment Model**: Cloud-native (containerized with Docker)

---

## 🗺️ Architecture Diagram

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

## 🛠️ Technology Stack

### Frontend Stack

| Technology | Purpose | Justification |
|------------|---------|---------------|
| **React 18** | UI Framework | Component reusability, virtual DOM performance, large ecosystem |
| **Three.js** | 3D Globe Rendering | Industry standard for WebGL, excellent docs, active community |
| **Redux Toolkit** | State Management | Centralized state for stations, user data, and playback |
| **Axios** | HTTP Client | Clean API, interceptors for auth, error handling |
| **Tailwind CSS** | Styling | Utility-first, responsive design, fast development |
| **Vite** | Build Tool | Fast HMR, optimized production builds |

### Backend Stack

| Technology | Purpose | Justification |
|------------|---------|---------------|
| **Node.js 20** | Runtime Environment | Non-blocking I/O for concurrent requests, JavaScript full-stack |
| **Express.js** | Web Framework | Lightweight, flexible, extensive middleware ecosystem |
| **Python 3.11** | Recommendation Engine | Excellent ML libraries, fast data processing |
| **Flask** | Python Microservice | Lightweight REST API for recommendation service |
| **JWT** | Authentication | Stateless auth, works well with SPAs |

### Database & Caching

| Technology | Purpose | Justification |
|------------|---------|---------------|
| **PostgreSQL 15** | Primary Database | ACID compliance, JSON support, proven reliability |
| **Redis 7** | Cache & Sessions | In-memory speed, session management, rate limiting |

### DevOps & Deployment

| Technology | Purpose | Justification |
|------------|---------|---------------|
| **Docker** | Containerization | Consistent environments, easy deployment |
| **Docker Compose** | Local Development | Multi-container orchestration |
| **GitHub Actions** | CI/CD Pipeline | Integrated with repo, free for public repos |
| **Vercel/Netlify** | Frontend Hosting | Fast CDN, automatic HTTPS, easy deploys |
| **Railway/Render** | Backend Hosting | Simple deployment, free tier available |

---

## 🎨 Frontend Architecture

### Component Structure

```
src/
├── components/
│   ├── Globe/
│   │   ├── Globe3D.jsx           # Three.js globe component
│   │   ├── StationMarkers.jsx    # Pin stations on globe
│   │   └── GlobeControls.jsx     # Interaction handlers
│   ├── Discover/
│   │   ├── DiscoverFeed.jsx      # Main discovery interface
│   │   ├── StationCard.jsx       # Individual station display
│   │   └── TrendingList.jsx      # Trending stations
│   ├── Player/
│   │   ├── RadioPlayer.jsx       # Audio player controls
│   │   └── NowPlaying.jsx        # Currently playing info
│   ├── Search/
│   │   └── SearchBar.jsx         # Search functionality
│   └── Common/
│       ├── Navigation.jsx        # Top nav bar
│       └── SpotifyButton.jsx     # Spotify integration button
├── services/
│   ├── api.js                    # API client wrapper
│   ├── spotify.js                # Spotify SDK integration
│   └── geolocation.js            # Location services
├── store/
│   ├── slices/
│   │   ├── userSlice.js          # User state
│   │   ├── stationSlice.js       # Stations data
│   │   └── playerSlice.js        # Playback state
│   └── store.js                  # Redux store config
└── utils/
    ├── spotifyAuth.js            # OAuth helpers
    └── formatters.js             # Data formatting
```

### State Management Flow

```javascript
// Example Redux slice for stations
{
  stations: {
    nearby: [],
    trending: [],
    favorites: [],
    currentStation: null,
    loading: false,
    error: null
  }
}
```

### Key Frontend Features

1. **3D Globe Interaction**
   - Uses Three.js OrbitControls for smooth rotation
   - Custom shaders for Earth texture and atmosphere glow
   - Raycasting for station marker clicks

2. **Real-time Audio Streaming**
   - HTML5 Audio API for radio stream playback
   - WebSocket connection for live metadata updates

3. **Responsive Design**
   - Mobile-first approach with Tailwind breakpoints
   - Touch gestures for globe navigation on mobile
   - Progressive enhancement for desktop features

---

## ⚙️ Backend Architecture

### API Endpoints

#### Authentication

```
POST   /api/auth/register         # Create new user
POST   /api/auth/login            # Login user
POST   /api/auth/refresh          # Refresh JWT token
POST   /api/auth/logout           # Logout user
GET    /api/auth/spotify/callback # Spotify OAuth callback
```

#### Stations

```
GET    /api/stations              # Get all stations (paginated)
GET    /api/stations/:id          # Get station by ID
GET    /api/stations/nearby       # Get nearby stations (requires coords)
GET    /api/stations/trending     # Get trending stations
GET    /api/stations/search       # Search stations (query params)
```

#### User

```
GET    /api/user/profile          # Get user profile
PUT    /api/user/profile          # Update user profile
GET    /api/user/favorites        # Get favorite stations
POST   /api/user/favorites/:id    # Add station to favorites
DELETE /api/user/favorites/:id    # Remove from favorites
```

#### Spotify Integration

```
GET    /api/spotify/track/:id     # Get track info
POST   /api/spotify/save-track    # Save track to user's Spotify
GET    /api/spotify/current-song  # Identify currently playing song
```

### Backend Service Architecture

```javascript
// Express.js App Structure
app.js
├── middleware/
│   ├── auth.js              # JWT verification
│   ├── rateLimiter.js       # Rate limiting
│   └── errorHandler.js      # Centralized error handling
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
│   ├── recommendation.service.js  # Calls Python microservice
│   └── spotify.service.js
└── models/
    ├── User.js
    ├── Station.js
    └── Favorite.js
```

### Recommendation Engine (Python Microservice)

```python
# Flask Microservice Structure
recommendation_service/
├── app.py                        # Flask app
├── models/
│   └── recommender.py           # Recommendation logic
├── utils/
│   ├── location.py              # Geospatial calculations
│   └── trending.py              # Trending algorithm
└── requirements.txt
```

**Recommendation Algorithm:**

```python
# Pseudocode for recommendation logic
def get_recommendations(user_location, user_preferences):
    # 1. Calculate nearby stations (within 100km radius)
    nearby = calculate_distance(user_location, all_stations)
    
    # 2. Get trending stations (weighted by recent listens)
    trending = calculate_trending_score(stations, time_window=24h)
    
    # 3. Apply user preferences (genres, languages)
    filtered = apply_user_filters(nearby + trending, user_preferences)
    
    # 4. Rank and return top 20
    return rank_stations(filtered, weights={
        'distance': 0.3,
        'trending_score': 0.4,
        'user_preference_match': 0.3
    })
```

---

## 🗄️ Database Design

### PostgreSQL Schema

```sql
-- Users Table
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

-- Stations Table
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

-- Favorites Table (Many-to-Many relationship)
CREATE TABLE favorites (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    station_id INTEGER REFERENCES stations(id) ON DELETE CASCADE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(user_id, station_id)
);

-- Listening History (for trending algorithm)
CREATE TABLE listening_history (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
    station_id INTEGER REFERENCES stations(id) ON DELETE CASCADE,
    listened_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    duration_seconds INTEGER
);

-- Indexes for performance
CREATE INDEX idx_stations_location ON stations(latitude, longitude);
CREATE INDEX idx_stations_country ON stations(country);
CREATE INDEX idx_favorites_user ON favorites(user_id);
CREATE INDEX idx_listening_history_station ON listening_history(station_id, listened_at);
```

### Redis Cache Structure

```
Key Pattern                          | Value Type | TTL      | Purpose
-------------------------------------|------------|----------|------------------
user:session:{userId}                | String     | 24h      | User session data
station:data:{stationId}             | Hash       | 1h       | Station metadata cache
trending:global                      | List       | 30min    | Global trending stations
nearby:{lat}:{lon}                   | List       | 1h       | Cached location queries
ratelimit:{ip}:{endpoint}            | Counter    | 1min     | Rate limiting
spotify:token:{userId}               | String     | 50min    | Cached Spotify tokens
```

---

## 🔌 External Integrations

### 1. Spotify Web API

**Purpose**: Song identification and streaming integration

**Integration Points**:
- OAuth 2.0 authentication flow
- Track search and metadata retrieval
- Save tracks to user's library
- Playback control (optional)

**API Calls**:
```javascript
// Example: Get track information
GET https://api.spotify.com/v1/tracks/{id}
Authorization: Bearer {access_token}

// Save track to library
PUT https://api.spotify.com/v1/me/tracks
Authorization: Bearer {access_token}
Body: { "ids": ["track_id"] }
```

**Rate Limits**: 
- 1,000 requests per user per hour
- Mitigated with Redis caching

### 2. Geolocation API

**Purpose**: Determine user's location for nearby recommendations

**Options**:
- Browser Geolocation API (primary)
- IP-based fallback (ipapi.co or ip-api.com)

**Implementation**:
```javascript
// Browser Geolocation
navigator.geolocation.getCurrentPosition(
    (position) => {
        const { latitude, longitude } = position.coords;
        fetchNearbyStations(latitude, longitude);
    },
    (error) => {
        // Fallback to IP-based location
        fetchLocationByIP();
    }
);
```

### 3. Radio Streaming APIs

**Options**:
- Radio Browser API (community database)
- TuneIn API
- Direct station stream URLs

**Integration**:
```javascript
// Example: Radio Browser API
GET http://all.api.radio-browser.info/json/stations/search
Params: { country: "Kenya", limit: 100 }
```

---

## 🔄 Data Flow

### User Discovery Flow

```
1. User opens app
   └─► Frontend requests user location (browser API)
   
2. Location obtained (lat, lon)
   └─► Frontend sends GET /api/stations/nearby?lat={lat}&lon={lon}
   
3. Backend receives request
   └─► Checks Redis cache for nearby:{lat}:{lon}
   └─► If miss: Calls Python recommendation service
   
4. Recommendation service processes
   └─► Queries PostgreSQL for stations within radius
   └─► Applies trending algorithm
   └─► Returns ranked list
   
5. Backend caches result in Redis (1h TTL)
   └─► Returns JSON response to frontend
   
6. Frontend renders station cards
   └─► User clicks "Play on Spotify" button
   
7. Frontend calls GET /api/spotify/track/{id}
   └─► Backend queries Spotify API (cached in Redis)
   └─► Returns track info + Spotify deep link
   
8. User redirected to Spotify
   └─► Track opens in Spotify app/web player
```

### Authentication Flow

```
1. User clicks "Connect Spotify"
   └─► Frontend redirects to Spotify OAuth URL
   
2. User authorizes app on Spotify
   └─► Spotify redirects to /api/auth/spotify/callback?code={code}
   
3. Backend exchanges code for access token
   └─► Stores tokens in PostgreSQL (encrypted)
   └─► Creates JWT session token
   └─► Caches session in Redis
   
4. Backend redirects to frontend with JWT
   └─► Frontend stores JWT in localStorage
   └─► Attaches JWT to all subsequent API requests
```

---

## 🔐 Security & Authentication

### Authentication Strategy

- **JWT (JSON Web Tokens)** for stateless authentication
- **Refresh Token Rotation** for extended sessions
- **Spotify OAuth 2.0** for Spotify integration

### Security Measures

| Layer | Implementation |
|-------|----------------|
| **Transport** | HTTPS only (TLS 1.3), HSTS headers |
| **Authentication** | Bcrypt password hashing (12 rounds), JWT with RS256 |
| **Authorization** | Role-based access control (RBAC), Middleware verification |
| **API Protection** | Rate limiting (100 req/min per IP), CORS configuration |
| **Data Protection** | Encrypted Spotify tokens at rest, SQL injection prevention (parameterized queries) |
| **Headers** | Helmet.js (XSS, clickjacking, MIME sniffing protection) |

### Environment Variables

```bash
# .env file structure
NODE_ENV=production
PORT=5000
DATABASE_URL=postgresql://user:pass@host:5432/radiogarden
REDIS_URL=redis://host:6379
JWT_SECRET=<strong-random-secret>
JWT_REFRESH_SECRET=<strong-random-secret>
SPOTIFY_CLIENT_ID=<spotify-client-id>
SPOTIFY_CLIENT_SECRET=<spotify-client-secret>
SPOTIFY_REDIRECT_URI=https://domain.com/api/auth/spotify/callback
FRONTEND_URL=https://radiogarden.com
```

---

## ⚡ Scalability & Performance

### Performance Optimizations

1. **Caching Strategy**
   - Redis for hot data (trending, nearby stations)
   - CDN for static assets (Cloudflare/Vercel)
   - Browser caching headers for station metadata

2. **Database Optimization**
   - Indexed queries on lat/lon for geospatial searches
   - Connection pooling (pg Pool, max 20 connections)
   - Read replicas for heavy read operations

3. **Frontend Optimization**
   - Code splitting (React.lazy)
   - Image optimization (WebP format, lazy loading)
   - Three.js performance (LOD for distant markers, instanced rendering)

4. **API Optimization**
   - Pagination (limit 50 items per page)
   - Response compression (gzip)
   - Batch endpoints to reduce round trips

### Scalability Approach

**Horizontal Scaling**:
- Stateless backend (JWT auth allows load balancing)
- Docker containers orchestrated with Kubernetes (future)
- Database sharding by geographic region (future)

**Vertical Scaling** (Initial):
- Start with single server deployment
- Upgrade resources as needed
- Redis caching reduces database load

**Load Estimates**:
- Expected: 1,000 concurrent users → 50 req/sec
- Handles: 10,000 concurrent users → 500 req/sec (with caching)

---

## 🚀 Deployment Strategy

### Development Environment

```bash
# Local setup with Docker Compose
docker-compose up -d

# Services:
# - Frontend: localhost:3000
# - Backend: localhost:5000
# - PostgreSQL: localhost:5432
# - Redis: localhost:6379
# - Python Service: localhost:8000
```

### CI/CD Pipeline (GitHub Actions)

```yaml
# Simplified workflow
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - Run backend tests (Jest)
      - Run frontend tests (Vitest)
      - Lint code (ESLint, Prettier)
  
  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
      - Build Docker images
      - Push to container registry
      - Deploy to hosting platform
```

### Production Deployment

**Frontend**: Vercel/Netlify
- Automatic deployments on git push
- Global CDN distribution
- HTTPS by default

**Backend**: Railway/Render/DigitalOcean
- Dockerized Node.js app
- Managed PostgreSQL database
- Redis instance
- Environment variables via platform

**Monitoring**:
- Error tracking: Sentry
- Analytics: Plausible/Mixpanel
- Uptime monitoring: UptimeRobot

---

## ✅ Technical Feasibility

### Why This Architecture Works

1. **Proven Technologies**
   - All technologies are mature, well-documented, and production-tested
   - Large communities for troubleshooting
   - Extensive libraries and tools available

2. **Realistic Scope**
   - Core features achievable with existing APIs
   - Radio Browser API provides free station database
   - Spotify API is well-documented and accessible

3. **Scalable from Day One**
   - Stateless design allows easy horizontal scaling
   - Caching strategy reduces expensive operations
   - Can start small and grow incrementally

4. **Cost-Effective**
   - Free tiers available for all services initially
   - Open-source technologies (no licensing fees)
   - Pay-as-you-grow pricing models

5. **Development Velocity**
   - JavaScript full-stack reduces context switching
   - Reusable components and libraries
   - Active ecosystems with pre-built solutions

### Potential Challenges & Solutions

| Challenge | Mitigation |
|-----------|----------|
| **Radio stream reliability** | Implement health checks, fallback streams, user reporting |
| **Spotify rate limits** | Aggressive caching, batch requests, user token distribution |
| **Geolocation accuracy** | Multiple fallback methods (browser → IP → manual selection) |
| **Globe performance on mobile** | LOD rendering, reduced marker density, simplified shaders |
| **Song identification** | Use third-party APIs (ACRCloud, Shazam API) as fallback |

### Development Timeline Estimate

- **Phase 1 (Weeks 1-2)**: Setup, Auth, Basic Station API
- **Phase 2 (Weeks 3-4)**: 3D Globe, Station Display
- **Phase 3 (Weeks 5-6)**: Discover & Stream Feature, Spotify Integration
- **Phase 4 (Weeks 7-8)**: Testing, Optimization, Deployment

**Total**: ~8 weeks for MVP

---

## 📚 Additional Resources

- [React Documentation](https://react.dev/)
- [Three.js Examples](https://threejs.org/examples/)
- [Spotify Web API Docs](https://developer.spotify.com/documentation/web-api/)
- [PostgreSQL Performance Tips](https://wiki.postgresql.org/wiki/Performance_Optimization)
- [Radio Browser API](https://api.radio-browser.info/)

---

## 🎯 Conclusion

Radio Garden Enhanced leverages a modern, scalable architecture built on proven technologies. The microservices approach allows independent scaling of components, while the caching strategy ensures fast response times. Integration with Spotify is technically feasible using their Web API, and the recommendation engine can be built with simple geospatial queries and trending algorithms.

This architecture balances:
- **Simplicity** for rapid development
- **Scalability** for future growth
- **Performance** for excellent UX
- **Maintainability** for long-term success

The system is technically sound, financially viable, and can be built incrementally from MVP to full-featured platform.

---

*Last Updated: November 8, 2025*