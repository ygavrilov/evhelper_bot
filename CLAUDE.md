# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**evhelper_bot** is a Telegram Mini App (WebApp) consisting of three pages:
1. **EV Charging Calculator** - Calculates EV charging requirements
2. **Parking Fine Appeal Generator** - Creates appeal letters for EV parking fines
3. **VW ID.4/ID.6 Resources** - Maintenance and app resources for VW electric vehicles

All pages are single-file HTML applications with embedded CSS and JavaScript that run directly within Telegram.

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

2. **[appeal.html](appeal.html)** - Parking fine appeal generator
   - Form for appeal letter data (name, plate number, certificate serial, fine details)
   - Step-by-step submission instructions with links
   - Live preview of generated appeal letter
   - Copy to clipboard functionality
   - Cloud Storage integration for user data (name, plate number, certificate serial)
   - Based on Kazakhstan law allowing free EV parking

3. **[resources.html](resources.html)** - VW ID.4/ID.6 owner resources
   - Maintenance information (key battery, wipers, air filter)
   - Video tutorials with links
   - Product purchase links (Kaspi.kz, Avtodvorniki.kz)
   - iOS and Android app recommendations with real app icons
   - Link to appeal generator
   - Back navigation to calculator

### Telegram WebApp Integration

All pages use the Telegram WebApp API loaded via CDN:
- `window.Telegram.WebApp` provides the Telegram interface
- Telegram theme colors applied via CSS variables (`var(--tg-theme-*)`)
- **Cloud Storage API** (v6.9+): Saves user's car specs in calculator, personal data in appeal generator
- **BackButton API**: Resources and appeal pages show Telegram's native back button
- Auto-expand to full viewport with `tg.expand()`

### Navigation

- **Calculator → Appeal**: Link at bottom of [index.html](index.html)
- **Calculator → Resources**: Link at bottom of [index.html](index.html)
- **Resources → Appeal**: Card with link at top of [resources.html](resources.html)
- **Appeal → Calculator**: Back button (both HTML link and Telegram BackButton API)
- **Resources → Calculator**: Back button (both HTML link and Telegram BackButton API)

### Calculator Logic

Located in index.html:

1. Validates all inputs (capacity, usable %, current %, target %)
2. Calculates usable capacity: `capacity × (usablePercent / 100)`
3. Calculates required kWh: `(target - current) / 100 × usableCapacity`
4. Auto-saves battery specs to Telegram Cloud Storage

### Appeal Generator Logic

Located in [appeal.html](appeal.html):

1. Collects user data via form inputs (full name, plate number, certificate serial, fine number, date, location)
2. Displays step-by-step submission instructions with links to erap-public.kgp.kz
3. Generates appeal letter using Kazakhstan legal template matching the official format
4. Updates preview in real-time as user types
5. Saves personal data (name, plate number, certificate serial) to Cloud Storage for reuse
6. Does NOT save fine-specific data (number, date, location) as it changes each time
7. Copy to clipboard with validation (checks all fields filled before copying)
8. Success feedback animation on successful copy

**Template Format:**
- "Я, [ФИО], являюсь владельцем электромобиля ________________, государственный регистрационный номер [номер], что подтверждается свидетельством о регистрации транспортного средства серия [серия]."
- The blank space for car model/type is intentionally left empty as per original template

**Legal Basis:** Kazakhstan law allows free EV parking in designated areas with certified equipment

**Submission Process:**
1. Login to erap-public.kgp.kz
2. Navigate to complaint section
3. Search by fine number
4. Click "Подать обращение" button
5. Paste generated text into section 2.3

### Resources Content

Located in [resources.html](resources.html):

**Appeal Generator Section:**
- Link to [appeal.html](appeal.html) for parking fine appeals

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
- Open directly in browser: `open index.html`, `open appeal.html`, or `open resources.html`
- Use any HTTP server for local testing
- For Telegram-specific features (BackButton, Cloud Storage), deploy to a web server and configure as a Telegram WebApp

### Deployment

Deploy all three HTML files to any static hosting service (GitHub Pages, Netlify, Vercel, etc.) and register the index.html URL with Telegram BotFather as a WebApp.

### Making Changes

When modifying this application:
- **Calculator**: Edit [index.html](index.html) - all structure, styles, and logic in one file
- **Appeal Generator**: Edit [appeal.html](appeal.html) - all structure, styles, and logic in one file
- **Resources**: Edit [resources.html](resources.html) - all content, styles, and navigation in one file
- **Shared styling**: All pages use similar Telegram theme CSS variables for consistency
- Test Telegram theme integration by checking CSS variables work in light/dark modes
- Verify Cloud Storage API compatibility (requires Telegram WebApp v6.9+)
- Maintain input validation logic in calculator and appeal generator to prevent errors

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
- `tg.expand()` - Expands the app to full viewport (all pages)
- `tg.ready()` - Signals the app is ready (all pages)
- `tg.CloudStorage.getItem/setItem()` - Persistent storage for car specs (calculator) and user data (appeal generator)
- `tg.themeParams` - Telegram theme colors (all pages)
- `tg.BackButton` - Native Telegram back button (resources and appeal pages)

To test these features, the app must be accessed through Telegram's WebApp interface.
