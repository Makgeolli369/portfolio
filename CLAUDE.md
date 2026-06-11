# Portfolio Site — Rules & Schema

## Architecture

Three files. Never add more without explicit discussion.

- `index.html` — home page. Fetches `content.json` and renders name, tagline, bio, education, skills, and two CTAs.
- `work.html` — filterable project list. Each entry links to `project.html?p={slug}`.
- `project.html` — individual project detail page. Loads the matching project from `content.json` via `?p=` query param.
- `content.json` — all site data. Edit this to update content. Never hardcode content into any HTML file.

Deploy: `git push` to `main` triggers GitHub Actions → GitHub Pages auto-deploy.
Remote: `https://github.com/Makgeolli369/portfolio.git`

## Design System

All three HTML files share the same inline design system (no external CSS/JS files, no build step):

- Zinc/shadcn-style CSS variables defined in `:root` (light) and `.dark` (dark). The `dark` class is applied to `<html>`; preference persists in `localStorage.theme` (default light). A tiny inline `<head>` script applies it before paint.
- Shared chrome on every page: fixed blur navbar with scroll progress bar, theme toggle, mobile drawer menu, cursor glow (desktop only), scroll-to-top button, 3-column footer.
- Scroll reveal uses an IntersectionObserver adding `.in-view` to `.reveal` / `.reveal-left` elements.
- index.html hero: dot-grid background, floating orbs, conic-ring avatar with initials, letter-by-letter gradient name, typewriter (`roles`), count-up stats bar (computed from projects/certifications/education counts), magnetic CTA buttons.
- work.html cards: 3D tilt + mouse spotlight (desktop), gradient top strip keyed to `statusType`.
- Any CSS change to shared chrome must be applied to all three files (styles are intentionally duplicated inline).

---

## content.json Top-Level Fields

| Field | Notes |
|---|---|
| `name` | Full name |
| `initials` | Shown in avatar |
| `tagline` | Shown in footer brand block; typewriter fallback if `roles` is missing |
| `roles` | Array of strings cycled by the hero typewriter on index.html |
| `bio` | Paragraph in hero section |
| `links.email` | leem72929@gmail.com |
| `links.github` | https://github.com/Makgeolli369 |
| `links.linkedin` | https://www.linkedin.com/in/michael-lee-z/ |
| `links.website` | https://reviewstar.cv |
| `links.websiteLabel` | Display text for website link |
| `location` | Footer left |
| `availability` | Footer left |
| `year` | Footer right |
| `skills` | Array of strings — shown as tags (keep to ~15) |
| `education` | Array of `{ school, degree, year }` |
| `projects` | Array of project objects (see schema below) |

---

## Project Object Schema

```json
{
  "name": "string — project title",
  "url": "string — GitHub or live URL, empty string if none",
  "status": "string — display label on badge",
  "statusType": "live | active | production | cyber | academic",
  "stack": ["array", "of", "tech", "tags"],
  "categories": ["one or two from the allowed list below"],
  "problem": "string — what problem this solves",
  "evidence": "string — what was built and how",
  "result": "string — outcome or current state",
  "highlights": ["array", "of", "specific", "technical", "bullet", "points"],
  "preview": "optional — path to the listing card thumbnail (ProjectName/ProjectName1.png)",
  "images": ["optional — paths to detail page images (ProjectName2.png onward)"]
}
```

### Adding images to a project

1. Create a folder at the portfolio root named after the project, e.g. `ReviewStar/`.
2. Name files `ProjectName1.png`, `ProjectName2.png`, etc.
3. Add to the project entry in `content.json`:
   ```json
   "preview": "ReviewStar/ReviewStar1.png",
   "images": ["ReviewStar/ReviewStar2.png", "ReviewStar/ReviewStar3.png"]
   ```
4. `preview` shows as a thumbnail on the work.html listing card, before "Learn More".
5. `images` renders as a grid on the project.html detail page, below all description paragraphs.
6. Either field is optional — omit if not needed.

### Allowed categories (used for filter buttons — do not invent new ones)
- `"Full Stack"`
- `"Data Engineering"`
- `"AI / LLM"`
- `"Cloud / DevOps"`
- `"Cybersecurity"`
- `"Automation"`
- `"Product"`

### statusType colors
- `live` → emerald (green)
- `active` → blue
- `production` → indigo
- `cyber` → violet
- `academic` → slate (gray)

---

## Writing Rules

1. **No em dashes** (`—`) anywhere. Use commas, semicolons, or split into two sentences.
2. **No PER labels**. The three fields (`problem`, `evidence`, `result`) render as plain unlabeled paragraphs. Write them as standalone sentences that flow without needing a label.
3. **Simple sentence structure**. Avoid constructions that require em dashes to work.
4. **No fabricated projects**. Every project entry must correspond to a real folder in `C:\Users\Michael\projects\`.
5. **Highlights must be factual**. Pull from actual source code, READMEs, or config files. No generic bullets.

---

## Page Routing

- `index.html` — home, no query params
- `work.html` — project list with category filter buttons
- `project.html?p={slug}` — individual project detail; slug is the project name lowercased with spaces/special chars replaced by hyphens

The `slugify` function used in both `work.html` and `project.html`:
```javascript
function slugify(name) {
  return name.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/(^-|-$)/g, '');
}
```

---

## cv.pdf

Place the CV file at `C:\Users\Michael\projects\portfolio\cv.pdf`. The "Download CV" button on `index.html` links to `cv.pdf` with the `download` attribute.

---

## Skills Section

Currently 15 skills. Keep it around that count — it's a display list, not a filter. Filters use categories.

---

## What NOT to do

- Do not add new filter categories without updating every project's `categories` array
- Do not add new `statusType` values without adding a corresponding CSS class in all three HTML files
- Do not touch any HTML file's JS logic to change content — put content changes in `content.json`
- Do not add new top-level HTML sections without discussion
- Do not create additional HTML files beyond the three listed above
