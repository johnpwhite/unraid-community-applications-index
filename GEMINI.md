# Unraid Community Applications Index

## Purpose
This repository serves as the **Master Index** for all Unraid Community Applications (Docker & Plugins) developed by this organization. It hosts the XML templates required by the Unraid Community Applications store.

## Contents
*   `unraid-development-guide.md`: The central knowledge base for Unraid development workflows, best practices, and troubleshooting.
*   `*.xml`: Application templates (e.g., `openclaw.xml`) used by CA.

## Workflow
When releasing a new version or application:
1.  **Plugins**: Ensure the `.plg` file in the specific repo points to the GitHub release.
2.  **Dockers**: Ensure the Docker image is pushed to the public registry.
3.  **CA Update**: Copy the updated XML template from the specific repo to this repository and push to GitHub.
