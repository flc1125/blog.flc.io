# Filament Manager (v7)

## Project Overview
**Filament Manager** is a lightweight, single-page application (SPA) designed to help 3D printing enthusiasts manage their filament inventory. It allows users to track filament usage, organize rolls by color and brand, and monitor depleted stock.

The application is built as a standalone HTML file containing all necessary logic, styling, and templates, making it extremely portable and easy to deploy.

## Architecture & Tech Stack
This project utilizes a modern "No-Build" architecture, relying on browser-native ES Modules and CDNs.

*   **Frontend Framework:** [Vue 3](https://vuejs.org/) (via ESM CDN) - Handles reactivity, state management, and UI rendering.
*   **Styling:** [Tailwind CSS](https://tailwindcss.com/) (via CDN) - Utility-first CSS framework for rapid UI development.
*   **Icons:** [Phosphor Icons](https://phosphoricons.com/) - Consistent and flexible icon family.
*   **Backend & Auth:** [Google Firebase](https://firebase.google.com/) (v10.7.1)
    *   **Authentication:** Google Sign-In.
    *   **Database:** Cloud Firestore for real-time data sync and storage.

## Key Features
*   **Inventory Tracking:** Visual grid of filament rolls with color previews and remaining percentage bars.
*   **Smart Grouping:** Toggleable "Aggregate" view to group identical filaments (same brand/type/color) and estimate total stock.
*   **History:** Separate tab for "Depleted" items.
*   **Search & Filter:** Real-time search by color, brand, or material type.
*   **Presets:** Quick-fill templates for common Bambu Lab and generic filament types (PLA, PETG, ABS, etc.).

## Development & Usage

### Prerequisites
*   A modern web browser (Chrome, Edge, Firefox, Safari).
*   A simple local HTTP server (required because ES Modules via `file://` protocol are restricted by CORS policies).

### Running Locally
1.  Navigate to the project directory:
    ```bash
    cd /Users/flc/data/www/blog.flc.io/source/labs/filament-manager
    ```
2.  Start a local server. For example:
    *   **Python 3:** `python3 -m http.server`
    *   **Node.js (http-server):** `npx http-server`
    *   **VS Code:** Use the "Live Server" extension.
3.  Open the provided localhost URL (e.g., `http://localhost:8000`) in your browser.

### Configuration
The Firebase configuration is currently embedded directly in `index.html`.
*   **API Key & Config:** Located in the `<script type="module">` block under `firebaseConfig`.
*   **Security Note:** While Firebase API keys are generally safe to expose in client-side code (restricted by domain), ensure Firestore Security Rules are configured in the Firebase Console to prevent unauthorized access.

### File Structure
The project consists of a single file:
*   `index.html`: Contains the HTML structure, CSS styles (custom & Tailwind), and Vue.js/Firebase JavaScript logic.
