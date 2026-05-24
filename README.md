# ConstructPro v2.1 — Multi-File Structure

## Frontend folder layout

```
frontend/
├── index.html              ← App shell (HTML only, no scripts/styles inline)
├── nginx.conf              ← Nginx config (copy to /volume1/Docker/construct-saas/)
├── styles/
│   └── base.css            ← All CSS: design tokens, layout, components
└── modules/
    ├── state.js            ← APP state, pricebook defaults, constants
    ├── data.js             ← BOQ/MTO data structures, factories, helpers
    ├── nav.js              ← Left nav builder, module switcher
    ├── boq.js              ← BOQ panels (formwork → summary), sidebar
    ├── mto.js              ← MTO panels (concreting → summary), calcs, sync
    ├── pricebook.js        ← Pricebook module UI + lookup modal
    ├── project.js          ← Save/load, project management, user settings, NAS
    ├── exports-excel.js    ← Excel export (BOQ + MTO)
    ├── specdb.js           ← Spec DB data, UI, DOCX exports (BOQ + spec list)
    └── app.js              ← Boot: auth check, session restore, init
```

## Deploy to Synology

1. Copy the entire `frontend/` folder contents to:
   `/volume1/Docker/construct-saas/frontend/`

2. Copy `nginx.conf` to:
   `/volume1/Docker/construct-saas/nginx.conf`

3. Restart nginx container:
   ```
   cd /volume1/Docker/construct-saas
   docker compose restart frontend
   ```

## Development workflow

Edit any `.js` or `.css` file directly — just hard-refresh the browser (Ctrl+Shift+R).
No build step needed. The files load in order via `<script src="...">` tags.

When you're ready to add a build pipeline (Vite/esbuild), the module structure
is already clean enough to drop straight in — each file is a logical unit.

## Adding a new module

1. Create `modules/yourmodule.js`
2. Add `<script src="modules/yourmodule.js"></script>` to `index.html` before `app.js`
3. Add your module's HTML panel div to `index.html`
4. Register it in `nav.js` → `switchModule()` and `buildLeftNav()`
