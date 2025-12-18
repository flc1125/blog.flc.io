# Gemini Context: Spacebridge

**Spacebridge** is a lightweight "cloud clipboard" application designed to easily share text and files between devices using a temporary 6-digit code. It is a single-page application (SPA) embedded within the `blog.flc.io` project.

## Project Overview

*   **Name:** Spacebridge (v2.0)
*   **Type:** Single-File Web Application / Lab Experiment
*   **Purpose:** Allow users to create "sessions" (data packets) containing text and files, accessible via a short code.
*   **Architecture:** Serverless (Firebase) + Static Frontend (Vue 3).

## Tech Stack

The application is built using standard web technologies and imported via CDNs, requiring no build step for the core logic.

*   **Frontend Framework:** [Vue.js 3](https://vuejs.org/) (Global Build)
*   **Styling:** [Tailwind CSS](https://tailwindcss.com/) (Play CDN)
*   **Backend-as-a-Service (BaaS):** [Firebase](https://firebase.google.com/) (Modular SDK via ES Imports)
    *   **Authentication:** Google Auth, Anonymous Auth
    *   **Firestore:** Real-time database for session metadata and text content
    *   **Storage:** File storage for uploads

## Project Structure

*   `index.html`: The entire application logic, template, and styles reside in this single file.

## Configuration & Deployment

### Firebase Configuration
The application expects Firebase configuration to be injected into the global window object.

```javascript
// Expected Global Variables
window.__firebase_config = '{"apiKey": "...", ...}';
window.__app_id = 'your-app-id';
window.__initial_auth_token = '...'; // Optional, for pre-authentication
```

If these are not provided, the app attempts to parse an empty object, which will likely result in initialization errors unless a default fallback is configured in the code.

### Running Locally
Since this file is part of the Hexo `source` directory (`source/labs/space-bridge`), it is intended to be served by the Hexo server.

1.  **Start Hexo Server:** Run `hexo server` from the project root.
2.  **Access:** Navigate to `http://localhost:4000/labs/space-bridge/` (path may vary based on Hexo permalink settings).

**Note:** For the app to function fully locally, you may need to manually inject or hardcode a valid Firebase configuration object within `index.html` temporarily during development, or ensure the hosting environment (Hexo theme or layout) injects it.

## Development Conventions

*   **Single File:** Keep logic, styles, and template within `index.html` for portability as a "lab" experiment.
*   **No Build:** Do not introduce npm build steps (Webpack/Vite) for this specific file unless migrating to a separate project. It relies on browser-native ES modules.
*   **Real-time:** The app relies on Firestore `onSnapshot` for real-time synchronization between the "Owner" (sender) and "Guest" (receiver).
