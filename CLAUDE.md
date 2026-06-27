# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Previewing the site

No build step required. Open `index.html` directly in a browser, or serve it locally:

```bash
python3 -m http.server 8080
# then visit http://localhost:8080
```

---

## Architecture

Static single-page portfolio with three files:

| File | Role |
|------|------|
| `index.html` | Full page structure; all sections in one file |
| `styles.css` | All styles — CSS variables, components, responsive breakpoints |
| `app.js` | All interactivity — i18n, animations, filters, form, lightbox |

No framework, no build system, no dependencies beyond Google Fonts loaded via CDN.

---

## i18n / bilingual system (EN ↔ ES)

`app.js` holds a `translations` object with `en` and `es` keys. Language is persisted in `localStorage`.

**HTML attributes used:**
- `data-i18n="key"` → sets `textContent`
- `data-i18n-html="key"` → sets `innerHTML` (allows bold/br)
- `data-i18n-placeholder="key"` → sets `placeholder`

**Toggle button:** `#langToggle` (shows "ES" when current lang is EN, "EN" when ES).

**Adding a translated string:**
1. Add the key to both `en` and `es` objects in `app.js`
2. Add the `data-i18n` attribute to the HTML element

Contact link `href` values (WhatsApp, iMessage, email) are also rebuilt on each language switch using the translated message strings (`msg-wa`, `msg-imessage`, `msg-email-subject`, `msg-email-body`).

---

## Sections and IDs

| Section | ID | Notes |
|---------|----|-------|
| Nav | `#nav` | Fixed, glassmorphism; `.scrolled` class added after 20px scroll |
| Hero | `#hero` | Full-height; animated orbs; single stat counter |
| About | `#about` | Avatar + skill bars; photo opens lightbox on click |
| Work | `#work` | 3-col card grid; filter buttons by `data-category` |
| Process | — | 4 steps with `.process__connector` between them |
| Contact | `#contact` | Form is frontend-only (no backend); simulates send with `setTimeout` |
| Footer | — | — |

**Floating contact badges** (`#badgeWa`, `#badgeMsg`, `#badgeMail`) are fixed-position overlays outside the main sections. Their `href` values update on language switch.

**Photo lightbox:** clicking `#avatarPhoto` opens `#lightbox`; close with `#lightboxClose` or clicking the backdrop.

---

## Current projects in the work grid

| # | Title (EN) | Category | Preview type | URL |
|---|-----------|----------|--------------|-----|
| 1 | SaaS Product Landing | landing | Placeholder `--1` | — |
| 2 | Corporate Site for Sales Advisor | web | iframe (live) | https://gabriela-byd.vercel.app |
| 3 | Fitness Service Landing | landing | `img_gimnasio.png` | https://marcaacme.vercel.app/ |
| 4 | Online Store | web | Placeholder `--4` | — |
| 5 | Aesthetics & Wellness Landing | landing | `img_stetics.png` | https://stetics-by-isma.vercel.app/ |
| 6 | Photographer Portfolio | web | Placeholder `--6` | — |
| 7 | Gym Admin Panel | web | `img_gym_admin.png` | https://gymacmeadmin.vercel.app |

---

## Adding a project card

```html
<article class="card" data-category="landing">  <!-- or "web" -->
  <div class="card__img">
    <!-- CHOOSE ONE PREVIEW TYPE BELOW -->
    <div class="card__overlay">
      <a href="URL" target="_blank" class="card__link-btn" data-i18n="card-view">View project →</a>
    </div>
    <span class="card__badge" data-i18n="badge-landing">Landing Page</span>
  </div>
  <div class="card__body">
    <h3 class="card__title" data-i18n="cardN-title">Project Name</h3>
    <p class="card__desc" data-i18n="cardN-desc">Short description.</p>
    <div class="card__stack"><span>HTML</span><span>CSS</span><span>JS</span></div>
  </div>
</article>
```

Also add `cardN-title` and `cardN-desc` keys to both `en` and `es` in `app.js`.

### Preview type options

