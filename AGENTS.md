# Agent Instructions for dichtbij3d Deployment

## Project Context
This is the infrastructure and deployment repository for Dichtbij3D.

## Commands
- **Start Services:** `docker-compose up -d`
- **Stop Services:** `docker-compose down`

## Agent Rules
- Do not attempt to run or build application code here; this is for deployment configuration.
- Verify environment variables against `.env.example` before starting services.
- Do not commit any changes to existing configurations without explicit user permission.
