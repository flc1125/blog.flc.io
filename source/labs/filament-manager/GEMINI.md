# Filament Manager (v2)

## Project Overview
**Filament Manager (不焦虑耗材)** is a minimalist, visual-first web application designed to help 3D printing enthusiasts manage their filament inventory. It solves the problem of tracking numerous rolls by providing a visual "color wall" interface and estimating remaining stock.

**Core Philosophy:**
*   **No-Build:** Runs directly in the browser via ES Modules. No Node.js build steps required.
*   **Serverless:** Powered by Google Firebase (Auth + Firestore) for backend services.
*   **Visual-First:** Prioritizes visual representation (color, remaining levels) over text lists.

## Documentation
*   [v2 Development Guide](docs/v2-开发文档.md) - Detailed technical architecture and feature guide.
*   [v1 Requirements](docs/v1-需求文档.md) - Historical requirements.

## Tech Stack
*   **Frontend:** Vue 3 (ESM Browser Build)
*   **UI:** Tailwind CSS (CDN) + Phosphor Icons
*   **Backend:** Google Firebase (Auth, Firestore)
*   **Font:** Google Fonts (Inter)

## Key Features
*   **Inventory Tracking:** Visual grid of filament rolls with dynamic background colors and contrast-aware text.
*   **Smart Grouping:** Toggleable "Aggregate" view to group identical filaments (same brand/type/color) and estimate total stock.
*   **Batch Management:** Selection mode to delete multiple items simultaneously.
*   **Advanced Sorting & Filtering:** 
    *   Sort by: Purchase Date, Remaining Stock.
    *   Filter by: Brand, Material Type, Stock Status (In Stock/Depleted).
*   **Real-time Search:** Instant search by color name, brand, material, or notes.
*   **Presets:** One-click fill for Bambu Lab official colors (PLA, PETG, ABS, etc.).
*   **Visual Polish:** Glassmorphism UI, stack effects for groups, and animated interactions.

## Data Model (Firestore)
The application uses a `filaments` collection in Firestore with the following schema:
*   `id`: String (Auto-generated)
*   `uid`: String (User ID, for data isolation)
*   `brand`: String (e.g., "Bambu Lab")
*   `type`: String (e.g., "PLA Basic")
*   `colorName`: String (e.g., "Bambu Green")
*   `colorHex`: String (Hex code for UI rendering)
*   `remaining`: Number (0-100)
*   `purchaseDate`: String (ISO date YYYY-MM-DD, Optional)
*   `note`: String (Optional)
*   `updatedAt`: Timestamp

## Development & Usage

### Prerequisites
*   A modern web browser (Chrome, Edge, Firefox, Safari).
*   A local HTTP server (required for ES Modules).

### Running Locally
1.  Navigate to the project directory:
    ```bash
    cd /Users/flc/data/www/blog.flc.io/source/labs/filament-manager
    ```
2.  Start a local server:
    *   **Python:** `python3 -m http.server 8000`
    *   **Node:** `npx http-server`
3.  Open `http://localhost:8000`.

### Configuration
Firebase configuration is embedded in `index.html`.
*   **Security Rules:** Ensure Firestore rules allow read/write only for the authenticated user's `uid`.
*   **Indexes:** Composite index required for `uid` (Asc) + `updatedAt` (Desc).

## Roadmap (v2.x)
- [x] **Core Features:** CRUD, Grouping, Batch Delete, Search/Sort.
- [x] **Visuals:** Dark mode text adaptation, Stacking effects.
- [ ] **Dashboard:** Statistical view (Total Weight, Material Distribution).
- [ ] **PWA:** Manifest and Service Worker for mobile installability.
- [ ] **Data Tools:** Import/Export JSON data.
- [ ] **Expanded Presets:** Support for more brands (PolyMaker, eSun, etc.).