# ConstructPro — Deployment Guide

## 1. Copy this folder to your Synology NAS

Upload the entire `construct-saas` folder to:
  /volume1/Docker/construct-saas/

So the final structure looks like:
  /volume1/Docker/construct-saas/
  ├── docker-compose.yml
  ├── nginx.conf
  ├── .env                  ← contains your Cloudflare token
  ├── api/
  │   ├── server.js
  │   ├── package.json
  │   └── package-lock.json
  └── frontend/
      └── index.html

## 2. SSH into your Synology

  ssh your-user@your-nas-ip

## 3. Deploy

  cd /volume1/Docker/construct-saas
  docker compose up -d

## 4. Check status

  docker compose ps
  docker compose logs -f

## 5. Access your app

  Local:   http://your-nas-ip:8080
  Public:  https://your-domain.com  (via Cloudflare tunnel)

## Useful commands

  docker compose down                        # stop everything
  docker compose restart api                 # restart API only
  docker compose logs -f api                 # stream API logs
  docker compose up -d --force-recreate api  # redeploy after code change
