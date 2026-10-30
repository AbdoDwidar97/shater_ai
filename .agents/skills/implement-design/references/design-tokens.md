# SHATER AI — Figma Design Tokens

Extracted from Figma File: [SHATER AI](https://www.figma.com/design/3zX7JFMMuzyVcLBkdHgjAh/SHATER-AI?node-id=0-1&t=c3p3dLk1dJPEb6Sp-1)

---

## 1. Color Palette

### Brand & Accents
* **Brand Primary Blue:** `#1A237E` (Used for "SHATER AI" logo text, avatar text, dark accents)
* **Accent Orange / Action:** `#FF6D00` (Used for FAB background, active bottom nav icon/text, "View All" link)
* **Soft Warm Orange (Pill/Badge):** `#FFF3E0` (Used for bottom navigation active item container pill)
* **Avatar Background:** `#E8EAF6` (Soft indigo container for the user profile circle)

### Backgrounds & Surfaces
* **Scaffold Screen Background:** `#F4F5F9` (Light grayish-blue backdrop)
* **Card & Bar Surface:** `#FFFFFF` (Pure white for meeting cards, search bar, filter button, bottom nav)
* **Summary Container Background:** `#F4F5F9` (Nested sub-container inside meeting cards)

### Text & Icons
* **Text Primary (High Emphasis):** `#1C1B1F` (Headings, meeting titles, status bar time)
* **Text Secondary (Medium Emphasis):** `#79747E` (Search placeholder, calendar/clock metadata, inactive nav labels)
* **Text Tertiary / Summary Content:** `#49454F` (Summary text snippets inside meeting cards)
* **Border / Dividers:** `#E0E0E0` (Top border of bottom navigation bar)
* **Home Bar Pill:** `#79747E` (Width: 108.dp, Height: 4.dp, Rounded: 100.dp)

---

## 2. Corner Radiuses (Shapes)

* **Meeting Card:** `16.dp`
* **Search Bar & Filter Button:** `24.dp` (Full pill / circular button)
* **FAB (Record Button):** `16.dp`
* **Summary Box inside Card:** `8.dp`
* **Nav Active Item Indicator:** `16.dp`
* **User Avatar:** `18.dp` (Capsule / circle)

---

## 3. Shadows & Elevations

* **Meeting Cards:** Drop shadow `0px 4px 6px rgba(0, 0, 0, 0.03)` (~ `elevation = 2.dp` or custom subtle shadow)
* **Floating Action Button (FAB):** Drop shadow `0px 6px 8px rgba(255, 109, 0, 0.3)` (Vibrant orange glow/shadow, `elevation = 6.dp`)

---

## 4. Typography Scale

* **App Brand Title:** Font `Outfit`, Weight `ExtraBold` (800), Size `20.sp`, Color `#1A237E`
* **Section Heading ("Recent Meetings"):** Font `Outfit`, Weight `Bold` (700), Size `18.sp`, Color `#1C1B1F`
* **Card Title ("Meeting Title"):** Font `Outfit`, Weight `SemiBold` (600), Size `16.sp`, Color `#1C1B1F`
* **Action Link ("View All"):** Font `Geist` / Sans, Weight `SemiBold` (600), Size `13.sp`, Color `#FF6D00`
* **Search Placeholder:** Font `Geist` / Sans, Weight `Normal` (400), Size `15.sp`, Color `#79747E`
* **Card Metadata (Date, Duration):** Font `Geist` / Sans, Weight `Normal` (400), Size `12.sp`, Color `#79747E`
* **Card Summary Snippet:** Font `Geist` / Sans, Weight `Normal` (400), Size `13.sp`, Color `#49454F`
* **Navigation Item Label:** Font `Geist` / Sans, Weight `Bold` (active) / `Medium` (inactive), Size `11.sp`

---

## 5. Spacing & Layout Metrics

* **Screen Horizontal Padding:** `24.dp` (App bar, search section, list section)
* **Card Padding:** `16.dp` internal padding
* **Card Gap:** `12.dp` spacing between cards in the list
* **Bottom Bar Height:** `80.dp`
* **FAB Size:** `56.dp × 56.dp`, with padding `16.dp` from bottom/right edges
