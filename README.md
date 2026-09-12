# Everythingmustgo!

> A 90's retro, phone-only catalog to sell your stuff.

A 90's retro catalog website to sell your stuff, edited with [Sveltia CMS](https://github.com/sveltia/sveltia-cms).
Designed for smartphones only — it intentionally caps the layout to a phone-width column.

## What it is

- A static, **no build, no backend** site: plain HTML/CSS/JS + one JSON file.
- A **catalog** grid of items. Each item shows a thumbnail, name and price.
- Tap an item's image → goes to a **detail page** with a larger image, the name,
  price and description, plus a **BACK** button to return to the catalog.
- An **Admin** panel (`/admin/`) powered by Sveltia CMS where you can add/edit items
  yourself, with **multiple images**, a **name**, a **price** and a **description** for each.

## File layout

```
index.html              # catalog grid (reads content/catalog.json)
item.html               # item detail page (?i=<index>)
styles.css              # 90's retro, mobile-first styles
admin/
  index.html            # loads Sveltia CMS from CDN
  config.yml            # Sveltia/Decap collection config
content/
  catalog.json          # all your items live here
images/                 # uploaded images go here
.nojekyll               # so GitHub Pages serves everything as-is
```

## How the catalog data works

All items live in one file: `content/catalog.json`. Shape:

```json
{
  "items": [
    {
      "name": "Tamagotchi (Used)",
      "price": "$8.00",
      "description": "Virtual pet from 1997...",
      "images": [
        { "image": "/images/sample-1.png" },
        { "image": "/images/another.png" }
      ]
    }
  ]
}
```

The website fetches this JSON at runtime and renders it. You never need to edit
HTML by hand — use the Admin panel.

## Editing your items (Admin)

1. Go to `https://<your-site>/admin/` (e.g. `https://eglibra.github.io/everythingmustgo/admin/`).
2. Log in with GitHub (see "GitHub authentication" below — a one-time setup).
3. Open the **Catalog → Items** collection. Add / edit / reorder items.
   - Each item has: **Name**, **Price**, **Description**, and **Images** (a list —
     add as many image uploads as you want).
4. Save → Sveltia CMS commits the change to `content/catalog.json` (and uploads
   any new images to `images/`) directly to your repository.
5. The site updates automatically once the commit is deployed.

## Running locally

Because the pages fetch JSON, open the folder with a local web server (not `file://`):

```bash
python3 -m http.server 8000
# then open http://localhost:8000/  (and /admin/ for the editor)
```

The Admin panel only works against the real GitHub repo once authenticated, but
the catalog site works fully offline with `content/catalog.json`.

## Deploy on GitHub Pages

1. Push this repo to `main`.
2. In the repo: **Settings → Pages → Build and deployment → Source: Deploy from a
   branch → Branch: `main` / root**.
3. Your site lives at `https://eglibra.github.io/everythingmustgo/`.
   (`.nojekyll` is included so Pages doesn't process the folder.)

## GitHub authentication for the Admin (one-time)

Sveltia CMS needs a GitHub OAuth app so you can log in and commit changes to the repo.

1. Go to <https://github.com/settings/applications/new> (or your org's app settings).
   The form has these fields — fill them in:
   - **Application name:** `Everythingmustgo!` (whatever you like)
   - **Homepage URL:** `https://eglibra.github.io/everythingmustgo/`
   - **Application description:** `Admin panel for Everythingmustgo!` (whatever you like)
   - **Authorization callback URL:** `https://eglibra.github.io/everythingmustgo/admin/`
2. After creating it, copy the **Client ID** (starts with `Iv23.`).
3. Open `admin/config.yml` and replace the placeholder `app_id` value with your Client ID:
   ```yaml
   backend:
     name: github
     repo: eglibra/everythingmustgo
     branch: main
     auth_type: pkce
     app_id: "Iv23.12345abcdef..."   # <- your Client ID
   ```
   Sveltia CMS supports PKCE, so **no client secret** is needed.
4. Commit the change and reload `/admin/`. Sign in with GitHub → you can edit.

> If you change the deploy URL, update `site_url` in `admin/config.yml`, the
> **Homepage URL**, and the **Authorization callback URL** to match.

## Notes

- Mobile-only by design: the layout is capped at ~480px and centered, so it always
  reads like a phone screen. No desktop breakpoints were added on purpose.
- Style is intentionally 1990's: Windows-95 chrome, teal desktop, Comic Sans rainbow
  title, `<marquee>`-style scrolling banner, neon hit-counter, beveled buttons.
- Replace the placeholder images in `images/sample-*.png` with your own via the Admin.
