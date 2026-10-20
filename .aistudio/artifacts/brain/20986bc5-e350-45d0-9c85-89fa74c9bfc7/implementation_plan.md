# ChronoWidget: Android Real-Time Age & Precision Milestone Widget

ChronoWidget is a minimalist, high-contrast Android-style home screen widget dedicated to real-time age tracking and precision milestone countdowns. Built specifically around a standalone widget surface with zero clutter, it features authentic long-press haptics to reveal setup settings, granular time increments (years, months, weeks, days, hours, minutes, seconds), OLED high-contrast modes, and browser milestone alerts.

---

### User Review & Critical Decisions

> [!IMPORTANT]
> The following requirements and user preferences have been incorporated into this plan:

- **Confirmed Decision 1 (Standalone Minimal Display)**: As requested ("just standalone widget with minimun pixel display"), the primary interface centers directly on a standalone Android home screen widget card. Unnecessary decorative frames and fluff are eliminated in favor of an authentic widget canvas with ultra-clean pixel boundaries.
- **Confirmed Decision 2 (Long-Tap Settings Gesture)**: As requested ("on long tap give settins optioin which has the show setp setting"), tapping and holding the widget (or clicking and holding on desktop, with a smooth press-feedback ring) opens a native Android-style bottom modal / sheet containing birthdate entry, metric unit visibility toggles, widget resizing, and theme controls.
- **Confirmed Decision 3 (Milestone Alerts & Precision Tracking)**: Live counter updates every second (with sub-second micro-pulse option), calculating current age across all standard units and generating browser notifications + in-app celebrations for major life milestones (e.g., 10,000 days, 1 billion seconds, next birthday).

---

### 1. Overview & Core Concept

- **What It Does**: ChronoWidget acts as an interactive, live-ticking Android home screen widget. The user inputs their date and exact time of birth; the widget immediately transforms into a high-precision live counter tracking their exact age broken down by years, months, weeks, days, hours, minutes, and seconds, alongside total elapsed counts and a live countdown to the next birthday and milestone.
- **Target Persona**: Individuals seeking existential clarity, habit tracking, life-milestone mindfulness, and high-precision timekeeping with an ultra-clean, distraction-free aesthetic.
- **Key Value**: Provides an instant, glanceable, high-contrast perspective on time lived and time ahead with zero bloat and authentic Android widget behavior.

---

### 2. User Experience & Visual Design

#### A. Key User Flows

1. **First-Run Setup**:
   - Zero-state presents a clean prompt directly on the widget surface: *"Long-press to set birthdate"* or an initial date-picker trigger.
   - Quick date & optional time picker modal with instant live validation.
2. **Main Widget View**:
   - **Primary Metric Anchor**: Prominently displays primary age (e.g., `24y 114d` or custom primary focus) in crisp, ultra-high-contrast tabular typography.
   - **Precision Live Grid**: Live synchronized ticking row showing `Hours : Minutes : Seconds` updating every second with zero layout shifts.
   - **Dual-Mode Countdown Bar**: Clean progress indicator and countdown toward the next birthday and nearest major life milestone (e.g., `10,000 Days Milestone in 42d 14h 22m 04s`).
3. **Android Long-Press Interaction**:
   - Pressing and holding for 500ms triggers an authentic Android widget scale-down and tactile ripple effect, opening the **Widget Setup & Settings Sheet**.
   - Settings Sheet controls:
     - Birthdate and Birth Time input.
     - Display Format Switcher: *Complete Breakdown* (`Years + Months + Days + HMS`), *Total Elapsed Units* (`Total Days`, `Total Hours`, `Total Seconds`), or *Split Age & Birthday Countdown*.
     - High-Contrast Theme Switch: *OLED Pure Black*, *Daylight High Contrast White*, and *Deep Indigo Slate*.
     - Milestone Notifications: Request browser notification permissions, toggle alerts for upcoming milestones (7 days prior, 1 day prior, exact moment).
     - Widget Size Presets: *Compact (2x2)*, *Standard Wide (4x2)*, and *Extended Dashboard (4x3)*.

#### B. Visual Identity & Aesthetic Direction

- **Design Philosophy**: Minimalist Swiss-industrial meets Material You Android widget design. Utilitarian, functional, zero decorative pills or gradient fluff.
- **Color Palette**:
  - *OLED Night Mode (Default)*: `#000000` canvas, `#0c0d0e` widget surface, `#ffffff` primary text, `#a1a1aa` secondary metric labels, `#38bdf8` (electric cyan) or `#22c55e` (laser emerald) for milestone accent progress.
  - *High-Contrast Day Mode*: `#f8fafc` canvas, `#ffffff` widget surface, `#020617` primary text, `#475569` secondary text, subtle `#e2e8f0` hairline border.
- **Typography & Tabular Discipline**:
  - Display & Headings: `Outfit` / `Plus Jakarta Sans` for clean, modern geometry.
  - Numbers & Counters: `font-mono tabular-nums` (`JetBrains Mono` / monospace stack) so ticking digits never jitter or vibrate horizontally.
  - Zero-pill metadata: Units rendered as quiet inline labels (`Y`, `M`, `D`, `H`, `M`, `S` or `years · months · days`) separated by typographic dots, never boxed badges.
- **Ergonomics & Touch Targets**:
  - Full touch targets $\ge 44\text{px}$.
  - Long-press detection with visual countdown ring so touch devices and desktop mice work effortlessly.

---

### 3. Key Product Decisions & Trade-Offs

