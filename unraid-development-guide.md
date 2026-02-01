# Unraid Development Guide & Best Practices

This guide serves as the central knowledge base for developing Unraid plugins and Docker containers. It consolidates best practices, troubleshooting tips, and workflow standards.

## 1. General Workflow (The "Factory" vs. "Storefront" Model)

We utilize a dual-remote strategy for development to maintain a clean public history while preserving detailed private development logs.

*   **GitLab (Private "Factory"):**
    *   Used for: Active development, detailed commit history, interim releases, debug logs, and CI/CD pipelines (if applicable).
    *   Remote Name: `origin`
*   **GitHub (Public "Storefront"):**
    *   Used for: Public releases, issue tracking, and Community Applications (CA) integration.
    *   History: Clean, squashed history.
    *   Remote Name: `public`

**PowerShell Constraints (Windows):**
*   **No Chaining**: The environment does NOT support command chaining with `&&` or `;`.
*   **Sequential Execution**: Commands must be executed sequentially.

## 2. Unraid Plugin Development (`.plg`)

### The Installer (`.plg` XML)
Unraid's pre-installer parser is strict.
*   **Hybrid XML Strategy**:
    *   **Header**: **Hardcode** `pluginURL`, `name`, and `version` inside the `<PLUGIN>` tag. Do not use recursive entities here.
    *   **Payload**: Use entities (`&gitURL;`) for `<FILE>` tags.
*   **Ampersands**: NEVER use a raw `&`. Always use `&amp;` (e.g., `Time &amp; Pulse`).

### Boot Persistence
Unraid runs in RAM. `/usr/local/emhttp/plugins/` is wiped on reboot.
*   **Re-install Paradigm**: The `.plg` file is re-executed on every boot.
*   **Binary Paths**: NEVER hardcode paths (e.g., `/usr/bin/zramctl`). Use `$(which zramctl || echo "/sbin/zramctl")`.
*   **Init Script**: Trigger initialization (e.g., `zram_init.sh`) via a `<FILE Run="/bin/bash">` block in the `.plg`.

### Dashboard & UI/UX
*   **No Nested Tables**: **NEVER** use `<table>` or `<tbody>` tags inside a dashboard card. It causes `dynamix.js` to crash. Use CSS Grid/Flexbox.
*   **Chart.js**: Ensure labels have decimal precision (e.g., `1.3 GB`) and add `grace: '10%'` to the Y-axis.

### Uninstallation
*   **Golden Path**: Use `<FILE Run="/bin/bash" Method="remove">`.
*   **Output**: Use plain text. Silent scripts hang the uninstall dialog.
*   **No Nginx Reload**: Do NOT reload Nginx/Nchan during uninstall.
*   **Cleanup**: Explicitly `rm -rf` the plugin's config folder in `/boot/config/plugins/`.

### Versioning
*   **Format**: `YYYY.MM.DD.XX` (e.g., `2026.01.27.01`).
*   **Rollover**: Reset to `.01` on a new day. Increment `.XX` on the same day.

## 3. Unraid Docker Development

### Configuration
*   **Sandboxing**: Often requires `/var/run/docker.sock` mapping for management tools.
*   **Permissions**: Use `entrypoint.sh` to map `PUID`/`PGID` (Default: 99/100) to match Unraid's user.

### Testing Cycle
1.  **Local Build**: `docker build -t app:local .`
2.  **Push to Staging (GitLab)**: CI builds image.
3.  **Manual Test**: Run via Docker CLI on Unraid or use the generic Docker template.
4.  **Release**: Push to GitHub and update the XML template in the **Index Repo**.

## 4. Community Applications (CA) Integration

*   **Index Repo**: This repository (`unraid-community-applications-index`) acts as the master index.
*   **Templates**: All application XML templates must be copied here to be published/updated in the store.
