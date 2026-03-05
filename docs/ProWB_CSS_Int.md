## Revision History

| Version | Date | Author | Status | Description |
| --- | --- | --- | --- | --- |
| 4.5.0 | 2026-02-28 | S. Köhne | Stable | Initial Release: Modular CSS Architecture & Adaptive Theme Engine |

<div style="page-break-after: always;"></div>

### 1. Introduction: ProSuite CSS Architecture

The Graphical User Interface (GUI) of the ProSuite has been completely modularized following the strict **Separation of Concerns (SoC)** principle. To guarantee deterministic and error-free compilation of stylesheets into the `index.html` monolith by the ProWB (C-Builder), all files are controlled via a strict numerical prefix system (`00_` through `99_`).

This architecture actively prevents "CSS Bleeding" (unintended style overrides) and ensures that dynamically generated documents (Markdown) and cryptographic dashboards seamlessly integrate into the user's Corporate Design without compromising the structural layout.

---

### 2. Module Specifications ("Where is what?")

The architecture is divided into five functional layers, which are loaded sequentially into the DOM:

#### Layer 1: The Foundation (`00_`)

* **`00_reset.css`:** W3C Standardization. Eliminates browser-specific quirks (default paddings, margins, list-styles) to establish a perfectly identical starting point (Zero-Baseline) across all devices.
* **`00_vars.css`:** Defines the global CSS variables (`:root`). This acts as the "Fallback Theme" (Siemens Industrial Base) that the system reverts to if no specific theme is actively loaded.

#### Layer 2: The Skeleton (`10_`)

* **`10_layout.css`:** Exclusively handles the global App Shell. This file styles the header, navigation bar, footer, and the flexible main container (`#app-view-container`). It contains **no** content-specific colors; it strictly references global CSS variables.

#### Layer 3: The Document Engine (`20_`)

* **`20_markdown.css`:** The exclusive styling layer for the C-based GFM parser of the ProWB. It translates raw HTML tags (tables, code blocks, checkboxes) into the ProSuite Industrial design. **Security/Design Feature:** This file utilizes 100% adaptive CSS variables and contains *zero* hardcoded hex colors. Consequently, audit manuals automatically adapt to the currently active theme (Light/Dark/Matrix).

#### Layer 4: UI & Dashboards (`40_` to `50_`)

* **`40_dashboard.css`:** Defines the responsive grid structure, interactive action buttons, and the terminal window (`.console-window`) used in the main views.
* **`50_components.css`:** Provides styles for microscopic UI elements such as standard input fields (`.std-input`), cryptographic hash output boxes, and status LEDs (`.led-big`).

#### Layer 5: The Theme Engine (`99_`)

* **`99_theme_*.css`:** These files deliberately override the variables established in `00_vars.css`. They allow the entire visual appearance of the suite to be transformed instantly without touching a single layout or component file.

<div style="page-break-after: always;"></div>

### 3. The Adaptive Theme Engine (Integration & Customization)

ProSuite utilizes a data-driven theming system. The JavaScript (`30_ui_general.js`) simply toggles the `data-theme` attribute on the `<body>` tag (e.g., `<body data-theme="matrix">`). The CSS engine captures this state and globally injects the new color palette.

#### 3.1 Mandatory Variables for a New Theme

To construct a new theme (e.g., `99_theme_ocean.css`), the `body[data-theme="ocean"]` block **must** override the following core variables:

```css
body[data-theme="ocean"] {
    /* Backgrounds & Surfaces */
    --color-bg: #001f3f;          /* Outermost background */
    --color-surface: #003366;     /* Background for cards & tables */
    --color-header-bg: #000a1a;   /* Header, table headers & footer */
    --color-nav-bg: #002244;      /* Navigation bar & code-block borders */

    /* Typography & Contrast */
    --text-main: #e0f7fa;         /* Primary text color (body) */
    --text-muted: #80cadd;        /* Labels, quotes, and secondary info */
    --text-light: #ffffff;        /* High contrast required for header texts */

    /* Signals & Accents */
    --color-accent: #00e5ff;      /* Primary interaction color (buttons, links) */
    --color-accent-dim: #00b8cc;  /* Hover states and borders */
    --color-danger: #ff4d4d;      /* Errors & Panic Wipe alerts */

    /* Effects & Typography */
    --shadow-metallic: 0 0 10px rgba(0, 229, 255, 0.3); /* Neon/Glow effect */
    --shadow-card: 0 4px 10px rgba(0,0,0,0.5);
    
    --font-stack: 'Segoe UI', sans-serif;
    --font-mono: 'Courier New', monospace; /* Mandatory for terminal & code */
}

```

#### 3.2 Theme-Specific Component Overrides

In 95% of cases, overriding the CSS variables is sufficient. However, if a theme requires structural alterations (e.g., the Matrix theme, which demands harsh borders around `.dash-card` elements instead of the soft drop-shadows used in other themes), the override **must occur strictly within the theme file**.

**Rule:** Never modify `40_dashboard.css` to accommodate a specific theme!
**Correct Execution:** Place the override directly inside the `99_theme_matrix.css`:

```css
/* Example of a legitimate structural override within a theme file */
body[data-theme="matrix"] .dash-card {
    border: 1px solid #00ff00; /* Harsh border exclusively for Matrix mode */
    box-shadow: none;          /* Disable standard card shadows */
}

```

### 4. Styling Best Practices

1. **Zero Hardcodes in UI/Markdown:** Layout and Markdown files (`10_` through `50_`) must **never** utilize hardcoded HEX values (e.g., `color: #333`). They must exclusively reference variables (e.g., `color: var(--text-main)`). Failing to do so will break the dynamic theme-switching capability.
2. **Native HTML Elements:** Checkboxes inside Task Lists (ProTU-Audits) are not replaced by complex custom graphics. Instead, we utilize `accent-color: var(--color-accent);` applied to the native HTML checkbox. This guarantees maximum rendering performance with zero DOM overhead.
3. **Scrollbar Hygiene:** Custom scrollbars (such as those within the terminal) should be subtly matched to the `--color-bg` and `--color-surface` of the respective theme to prevent visual immersion breaks.

---

| © 2026 Sascha Köhne | ✉️ **Contact for KYC:** [koehne83@googlemail.com](mailto:koehne83@googlemail.com) | 🛡️ **ProTU-Verified** |
| --- | --- | --- |
| System Architect | BrainAI Inhaber : Sascha Alexander Köhne | We don't need **BRUTEFORCE**, we know **PHYSICS** |
