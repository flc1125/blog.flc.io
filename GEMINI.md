# Gemini Context: flc.io Blog

This is a personal blog project built with [Hexo](https://hexo.io/), a fast, simple, and powerful blog framework. It uses the [Icarus](https://github.com/ppoffice/hexo-theme-icarus) theme.

## Project Overview

*   **Type:** Static Site / Personal Blog
*   **Engine:** Hexo
*   **Theme:** Icarus
*   **Deployment:** GitHub Actions

## Directory Structure

*   `source/`: Contains the site content.
    *   `_posts/`: Blog posts, organized by year (e.g., `2025/`).
    *   `_drafts/`: Draft posts not yet published.
    *   `assets/`: Static assets like images and custom CSS.
*   `scaffolds/`: Templates for new posts (`post`, `page`, `draft`).
*   `themes/`: Contains the Icarus theme (managed via npm).
*   `_config.yml`: Main Hexo configuration.
*   `_config.icarus.yml`: Icarus theme configuration.
*   `.github/workflows/`: CI/CD configuration for deployment.

## Key Commands

Run these commands using `npm run <script>` or directly with `hexo <command>` if installed globally.

*   **Start Local Server:**
    ```bash
    npm run server
    # OR
    hexo server
    ```
    Starts the server at `http://localhost:4000`.

*   **Build Static Site:**
    ```bash
    npm run build
    # OR
    hexo generate
    ```
    Generates static files into the `public/` directory.

*   **Clean Cache:**
    ```bash
    npm run clean
    # OR
    hexo clean
    ```
    Removes the `public/` folder and database.

*   **Deploy:**
    ```bash
    npm run deploy
    # OR
    hexo deploy
    ```
    Deploys the site (usually triggers the git deployment strategy).

## Development Conventions

### Creating Posts

*   Posts are written in Markdown.
*   Located in `source/_posts/YYYY/filename.md`.
*   **Front Matter Format:**
    ```yaml
    ---
    title: "Post Title"
    cover: https://example.com/image.jpg # Optional cover image
    date: YYYY-MM-DD HH:mm:ss
    categories:
    - Main Category
    - Sub Category
    toc: true
    tags:
    - Tag1
    - Tag2
    ---
    ```
*   Use `<!-- more -->` to define the excerpt seen on the homepage.

### Categories & Tags

*   **Categories:** Hierarchical (e.g., Programming > Backend). Defined in Front Matter.
*   **Tags:** Flat labels (e.g., Go, Hexo, Life). Defined in Front Matter.
