# 🌍 City Safari

[![Java](https://img.shields.io/badge/Java-17-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3-green.svg)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-18-blue.svg)](https://reactjs.org/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-3-38B2AC.svg)](https://tailwindcss.com/)

**AI-assisted city exploration platform that generates actionable destination reports using live context and structured composition.**

City Safari helps users quickly understand a city by composing transport options, places, food, hotels, weather, events, and travel tips into a single, structured report with a clean, responsive UI.

---

## 📋 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [What It Does](#-what-it-does)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Configuration](#️-configuration)
- [API Reference](#-api-reference)
- [Project Structure](#-project-structure)
- [Scripts](#-scripts)
- [Security](#-security)
- [Roadmap](#️-roadmap)
- [Documentation](#-documentation)

---

## ✨ Features

- **AI-assisted content composition** using Gemini with a deterministic section schema
- **Database-backed caching layer** for faster repeat requests
- **Stateless REST API** with client-side navigation state
- **Search history persistence** with clean CRUD endpoints
- **Modular service layer** designed for provider replacement and future expansion
- **Responsive UI** with sticky tabs and smooth in-page navigation
- **Deep links to Google Maps** for routes and locations

---

## 🛠 Tech Stack

### Backend
- **Java 17**
- **Spring Boot 3**
- **REST API**
- **MySQL**

### Frontend
- **React 18**
- **Vite**
- **Tailwind CSS**

### AI
- **Gemini API** (structured content generation)

### Architecture
- DTO-based composition
- Cache-first design
- Clean service layering

---

## 🎯 What It Does

City Safari composes a destination report for a given city (optionally from an origin), returning a structured, UI-ready payload with the following sections:

- 🚗 **Transport options** (when an origin is provided)
- 🚇 **Getting around** (local transit tips)
- 📍 **Must-visit places** (highlights with ratings and notes)
- 🍽️ **Local food** (luxury and budget-friendly picks)
- 🏨 **Hotels** (luxury and budget-friendly options)
- ☀️ **Weather** (current and short forecast)
- 🎉 **Events** (high-level upcoming activities)
- 💡 **Travel tips** (practical pointers)

All sections are returned as a normalized DTO to ensure predictable rendering, extensibility, and clean separation between backend composition and frontend presentation.

The frontend presents these sections using sticky tabs, smooth in-page navigation, a sky/blue theme, and deep links to Google Maps for routes.

---

## 🏗 Architecture

- React SPA communicates only with the Spring Boot REST API over JSON
- A composition pipeline merges AI-generated sections with contextual data and applies caching
- MySQL stores users, preferences, search history, and cached reports
- Designed for testability, extensibility, and incremental feature growth without breaking API contracts

> See `docs/architecture.md` for diagrams and `docs/implementation.md` for a detailed walkthrough. UML diagrams are included.

---

## 📦 Prerequisites

- **Java 17+**
- **Maven 3.8+**
- **Node.js 18+** and npm
- **MySQL 8.0+** (or compatible)

A fresh setup can be running locally in under 5 minutes using the steps below.

---

## 🚀 Quick Start

### Backend Setup

1. **Create the database and tables:**
   ```bash
   # Run the schema SQL file in your MySQL instance
   mysql -u your_username -p < city-safari-backend/database/schema.sql
   ```

2. **Set environment variables:**
   ```bash
   export GEMINI_API_KEY="your_gemini_api_key"
   export DB_URL="jdbc:mysql://localhost:3306/citysafari_db?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true"
   export DB_USERNAME="your_db_username"
   export DB_PASSWORD="your_db_password"
   ```

3. **Start the API (default port 8080):**
   ```bash
   cd city-safari-backend
   mvn spring-boot:run
   ```

### Frontend Setup

```bash
cd city-safari-frontend
npm install
npm run dev
```

Vite serves the app at `http://localhost:5173` by default.

Open the URL and generate a city report!

> **Note:** CORS origins are configured in `city-safari-backend/src/main/resources/application.properties` via `cors.allowed-origins`.

---

## ⚙️ Configuration

Backend configuration supports safe environment overrides:

```properties
server.port=8080
gemini.api.key=${GEMINI_API_KEY:...}
gemini.api.url=https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent
cors.allowed-origins=http://localhost:3000,http://localhost:5173
spring.datasource.* # Override using DB_URL, DB_USERNAME, DB_PASSWORD
```

> **Important:** A valid Gemini API key is required for report generation. Without it, the API returns a validation error.

Database schema is located at: `city-safari-backend/database/schema.sql`

---

## 📡 API Reference

**Base URL:** `http://localhost:8080/api`

### `GET /report`

Generate a destination report for a city.

**Parameters:**
- `city` (required) - Destination city name
- `fromCity` (optional) - Origin city for transport options
- `userId` (optional) - User identifier for history tracking

**Returns:**
```json
{
  "transport": [...],
  "gettingAround": {...},
  "places": [...],
  "food": {...},
  "hotels": {...},
  "weather": {...},
  "events": [...],
  "tips": [...]
}
```

### `GET /history`

Retrieve user's search history.

**Parameters:**
- `userId` (required) - User identifier

**Returns:** Array of recent search history entries

### `DELETE /history/{id}`

Delete a single history entry.

### `DELETE /history?userId=...`

Clear all history for a user.

All endpoints return JSON and follow a stable DTO contract intended for frontend and third-party clients.

---

## 📁 Project Structure

```
city-safari/
├── city-safari-backend/
│   ├── pom.xml
│   ├── database/
│   │   └── schema.sql
│   └── src/main/
│       ├── java/com/citysafari/
│       │   ├── controller/
│       │   ├── service/
│       │   ├── repository/
│       │   └── entity/
│       └── resources/
│           └── application.properties
├── city-safari-frontend/
│   ├── package.json
│   └── src/
└── docs/
    ├── architecture.md
    ├── implementation.md
    ├── experimental-results.md
    ├── future-scope.md
    ├── references.md
    └── conclusion.md
```

---

## 🎬 Scripts

### Backend
```bash
mvn spring-boot:run
```

### Frontend
```bash
npm run dev      # Start development server
npm run build    # Build for production
npm run preview  # Preview production build
```

---

## 🔒 Security

- **Do not hardcode API keys**
- **Use environment variables for secrets**
- **Restrict `cors.allowed-origins` to trusted domains only**

---

## 🗺️ Roadmap

- [ ] Integrate live geocoding and external weather providers
- [ ] Personalized itineraries and improved ranking strategies
- [ ] Enhanced cache freshness policies
- [ ] Expanded data sources for places, events, and transit

---

## 📚 Documentation

Comprehensive documentation is available in the `docs/` directory:

- **Architecture Overview** - System design and component diagrams
- **Implementation Guide** - Detailed technical walkthrough
- **Experimental Results** - Performance analysis and testing
- **Future Scope** - Planned features and enhancements
- **References** - External resources and citations
- **Conclusion** - Project summary and outcomes

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

**Made with ❤️ for travelers and explorers**
