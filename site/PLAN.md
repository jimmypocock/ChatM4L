# ChatM4L Website Plan

## Overview
One-page landing site for chatm4l.com - flashy, minimal text, Ableton-inspired yellow/orange gradients.

## Deploy
- **Host**: Cloudflare Pages (domain already on Cloudflare)
- **Repo folder**: `site/`
- **Domain**: chatm4l.com

## Design
- Dark background (#1a1a1a or similar)
- Yellow/orange gradient accents (#F5A623, #FF6B00)
- Modern, clean typography
- Minimal text, visual-first
- Mobile responsive

## Sections

### 1. Hero
- Big tagline: "AI-Powered Music Production" or similar
- Subtitle: Control Ableton with natural language
- CTA button: "Download Free" → GitHub releases
- Maybe animated gradient background or Ableton-style grid

### 2. Features (3-4 icons with short text)
- 🎹 Create MIDI patterns with words
- 🎛️ Control any parameter naturally
- 🧠 Context-aware - knows your session
- ⚡ Multi-provider (Claude, GPT, Ollama)

### 3. How It Works (3 steps)
1. Drop ChatM4L on any track
2. Type what you want
3. AI does the rest

### 4. Demo/Screenshot
- GIF or screenshot of the device in action
- Show the chat interface with example conversation

### 5. Download/CTA
- Download button → GitHub releases
- Version number shown
- "Requires Ableton Live 11+ with Max for Live"

### 6. Footer
- GitHub link
- Made by [name]
- Version info

## Files to Create
```
site/
├── index.html
├── style.css
├── script.js (minimal, maybe just for animations)
└── assets/
    ├── og-image.png (social share)
    └── demo.gif (optional)
```

## Tech
- Pure HTML/CSS/JS
- No framework needed
- CSS custom properties for colors
- CSS animations for flair
- Responsive with CSS Grid/Flexbox

## Colors
```css
--bg-dark: #0d0d0d;
--bg-card: #1a1a1a;
--accent-orange: #F5A623;
--accent-yellow: #FFD93D;
--gradient: linear-gradient(135deg, #F5A623, #FF6B00);
--text-primary: #ffffff;
--text-muted: #888888;
```

## Next Steps
1. Create index.html with structure
2. Create style.css with Ableton-inspired design
3. Add minimal JS for animations if needed
4. Test locally
5. Commit and push
6. Set up Cloudflare Pages deployment