- **Decision 1: Standalone Widget-Centric Viewport with Dimension Presets**
  - *Chosen Approach*: Focus the screen directly on the widget element itself centered in a clean ambient backdrop, with a quick toggle to resize widget dimensions (4x2 banner, 2x2 square, 4x3 dashboard).
  - *Why*: User explicitly stated *"just standalone widget with minimun pixel display"*. Avoiding oversized mock phone bezels keeps the experience focused purely on the widget asset while remaining fully responsive on mobile screens.
  - *Alternatives Considered*: Full smartphone wallpaper mockup with app grid was passed over to honor the user's explicit request for minimum pixel display.

- **Decision 2: High-Precision Live Timing Architecture (`requestAnimationFrame` & 1000ms sync)**
  - *Chosen Approach*: Driven by a synchronized timestamp differential against client local time with exact calendar month/leap-year calculations (using precise date-math for years/months/days elapsed and total milliseconds elapsed).
  - *Why*: Standard naive subtraction (`ms / (1000 * 60 * 60 * 24 * 365)`) fails on leap years and variable month lengths. Precise date arithmetic ensures 100% calendar accuracy.
  - *Alternatives Considered*: Server-side time sync was deemed unnecessary since local device clock is standard for home screen widgets.

- **Decision 3: Web Notifications API & Local Milestone Engine**
  - *Chosen Approach*: Native browser `Notification` API with permission request trigger in settings, combined with a local evaluation engine checking milestone thresholds (e.g. 1,000-day increments, 10,000 days, 1 billion seconds, birthdays) and displaying celebratory in-widget banners if browser permissions are disabled or denied.
  - *Why*: Guarantees milestone feedback works in all browser environments (sandboxes, iOS Safari, desktop, Android Chrome).

- **Decision 4: LocalStorage Persistence**
  - *Chosen Approach*: All preferences (birthdate, birth time, theme, visible units, widget dimensions, alert settings) persist seamlessly across sessions in `localStorage`.

---

### 4. Technical Architecture & Data Strategy

```
┌─────────────────────────────────────────────────────────────┐
│                       ChronoWidget App                      │
├─────────────────────────────────────────────────────────────┤
│  State Management: LocalStorage + Reactive Hooks            │
│  - birthDate & birthTime                                    │
│  - theme: 'oled' | 'light' | 'slate'                        │
│  - displayMode: 'breakdown' | 'totals' | 'dual'             │
│  - widgetSize: '2x2' | '4x2' | '4x3'                        │
│  - notificationsEnabled: boolean                            │
└──────────────┬──────────────────────────────┬───────────────┘
               │                              │
               ▼                              ▼
┌──────────────────────────────┐ ┌────────────────────────────┐
│   Precision Time Engine      │ │    Milestone Engine        │
│   - Calendar Age Math        │ │    - Next Birthday Calc    │
│   - Total Days/Hours/Secs    │ │    - 10k Days / 1B Secs    │
│   - requestAnimationFrame /  │ │    - Web Notification Push │
│     1s Ticker                │ │    - Celebration Banners   │
└──────────────┬───────────────┘ └────────────┬───────────────┘
               │                              │
               ▼                              ▼
┌─────────────────────────────────────────────────────────────┐
│             Android Standalone Widget Surface               │
│  - Long-Press Detector (Touch & Mouse 500ms)                │
│  - High-Contrast Tabular Numerals (Jitter-free font-mono)   │
│  - Real-time Years/Months/Weeks/Days/Hours/Mins/Secs        │
│  - Milestone Progress Ring & Countdown                      │
└──────────────────────────────┬──────────────────────────────┘
                               │ (Long-Press Gesture)
                               ▼
┌─────────────────────────────────────────────────────────────┐
│             Android Widget Setup & Settings Sheet           │
│  - Birthdate & Time Inputs                                  │
│  - Granularity & Display Format Selectors                   │
│  - High-Contrast Theme Toggle (OLED / Light / Slate)        │
│  - Milestone Alerts Configuration                           │
│  - Widget Resize & Export / PWA install instructions        │
└─────────────────────────────────────────────────────────────┘
```

#### Component & Module Strategy

1. **`src/types/widget.ts`**:
   - `WidgetConfig`: birth date/time, theme mode, display format, widget size, notification flags.
   - `AgeBreakdown`: exact years, months, weeks, days, hours, minutes, seconds, milliseconds.
   - `MilestoneInfo`: name, targetDate, totalSecondsRemaining, formattedCountdown, percentageProgress.
2. **`src/utils/timeCalculations.ts`**:
   - Robust calendar-accurate age algorithm handling leap years and varying month lengths.
   - Total unit calculators (total days, total weeks, total hours, total minutes, total seconds).
   - Milestone prediction engine (upcoming birthdays, 5,000/10,000/15,000/20,000 days, 500M/1B seconds).
3. **`src/hooks/useAgeTimer.ts`**:
   - Custom hook managing the high-precision ticker interval, updating elapsed state smoothly without DOM re-render lag.
4. **`src/components/WidgetContainer.tsx`**:
   - Standalone widget chassis supporting Android rounded corners (`rounded-3xl`), hairline border, and long-press event handlers (`onTouchStart`, `onTouchEnd`, `onMouseDown`, `onMouseUp`).
5. **`src/components/AgeDisplay.tsx`**:
   - Minimalist pixel-perfect display rendering the current age with high-contrast tabular typography.
6. **`src/components/MilestoneCountdown.tsx`**:
   - Live countdown to nearest upcoming milestone and next birthday.
7. **`src/components/WidgetSettingsModal.tsx`**:
   - Slide-up Android bottom sheet opened on long-press to configure birthdate, time tracking format, dark/light theme, and milestone alerts.
8. **`src/utils/notifications.ts`**:
   - Browser notification permission request, scheduling check, and sound/vibration triggering.