**A — Animated placeholder** (no real project yet):
```html
<div class="card__placeholder card__placeholder--1">  <!-- --1 through --6 -->
  <div class="card__mock">
    <div class="card__mock-bar"></div>
    <div class="card__mock-hero"></div>
    <div class="card__mock-lines"><div></div><div></div><div></div></div>
  </div>
</div>
```

**B — Static screenshot** (recommended when site blocks iframes):
```bash
# Check first:
curl -sI https://url.com | grep -i x-frame
```
```html
<div class="card__screenshot" style="background-image: url('img_name.png');"></div>
```

**C — Live iframe** (only if site has no `X-Frame-Options`):
```html
<div class="card__img card__img--iframe">
  <iframe src="https://url" class="card__iframe" loading="lazy" scrolling="no" tabindex="-1" title="..."></iframe>
  <!-- overlay and badge go here too -->
</div>
```

---

## Taking a screenshot with Selenium

```bash
pip3 install selenium webdriver-manager
```

```python
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
import time

opts = Options()
opts.add_argument("--headless")
opts.add_argument("--window-size=1280,800")
opts.add_argument("--no-sandbox")
opts.add_argument("--disable-dev-shm-usage")
opts.binary_location = "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"

driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=opts)
driver.set_window_size(1280, 800)
driver.get("https://url-del-sitio.com")
time.sleep(3)
driver.save_screenshot("img_name.png")
driver.quit()
```

---

## Skill bars

```html
<div class="skill">
  <div class="skill__header"><span>Name</span><span>90%</span></div>
  <div class="skill__bar"><div class="skill__fill" data-width="90"></div></div>
</div>
```

Width animates via IntersectionObserver when `.about__skills` enters the viewport.

---

## Hero stat counter

```html
<span class="hero__stat-number" data-target="20">0</span>
```

Counts from 0 to `data-target` when `.hero__stats` enters viewport.

---

## Responsive breakpoints

| Breakpoint | Changes |
|------------|---------|
| `≤ 900px` | Work grid 2-col; about/contact 1-col; process connectors hidden |
| `≤ 640px` | Hamburger nav; work grid 1-col; hero text smaller; form 1-col |

---

## Design tokens (CSS variables)

```css
--blue-dark:  #1a3a4e   /* dark backgrounds, footer */
--blue-mid:   #2e6b8f   /* primary color, buttons */
--blue-plat:  #5ba3c9   /* accents, gradients */
--blue-light: #a8d4ea   /* borders, tags */
--blue-pale:  #d6edf7   /* soft backgrounds */
--blue-ghost: #eaf5fb   /* subtle backgrounds */
--white:      #ffffff
--gray-100:   #f4f8fb
--gray-200:   #e2edf4
--text-dark:  #0d2535
--text-mid:   #2d5068
--text-soft:  #5a8aaa
```

**Fonts (Google Fonts):** headings → `Space Grotesk` (400–700); body → `Inter` (300–800).

---

## Favicon

`favicon.svg` — SVG icon with "PT" initials on a dark blue background. Linked in `index.html` as `<link rel="icon" type="image/svg+xml" href="favicon.svg">`.

## Demo badge on cards

Cards with a live demo can show a `.card__demo-badge` element inside `.card__body` with the access credentials:

```html
<div class="card__demo-badge" data-i18n-html="card7-demo">Demo access: <strong>246810</strong></div>
```

CSS is in `styles.css`. Uses `data-i18n-html` so the translated string can include `<strong>` for the code. Both `en` and `es` entries must include the full `<strong>246810</strong>` HTML.

## Personalisation checklist when reusing

- Name/title in Hero (`index.html` ~line 51–55)
- Stat counter target (`data-target` ~line 66)
- Skills and percentages (`about__skills` section)
- Contact email, WhatsApp, iMessage numbers (contact section + `applyTranslations` in `app.js`)
- Project cards (section `#workGrid`)
- Footer copyright (~line 468)
- `translations` object in `app.js` (all user-facing strings)
