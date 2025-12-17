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

## Data Model (Firestore)
The application uses a `filaments` collection in Firestore with the following schema:
*   `id`: String (Auto-generated)
*   `uid`: String (User ID, for data isolation)
*   `brand`: String (e.g., "Bambu Lab")
*   `type`: String (e.g., "PLA Basic")
*   `colorName`: String (e.g., "Bambu Green")
*   `colorHex`: String (Hex code for UI rendering)
*   `remaining`: Number (0-100)
*   `note`: String (Optional)
*   `updatedAt`: Timestamp

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

#### Firestore Security Rules
To ensure data isolation, deploy the following rules to your Firebase project:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /filaments/{document} {
      allow create: if request.auth != null && request.resource.data.uid == request.auth.uid;
      allow read, update, delete: if request.auth != null && resource.data.uid == request.auth.uid;
    }
  }
}
```

#### Firestore Indexes
The app uses compound queries (`uid` + `updatedAt`). If you encounter index errors, create the following composite index in Firestore:
*   Collection: `filaments`
*   Fields: `uid` (Ascending), `updatedAt` (Descending)

### File Structure
The project consists of a single file:
*   `index.html`: Contains the HTML structure, CSS styles (custom & Tailwind), and Vue.js/Firebase JavaScript logic.

## Roadmap (v1 Requirements)
*   **Localization:** Change app title to "耗材管家".
*   **Dashboard:** Add a statistical dashboard (Total Weight, Material Distribution).
*   **Footer:** Add copyright info.
