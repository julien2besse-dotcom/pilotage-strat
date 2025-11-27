# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-page web application for presenting a Balanced ScoreCard for Dynastar (ski manufacturer). The project is a case study for TSM (Toulouse School of Management) focused on Strategic Axis 3: Internal Processes.

**Team Members:** Konrad, Shayna, Davide, Julien

## Architecture

### Single-File Application Structure

This is a self-contained HTML file (`index.html`) with embedded CSS and JavaScript. All styling and logic are inline - there are no external CSS or JS files.

**Key sections:**
- `:root` CSS variables define the design system (colors, spacing, borders)
- Particle animation system using HTML5 Canvas for interactive background
- Card-based layout using CSS Grid (bento-grid pattern)
- Presentation mode with FLIP (First, Last, Invert, Play) animations for smooth card transitions

### Design System

The application uses a dark, premium glass-morphism design:
- **Primary color:** `#ef4444` (red, Dynastar brand color)
- **Background:** Dark with layered effects (blobs, noise overlay, particle canvas)
- **Cards:** Glass-morphism with backdrop-filter blur, radial gradient spotlights following mouse
- **Typography:** 'Outfit' for headings, 'Inter' for body text (Google Fonts)

### Interactive Features

1. **Mouse-based interactions:**
   - 3D tilt effect on cards (perspective transforms)
   - Spotlight effect following cursor (CSS custom properties `--mouse-x`, `--mouse-y`)
   - Particle connections to cursor within 150px radius
   - Header parallax on mouse move
   - Ambient light blobs that shift with cursor

2. **Navigation:**
   - Two-tab system: "Objectifs & KPI" and "Fiche Technique"
   - Pill-style navigation with active state
   - Smooth section transitions with fade-in animations

3. **Presentation Mode:**
   - Click any card to enter presentation mode
   - Uses FLIP animation technique for smooth position/scale transitions
   - Arrow keys navigate between cards (→/↓ next, ←/↑ previous, ESC exit)
   - Focused card becomes full-screen overlay (85vw × 85vh) with scrollable content
   - Background content is dimmed and blurred
   - Portal pattern: card is moved to `document.body` and repositioned with placeholder

### Content Structure

**Four Main Cards (KPI Section):**
1. 🏭 Améliorer les processus industriels (Industrial processes)
2. 📦 Améliorer les processus logistiques (Logistics)
3. 🚀 Améliorer la conception (Design/R&D)
4. 💻 Optimiser les SI (Information Systems)

Each card contains:
- Icon, title, and 3 sub-objectives
- Each sub-objective has: title, quote (context), and KPI badge

**Fiche Technique Section:**
Data sheet for KPI #10 "% Livraisons en 1 ou 2 fois" with:
- Strategic objective alignment
- Target value, owner, frequency
- Formula display
- Management notes and hypotheses

## Development

### Local Development

Simply open `index.html` in a modern browser. No build process required.

For live development with auto-reload, use any static server:
```bash
python3 -m http.server 8000
# or
npx serve .
```

### Deployment

This project is deployed as a static site. The file must be named `index.html` for deployment platforms (GitHub Pages, Netlify, Vercel, etc.).

### Browser Compatibility

Requires modern browser with support for:
- CSS backdrop-filter (glass-morphism)
- CSS custom properties
- Canvas API
- ES6+ JavaScript (async/await, arrow functions, classes)
- CSS Grid and Flexbox

### Mobile Responsiveness

Responsive breakpoint at 768px:
- Single column layout on mobile
- Reduced font sizes
- Disabled 3D tilt effects
- Simplified presentation mode (95vw cards)
- Centered team badges

## Code Patterns

### FLIP Animation Pattern

The presentation mode uses FLIP technique for performance:
1. **First:** Measure initial card position (`getBoundingClientRect()`)
2. **Last:** Add focused classes, move card, measure final position
3. **Invert:** Apply transform to make it appear at start position
4. **Play:** Remove transform with transition, card animates to final position

Key implementation details:
- Placeholder div maintains grid layout when card is "portaled" to body
- `isAnimating` flag prevents concurrent animations
- `async/await` ensures animation completion before next transition
- Body gets `perspective: none` in presentation mode to fix `position: fixed` context

### Mouse Position Tracking

Cards use CSS custom properties for mouse-reactive effects:
```javascript
card.style.setProperty('--mouse-x', `${x}px`);
card.style.setProperty('--mouse-y', `${y}px`);
```

CSS references these in `radial-gradient()` for spotlight effects.

### Particle System

Canvas-based particles with:
- Particle count scales with viewport size
- Particles connect within 100px distance (white lines)
- Particles connect to cursor within 150px (brighter lines)
- Continuous `requestAnimationFrame` loop
- Auto-resize on window resize with reinitialization

## Content Updates

To modify KPI cards or objectives:
1. Locate the card in the `#kpi` section (search for data-index attribute)
2. Update `.sub-item` blocks (title, quote, KPI badge)
3. Maintain the 3 sub-items per card structure for visual consistency

To update the technical sheet:
1. Modify the `#fiche` section
2. Update `.data-row` elements in `.data-grid`
3. Formula is in `.formula-box` with custom fraction styling
4. Alerts/notes use `.alert-box` styling

## Performance Considerations

- Particle count is dynamic: `(width × height) / 15000`
- Mouse move events are not throttled (consider adding for low-end devices)
- Canvas redraws every frame (60fps target)
- Multiple blur filters may impact performance on older devices
- `will-change: transform` on blobs for GPU optimization
