# Deploying The Audit to veronicarbaker.com/the-audit

## What's in this folder

```
the-audit/
├── index.html          ← the tool (home page) — sortable filterable table of all 60 audits
├── about/
│   └── index.html      ← the manifesto / about page — the "why"
└── DEPLOY.md           ← this file
```

Final URLs:
- `veronicarbaker.com/the-audit/` → the tool (home)
- `veronicarbaker.com/the-audit/about/` → the manifesto

Both meta tags (`og:url`) are pre-set to those paths.

---

## Before you deploy: TODOs

### 1. Wire the notify-me form to a real endpoint

The tool home page has a "Notify me" form inside each audit row's expanded detail panel. It currently shows a fake confirmation message but doesn't actually capture the email anywhere. You'll want to replace the placeholder handler in `index.html`.

Open `index.html`, search for the `// TODO` comment (inside the `submit` event handler, ~line 580). Two options:

**Option A — Single endpoint, all audits go to one list.** Easiest. Replace the placeholder logic with a `fetch()` to your form provider, including the `data-audit` value as a tag/segment so you know which audit a sign-up is for.

```js
form.addEventListener('submit', async e => {
  e.preventDefault();
  const email = form.querySelector('input').value;
  const auditId = form.dataset.audit;
  await fetch('https://YOUR_ENDPOINT', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email, audit: auditId })
  });
  // ...show thanks
});
```

**Option B — Per-audit lists.** If you want a separate Buttondown / ConvertKit list per audit, map the `auditId` to the right form action. More setup, but cleaner segmentation.

Endpoints worth checking: Buttondown (newsletters), ConvertKit (forms+tags), Formspree (just collects), Mailchimp (lists).

### 2. Create the OG share image

Both pages reference `/the-audit/og-image.png`. Create a 1200×630 PNG at that path. Suggested treatment: warm cream `#F7F4EE`, "The Audit" in Fraunces serif, a tight tagline below in muted gray. Not a launch blocker — sharing still works without it, just no preview image.

### 3. Confirm the contact email

The footer of both pages uses `hello@veronicarbaker.com`. If you haven't set up that mailbox, change it (search both files for `mailto:`) or use whatever address you prefer.

---

## Deploy by host

### GitHub Pages

1. Copy the entire `the-audit` folder into the root of your site repo.
2. Commit and push.
3. Live at `https://veronicarbaker.com/the-audit/` once Pages rebuilds (<1 min).

If your site uses a static-site generator (Jekyll, Hugo, Eleventy), you may need to mark `the-audit/` as a passthrough folder so the generator doesn't try to process it. In Jekyll, add to `_config.yml`:

```yaml
include: [the-audit]
keep_files: [the-audit]
```

### Netlify

1. Drop `the-audit` into your repo, commit, push — auto-deploys.
2. To preview before merging: drag-and-drop the `the-audit` folder onto https://app.netlify.com/drop for a temporary preview URL.

### Vercel

1. Place `the-audit` inside your project's `public/` directory. Commit, push.
2. Or, no-framework: `vercel deploy` from inside the folder.

---

## After deploy: verify

- [ ] `/the-audit/` loads and shows the table with 60 rows
- [ ] Sorting works (click column headers — Company / Category / Status / Last update)
- [ ] Filtering works (Company / Category / Status dropdowns + Clear button)
- [ ] Clicking a row expands an inline detail panel (methodology, sources, notify form)
- [ ] `/the-audit/about/` loads the manifesto
- [ ] The "About" link in the top nav navigates between pages
- [ ] LinkedIn post link in manifesto works
- [ ] Mobile: drag the browser narrow — table should horizontally scroll, detail panel stacks vertically
- [ ] OG share preview: paste both URLs into LinkedIn Post Inspector (https://www.linkedin.com/post-inspector/) — refresh after creating the og-image.png

---

## What's next (Phase 2 and 3, not part of this deploy)

**Phase 2 — Pilot audits.** Research and write 3–5 actual audit entries (cells flip from "In research" to "Live"). Each becomes a permanent page or a richer detail panel with the claim/action/gap and full source list.

**Phase 3 — Verified-employee rebuttals.** Auth (business email + LinkedIn cross-check), moderation, the corporate PR channel, removal-log transparency. Real software project — described in the manifesto, not yet built.

The tool home is structured so Phases 2 and 3 plug in:
- Audit entries flip status (`research` → `live`) by editing a single field in the `AUDITS` array
- Each row already has a unique ID (e.g., `apple-price`) for permanent URLs
- The detail panel layout has a slot ready for richer content when audits go live
