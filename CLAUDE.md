# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**evhelper_bot** is a Telegram Mini App (WebApp) consisting of two pages:
1. **EV Charging Calculator** - Calculates EV charging requirements
2. **VW ID.4/ID.6 Resources** - Maintenance and app resources for VW electric vehicles

Both are single-file HTML applications with embedded CSS and JavaScript that run directly within Telegram.

## Architecture

This is a **multi-page standalone application** with no build process, no dependencies, and no external tooling. Each page is self-contained with:

- **HTML Structure**: Semantic markup for content
- **Embedded CSS**: Telegram theme-aware styling using CSS variables
- **Inline JavaScript**: Application logic and Telegram WebApp API integration

### Application Files

1. **[index.html](index.html)** - EV charging calculator
   - Form inputs for battery specs and charge levels
   - Auto-calculation and validation
   - Cloud Storage integration for user preferences

2. **[resources.html](resources.html)** - VW ID.4/ID.6 owner resources
   - Maintenance information (key battery, wipers, air filter)
   - Video tutorials with links
   - Product purchase links (Kaspi.kz, Avtodvorniki.kz)
   - iOS and Android app recommendations with real app icons
   - Back navigation to calculator

### Telegram WebApp Integration

Both pages use the Telegram WebApp API loaded via CDN:
- `window.Telegram.WebApp` provides the Telegram interface
- Telegram theme colors applied via CSS variables (`var(--tg-theme-*)`)
- **Cloud Storage API** (v6.9+): Saves user's car specs in calculator
- **BackButton API**: Resources page shows Telegram's native back button
- Auto-expand to full viewport with `tg.expand()`

### Navigation

- **Calculator → Resources**: VW logo link at bottom of [index.html](index.html)
- **Resources → Calculator**: Back button (both HTML link and Telegram BackButton API)

### Calculator Logic

Located in index.html:

1. Validates all inputs (capacity, usable %, current %, target %)
2. Calculates usable capacity: `capacity × (usablePercent / 100)`
3. Calculates required kWh: `(target - current) / 100 × usableCapacity`
4. Auto-saves battery specs to Telegram Cloud Storage

### Resources Content

Located in [resources.html](resources.html):

**Maintenance Sections:**
1. **Key Battery (CR3032)** - Video tutorial + Kaspi.kz purchase link
2. **Windshield Wipers (700mm + 450mm)** - Narrow push button type + store links
3. **Air Filter (5Q0819653/5Q0819644)** - Compatible models (MAHLE LA1854, MANN SP 1059-2) + video + purchase links

**App Sections:**
- **iOS Apps (7)**: evPoint.kz, Vlife, eDrive.kz, GreenCars, Mycharge.kz, Power HUB, EVS
- **Android Apps (6)**: evPoint.kz, Vlife, eDrive.kz, MyCharge, Power HUB, EVS
- All apps display real app icons from App Store/Play Store
- Icons loaded from Apple CDN (mzstatic.com) for consistency

## Development Workflow

### Local Testing

Since these are static HTML files, you can:
- Open directly in browser: `open index.html` or `open resources.html`
- Use any HTTP server for local testing
- For Telegram-specific features (BackButton, Cloud Storage), deploy to a web server and configure as a Telegram WebApp

### Deployment

Deploy both HTML files to any static hosting service (GitHub Pages, Netlify, Vercel, etc.) and register the index.html URL with Telegram BotFather as a WebApp.

### Making Changes

When modifying this application:
- **Calculator**: Edit [index.html](index.html) - all structure, styles, and logic in one file
- **Resources**: Edit [resources.html](resources.html) - all content, styles, and navigation in one file
- **Shared styling**: Both pages use similar Telegram theme CSS variables for consistency
- Test Telegram theme integration by checking CSS variables work in light/dark modes
- Verify Cloud Storage API compatibility (requires Telegram WebApp v6.9+)
- Maintain input validation logic in calculator to prevent calculation errors

### Adding New Resource Sections

To add new sections to [resources.html](resources.html):
1. Create a new `.resource-card` div after existing cards
2. Add section heading with appropriate emoji
3. Use `.resource-info` for specifications/details
4. Use `.link-buttons` with `<a>` tags for external links
5. Primary buttons use default styling, secondary buttons add `.secondary` class
6. For apps, use `.app-grid` with `.app-item` containers

### App Icon Management

- iOS app icons: Fetch from App Store metadata
- Android app icons: Reuse iOS icons for consistency (Play Store icons harder to extract)
- Icon URLs from Apple CDN (is1-ssl.mzstatic.com) are stable and cacheable
- Image format: `.app-icon img { width: 100%; height: 100%; object-fit: cover; }`

## Testing Telegram WebApp Features

Key Telegram WebApp APIs used:
- `tg.expand()` - Expands the app to full viewport (both pages)
- `tg.ready()` - Signals the app is ready (both pages)
- `tg.CloudStorage.getItem/setItem()` - Persistent storage for car specs (calculator only)
- `tg.themeParams` - Telegram theme colors (both pages)
- `tg.BackButton` - Native Telegram back button (resources page only)

To test these features, the app must be accessed through Telegram's WebApp interface.
