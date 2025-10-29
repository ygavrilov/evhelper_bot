# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**evhelper_bot** is a Telegram Mini App (WebApp) for calculating EV (Electric Vehicle) charging requirements. It's a single-file HTML application with embedded CSS and JavaScript that runs directly within Telegram.

## Architecture

This is a **standalone single-file application** ([index.html](index.html)) with no build process, no dependencies, and no external tooling. The entire application is self-contained in one HTML file with:

- **HTML Structure**: Form inputs for battery specs and charge levels
- **Embedded CSS**: Telegram theme-aware styling using CSS variables
- **Inline JavaScript**: Calculator logic and Telegram WebApp API integration

### Telegram WebApp Integration

The app uses the Telegram WebApp API loaded via CDN:
- `window.Telegram.WebApp` provides the Telegram interface
- Telegram theme colors applied via CSS variables (`var(--tg-theme-*)`)
- **Cloud Storage API** (v6.9+): Saves user's car specs (battery capacity, usable %) for future sessions
- Auto-calculation triggers on input changes

### Core Calculation Logic

Located in [index.html:232-294](index.html#L232-L294):

1. Validates all inputs (capacity, usable %, current %, target %)
2. Calculates usable capacity: `capacity × (usablePercent / 100)`
3. Calculates required kWh: `(target - current) / 100 × usableCapacity`
4. Auto-saves battery specs to Telegram Cloud Storage

## Development Workflow

### Local Testing

Since this is a static HTML file, you can:
- Open directly in browser: `open index.html` or use any HTTP server
- For Telegram-specific features, deploy to a web server and configure as a Telegram WebApp

### Deployment

Deploy [index.html](index.html) to any static hosting service (GitHub Pages, Netlify, Vercel, etc.) and register the URL with Telegram BotFather as a WebApp.

### Making Changes

When modifying this application:
- All changes go into [index.html](index.html) (structure, styles, and logic are all in one file)
- Test Telegram theme integration by checking CSS variables work in light/dark modes
- Verify Cloud Storage API compatibility (requires Telegram WebApp v6.9+)
- Maintain input validation logic to prevent calculation errors

## Testing Telegram WebApp Features

Key Telegram WebApp APIs used:
- `tg.expand()` - Expands the app to full viewport
- `tg.ready()` - Signals the app is ready
- `tg.CloudStorage.getItem/setItem()` - Persistent storage for car specs
- `tg.themeParams` - Telegram theme colors

To test these features, the app must be accessed through Telegram's WebApp interface.
