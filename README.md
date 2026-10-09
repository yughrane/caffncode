# ☕ caffncode — Portfolio Website
### Yug Rane · Frontend Developer

> *"Something's brewing..."*

A coffee-themed personal portfolio website for **Yug Rane**.

---

## 📁 Project Structure

```
caffncode/
├── index.html          # Main portfolio page
├── css/
│   └── style.css       # Styles & themes
├── photo.jpg           # Hero photo
├── projects/           # Projects directory (AccessRide, ClipDrop, etc.)
└── README.md           # Documentation
```

> ⚠️ `style.css` must be inside the `css/` folder. `index.html` references it as `href="css/style.css"`.

---

## 🚀 Getting Started

### 1. Hero Photo
Place your photo in the root folder and name it `photo.jpg`, or use a hosted URL in `index.html`.
```
caffncode/
├── photo.jpg
├── index.html
└── css/
    └── style.css
```

### 2. Open locally
Simply open `index.html` in your browser. You need an **internet connection** for fonts to load from Google Fonts.

Or run a local server for best results:
```bash
# Using Node.js
npx serve .

# Using Python
python -m http.server 8000
```

Then visit `http://localhost:8000` (or `http://localhost:3000` for npx serve).

---

## ✏️ Customisation Guide

### Personal Info
All editable content is in `index.html`. Search for these and update them:

| What | Where to find it | Replace with |
|---|---|---|
| Your photo | `src="https://assets.caffncode.com/photo.jpg"` | Your photo filename or URL |
| Email | `mailto:yug_rane@outlook.com` | Your email |
| Instagram | `href="https://instagram.com/yug.rane"` | Your handle |
| Copyright year | `© 2026 CaffnCode` | Current year |

### Adding a Project
Copy this block inside `<div class="projects-grid">` in `index.html`:
```html
<a href="YOUR_LINK" target="_blank" rel="noopener" class="project-card">
  <div class="project-stripe"></div>
  <div class="project-num">04 / your-project.com</div>
  <div class="project-title">Project Name</div>
  <p class="project-desc">Short description of what you built and why.</p>
  <div class="project-tags">
    <span class="project-tag">React</span>
    <span class="project-tag">Node.js</span>
  </div>
  <div class="project-arrow">↗</div>
</a>
```

### Changing Colours
All colours are CSS variables at the top of `style.css`:
```css
:root {
  --cream:      #fdf6f0;  /* page background */
  --espresso:   #2c1a0e;  /* dark text, borders */
  --caramel:    #c87941;  /* accent colour */
  --latte:      #e8c9a0;  /* light fills */
  --foam:       #f5ede3;  /* card backgrounds */
  --steam:      #d4a574;  /* muted text/lines */
  --dark-roast: #1a0f07;  /* footer background */
  --mocha:      #6b3d1e;  /* body text */
}
```

---

## 🔤 Fonts

| Font | Used for | Source |
|---|---|---|
| **Outfit** (800) | All headings — "Hey, I'm", section titles, nav logo | Google Fonts |
| **Cormorant Garamond** (italic 700) | "Yug Rane" cursive name only | Google Fonts |
| **Space Mono** (400/700) | Body text, tagline, badges, buttons | Google Fonts |

Fonts load via `@import` at the top of `style.css`. Requires internet connection.

> 💡 If you own a license for **PP Neue Montreal** (the original inspiration), replace `'Outfit'` with `'PP Neue Montreal'` everywhere in `style.css` and add a `@font-face` rule pointing to your `.woff2` files.

---

## ✨ Features

- **Split hero layout** — text left, photo right with pixel-art coffee cup
- **Cursive "Yug Rane"** in Cormorant Garamond italic
- **Scroll animations** — every section reveals as you scroll using `IntersectionObserver`
  - Word-by-word text reveal on headings
  - Staggered project cards
  - Skill badges pop in one by one
  - Caramel underline draws across section tags
- **Pixel aesthetic** — grid backgrounds, dashed borders, chunky box-shadows
- **Coffee theme** — animated floating cup, steam lines, cozy warm palette
- **Fully responsive** — single column on mobile, centered layout
- **No JavaScript frameworks** — pure vanilla HTML, CSS, JS
- **No build tools needed** — just open `index.html`

---

## 📱 Social & Contact

- Instagram: [@yug.rane](https://instagram.com/yug.rane)
- Email: [yug_rane@outlook.com](mailto:yug_rane@outlook.com)
- Website: [caffncode.com](https://caffncode.com)

---

## 📄 License

© 2026 CaffnCode.
Built with ☕ and code by **Yug Rane**.
