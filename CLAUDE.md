# dichtbij3d Deployment / Infrastructure

This repository contains the deployment configuration and environment setup (Docker Compose) for the Dichtbij3D project.

## Tech Stack
- Docker
- Docker Compose

## Guidelines
- Ensure `.env.example` is kept up to date when introducing new environment variables.
- Never expose sensitive credentials in `docker-compose.yml` or commit `.env` files.
- Keep the `docker-compose.yml` clean and organized.
