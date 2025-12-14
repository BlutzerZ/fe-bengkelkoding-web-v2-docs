# Panduan UI/UX Standards - Bengkel Koding Web V2

## Filosofi Desain

Bengkel Koding Web V2 mengadopsi **clean, modern, dan accessible design** dengan fokus pada user experience yang intuitif untuk platform pembelajaran.

**Prinsip Utama**:

- **Simplicity**: Desain yang sederhana dan mudah dipahami
- **Consistency**: Konsistensi visual dan interaksi di seluruh aplikasi
- **Accessibility**: Dapat diakses oleh semua pengguna, termasuk yang memiliki disabilitas
- **User-Centered**: Mengutamakan kebutuhan dan kenyamanan pengguna

---

## Design System

Design system adalah kumpulan komponen, pattern, dan panduan yang digunakan untuk membangun interface yang konsisten.

### Color Palette

Color palette adalah palet warna yang digunakan di seluruh aplikasi. Warna dipilih untuk menciptakan hierarki visual yang jelas dan mudah dibaca.

#### Primary Colors

**Warna biru** digunakan sebagai warna utama brand Bengkel Koding. Warna ini merepresentasikan teknologi, profesionalisme, dan kepercayaan.

```css
/* Primary - Blue */
--primary-50: #eff6ff; /* Sangat terang - untuk background hover */
--primary-100: #dbeafe; /* Terang - untuk background subtle */
--primary-200: #bfdbfe; /* Terang - untuk border */
--primary-300: #93c5fd; /* Medium terang - untuk disabled state */
--primary-400: #60a5fa; /* Medium - untuk hover state */
--primary-500: #3b82f6; /* Main primary - warna utama */
--primary-600: #2563eb; /* Medium gelap - untuk active state */
--primary-700: #1d4ed8; /* Gelap - untuk emphasis */
--primary-800: #1e40af; /* Sangat gelap - untuk text */
--primary-900: #1e3a8a; /* Extra gelap - untuk text bold */
```

**Penggunaan**:

- `primary-500`: Button primary, link, icon aktif
- `primary-600`: Button hover state
- `primary-100`: Background highlight, badge

#### Neutral Colors

**Warna grayscale** untuk text, background, border, dan elemen UI netral. Memberikan kontras yang baik untuk keterbacaan.

```css
/* Grayscale */
--gray-50: #f9fafb; /* Background page */
--gray-100: #f3f4f6; /* Background card, input */
--gray-200: #e5e7eb; /* Border, divider */
--gray-300: #d1d5db; /* Border default */
--gray-400: #9ca3af; /* Placeholder text */
--gray-500: #6b7280; /* Secondary text */
--gray-600: #4b5563; /* Body text secondary */
--gray-700: #374151; /* Body text primary */
--gray-800: #1f2937; /* Heading text */
--gray-900: #111827; /* Text emphasis, heading */
```

**Penggunaan**:

- `gray-50`, `gray-100`: Background halaman dan card
- `gray-200`, `gray-300`: Border dan divider
- `gray-500`, `gray-600`: Text secondary
- `gray-700`, `gray-800`, `gray-900`: Text primary dan heading

#### Semantic Colors

**Warna semantik** untuk status dan feedback. Membantu user memahami state dan action result dengan cepat.

```css
/* Success - Green */
--success: #10b981; /* Untuk operasi berhasil */
--success-light: #d1fae5; /* Background success alert */
--success-dark: #065f46; /* Text success emphasis */

/* Warning - Yellow */
--warning: #f59e0b; /* Untuk peringatan */
--warning-light: #fef3c7; /* Background warning alert */
--warning-dark: #92400e; /* Text warning emphasis */

/* Error - Red */
--error: #ef4444; /* Untuk error dan danger */
--error-light: #fee2e2; /* Background error alert */
--error-dark: #991b1b; /* Text error emphasis */

/* Info - Blue */
--info: #3b82f6; /* Untuk informasi */
--info-light: #dbeafe; /* Background info alert */
--info-dark: #1e40af; /* Text info emphasis */
```

**Kapan Menggunakan**:

- **Success (Hijau)**: Form submit berhasil, operasi selesai
- **Warning (Kuning)**: Peringatan, action yang perlu perhatian
- **Error (Merah)**: Error validation, operasi gagal
- **Info (Biru)**: Informasi umum, tips, helper text

---

### Typography

Typography adalah sistem tipografi untuk mengatur font, ukuran, dan hierarchy teks. Tipografi yang baik meningkatkan keterbacaan dan user experience.

#### Font Family

**Poppins** dipilih karena modern, mudah dibaca, dan mendukung berbagai weight untuk hierarchy yang jelas.

```typescript
// Using Poppins from Google Fonts
import { Poppins } from "next/font/google";

const poppins = Poppins({
  subsets: ["latin"],
  weight: ["100", "200", "300", "400", "500", "600", "700", "800", "900"],
});
```

#### Font Scale

**Font scale** menciptakan hierarchy visual yang jelas. Ukuran yang lebih besar untuk heading, ukuran sedang untuk body text.

| Element        | Size            | Weight | Line Height | Penggunaan                 |
| -------------- | --------------- | ------ | ----------- | -------------------------- |
| **H1**         | 48px (3rem)     | 700    | 1.2         | Page title, hero heading   |
| **H2**         | 36px (2.25rem)  | 600    | 1.3         | Section heading utama      |
| **H3**         | 28px (1.75rem)  | 600    | 1.4         | Subsection heading         |
| **H4**         | 24px (1.5rem)   | 600    | 1.4         | Card title, modal title    |
| **H5**         | 20px (1.25rem)  | 600    | 1.5         | Small section title        |
| **H6**         | 18px (1.125rem) | 600    | 1.5         | Smallest heading           |
| **Body Large** | 18px (1.125rem) | 400    | 1.6         | Lead paragraph, intro text |
| **Body**       | 16px (1rem)     | 400    | 1.6         | Default body text          |
| **Body Small** | 14px (0.875rem) | 400    | 1.5         | Secondary text, meta info  |
| **Caption**    | 12px (0.75rem)  | 400    | 1.4         | Labels, timestamps, helper |

**Tips**:

- Line height lebih besar untuk body text (1.6) meningkatkan keterbacaan
- Font weight 400 untuk body, 600-700 untuk heading
- Gunakan hierarchy yang konsisten di seluruh aplikasi

#### Tailwind Typography Classes

```html
<!-- Headings -->
<h1 class="text-5xl font-bold">Heading 1</h1>
<h2 class="text-4xl font-semibold">Heading 2</h2>
<h3 class="text-3xl font-semibold">Heading 3</h3>

<!-- Body Text -->
<p class="text-base text-gray-700">Regular body text</p>
<p class="text-sm text-gray-600">Small text</p>
<p class="text-xs text-gray-500">Caption text</p>
```

---

### Spacing System

**Spacing system** menggunakan **4px baseline grid** untuk konsistensi spacing di seluruh aplikasi. Semua margin, padding, dan gap harus kelipatan 4px.

**Mengapa 4px?**

- Mudah dihitung dan diingat
- Menciptakan rhythm visual yang harmonis
- Compatible dengan berbagai screen size

| Token | Value | Usage                | Contoh Penggunaan                    |
| ----- | ----- | -------------------- | ------------------------------------ |
| `xs`  | 4px   | Minimal spacing      | Icon padding, badge padding          |
| `sm`  | 8px   | Tight spacing        | Spacing antar icon, chip gap         |
| `md`  | 16px  | Default spacing      | Default padding, margin antar elemen |
| `lg`  | 24px  | Comfortable spacing  | Section spacing, card padding        |
| `xl`  | 32px  | Spacious sections    | Large section margin                 |
| `2xl` | 48px  | Large sections       | Hero section padding                 |
| `3xl` | 64px  | Extra large sections | Page section divider                 |

**Best Practices**:

- Gunakan spacing yang konsisten untuk elemen yang sama
- Spacing lebih besar untuk section separator
- Spacing lebih kecil untuk related elements

```html
<!-- Padding examples -->
<div class="p-4">xs padding</div>
<div class="p-8">sm padding</div>
<div class="p-16">md padding</div>

<!-- Margin examples -->
<div class="mb-4">xs bottom margin</div>
<div class="mt-8">sm top margin</div>
<div class="mx-16">md horizontal margin</div>
```

---

### Border Radius

**Border radius** menciptakan tampilan yang lebih soft dan modern. Gunakan radius yang konsisten untuk elemen sejenis.

```css
/* Border radius scale */
--radius-sm: 0.25rem; /* 4px - untuk small elements seperti badge */
--radius-md: 0.5rem; /* 8px - untuk button, input (default) */
--radius-lg: 0.75rem; /* 12px - untuk card, modal */
--radius-xl: 1rem; /* 16px - untuk large card, image */
--radius-2xl: 1.5rem; /* 24px - untuk hero card, featured elements */
--radius-full: 9999px; /* Fully rounded - untuk avatar, pill button */
```

**Kapan Menggunakan**:

- `sm` (4px): Badge, chip, small tag
- `md` (8px): Button, input field, dropdown
- `lg` (12px): Card, panel, modal
- `xl` (16px): Large card, hero image
- `full`: Avatar, circular button, pill-shaped button

---

### Shadows

**Shadow system** menciptakan depth dan hierarchy. Shadow yang subtle lebih modern dan tidak mengganggu.

```css
/* Elevation system */
--shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05); /* Subtle - untuk hover state */
--shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1); /* Default - untuk card */
--shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1); /* Medium - untuk dropdown */
--shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1); /* Large - untuk modal */
--shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1); /* Extra large - untuk popup */
--shadow-2xl: 0 25px 50px -12px rgb(0 0 0 / 0.25); /* Dramatic - untuk dialog */
```

**Hierarchy Shadow**:

1. **Base**: Langsung di background (no shadow)
2. **Raised**: Card default (`shadow`)
3. **Floating**: Dropdown, tooltip (`shadow-md`)
4. **Overlay**: Modal, dialog (`shadow-lg` atau `shadow-xl`)

**Tips**:

- Jangan gunakan shadow yang terlalu gelap/tebal
- Shadow lebih besar = elemen lebih tinggi di z-axis
- Gunakan shadow konsisten untuk elemen sejenis

---

## Component Library

Component library adalah kumpulan komponen UI yang reusable. Gunakan komponen ini untuk konsistensi dan efisiensi development.

### Buttons

Button adalah elemen interaktif utama untuk user action. Design yang jelas membantu user memahami action yang bisa dilakukan.

#### Primary Button

**Primary button** untuk action utama/penting di setiap page (submit, save, continue).

```tsx
<button
  className="
  px-6 py-3
  bg-blue-600 hover:bg-blue-700 active:bg-blue-800
  text-white font-medium
  rounded-lg
  transition-colors duration-200
  disabled:bg-gray-300 disabled:cursor-not-allowed
  focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
"
>
  Primary Button
</button>
```

**States**:

- Default: `bg-blue-600`
- Hover: `bg-blue-700` (lebih gelap)
- Active: `bg-blue-800` (pressed state)
- Disabled: `bg-gray-300` (tidak bisa di-click)
- Focus: Ring biru untuk keyboard navigation

#### Secondary Button

**Secondary button** untuk action alternatif/kurang penting (cancel, back, view details).

```tsx
<button
  className="
  px-6 py-3
  bg-white hover:bg-gray-50 active:bg-gray-100
  text-gray-900 font-medium
  border border-gray-300
  rounded-lg
  transition-colors duration-200
  focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2
"
>
  Secondary Button
</button>
```

**Kapan Menggunakan**:

- Primary: Submit form, save data, action penting
- Secondary: Cancel, back, alternative action
- Maksimal 1 primary button per screen section

#### Button Sizes

**3 ukuran button** untuk berbagai konteks penggunaan.

| Size       | Padding       | Font Size   | Penggunaan                 |
| ---------- | ------------- | ----------- | -------------------------- |
| **Small**  | `px-3 py-1.5` | `text-sm`   | Inline action, compact UI  |
| **Medium** | `px-4 py-2`   | `text-base` | Default button size        |
| **Large**  | `px-6 py-3`   | `text-lg`   | Hero CTA, prominent action |

**Tips**:

- Gunakan size yang konsisten dalam satu section
- Large button untuk call-to-action penting
- Small button untuk action di table/list

---

### Input Fields

Input field adalah komponen untuk menerima user input. Design yang jelas membantu user memahami apa yang perlu diisi.

#### Text Input

**Struktur input field yang baik** terdiri dari: label, input, dan helper text.

```tsx
<div className="space-y-2">
  {/* Label - wajib ada untuk accessibility */}
  <label className="block text-sm font-medium text-gray-700">
    Email Address
  </label>

  {/* Input field dengan berbagai state */}
  <input
    type="email"
    className="
      w-full px-4 py-2
      border border-gray-300 rounded-lg
      focus:ring-2 focus:ring-blue-500 focus:border-transparent
      placeholder:text-gray-400
      disabled:bg-gray-100 disabled:cursor-not-allowed
      transition-shadow duration-200
    "
    placeholder="your@email.com"
  />

  {/* Helper text - optional untuk guidance */}
  <p className="text-xs text-gray-500">Helper text goes here</p>
</div>
```

**Elemen Penting**:

- **Label**: Jelaskan apa yang harus diisi (wajib)
- **Placeholder**: Contoh format input (optional)
- **Helper text**: Panduan atau informasi tambahan
- **Error message**: Tampilkan jika ada validation error

#### Input States

**5 state input** yang harus di-handle dengan baik.

```tsx
{
  /* 1. Default - state awal */
}
<input className="border border-gray-300" />;

{
  /* 2. Focus - saat user click/tab ke input */
}
<input className="border border-blue-500 ring-2 ring-blue-500" />;

{
  /* 3. Error - saat validation gagal */
}
<input className="border border-red-500 ring-2 ring-red-500" />;

{
  /* 4. Success - saat validation berhasil (optional) */
}
<input className="border border-green-500 ring-2 ring-green-500" />;

{
  /* 5. Disabled - saat input tidak bisa diubah */
}
<input className="bg-gray-100 cursor-not-allowed" disabled />;
```

**Best Practices**:

- Selalu tampilkan error message yang jelas
- Focus state harus terlihat jelas (untuk keyboard navigation)
- Disabled input harus terlihat berbeda (gray background)
- Validation sebaiknya real-time atau on blur

````

---

### Cards

Card adalah container untuk mengelompokkan informasi terkait. Card memudahkan scanning dan membuat layout lebih organized.

#### Basic Card

**Card standar** dengan border, shadow subtle, dan padding yang nyaman.

```tsx
<div
  className="
  bg-white
  border border-gray-200
  rounded-lg
  shadow-sm
  p-6
  hover:shadow-md
  transition-shadow duration-200
"
>
  <h3 className="text-lg font-semibold text-gray-900 mb-2">Card Title</h3>
  <p className="text-gray-600">Card content goes here</p>
</div>
````

**Elemen Card**:

- Background putih untuk kontras dengan page background
- Border subtle untuk definition
- Shadow sm untuk depth
- Hover shadow untuk feedback
- Padding 24px (p-6) untuk spacing yang nyaman

#### Interactive Card

**Clickable card** dengan hover effect yang lebih prominent. Gunakan untuk card yang bisa di-click (link ke detail page).

```tsx
<div
  className="
  bg-white
  border border-gray-200
  rounded-lg
  shadow-sm
  p-6
  cursor-pointer
  hover:shadow-lg hover:border-blue-300
  transform hover:-translate-y-1
  transition-all duration-200
"
>
  {/* Card content */}
</div>
```

**Interactive Effects**:

- `cursor-pointer`: Tunjukkan bahwa card bisa di-click
- `hover:shadow-lg`: Shadow lebih besar saat hover
- `hover:border-blue-300`: Border biru untuk emphasis
- `hover:-translate-y-1`: Lift effect (naik sedikit)
- Transition smooth untuk professional look

**Kapan Menggunakan**:

- Basic card: Untuk display informasi statis
- Interactive card: Untuk card yang bisa di-click (course card, event card)

---

### Badges

Badge adalah label kecil untuk menunjukkan status, category, atau count. Badge harus jelas dan mudah dibaca.

```tsx
{
  /* Status badges dengan semantic colors */
}

{
  /* Active/Success - hijau */
}
<span className="px-3 py-1 text-xs font-medium rounded-full bg-green-100 text-green-800">
  Active
</span>;

{
  /* Pending/Warning - kuning */
}
<span className="px-3 py-1 text-xs font-medium rounded-full bg-yellow-100 text-yellow-800">
  Pending
</span>;

{
  /* Inactive/Error - merah */
}
<span className="px-3 py-1 text-xs font-medium rounded-full bg-red-100 text-red-800">
  Inactive
</span>;

{
  /* Info/Default - biru */
}
<span className="px-3 py-1 text-xs font-medium rounded-full bg-blue-100 text-blue-800">
  New
</span>;
```

**Design Decisions**:

- `rounded-full`: Pill shape lebih modern
- `text-xs`: Small size agar tidak mendominasi
- `font-medium`: Readable tapi tidak terlalu bold
- Background light + text dark: Kontras yang baik
- Color semantik: User langsung paham status

**Penggunaan**:

- **Green**: Status aktif, sukses, completed
- **Yellow**: Pending, in progress, warning
- **Red**: Inactive, failed, error
- **Blue**: New, info, default tag

````

---

### Alerts

Alert adalah notifikasi untuk memberikan feedback ke user tentang hasil action atau informasi penting. Alert harus jelas dan mudah dipahami.

#### Success Alert

**Success alert** menunjukkan bahwa action berhasil dilakukan (form submitted, data saved, etc).

```tsx
<div
  className="
  flex items-start gap-3
  p-4
  bg-green-50 border border-green-200
  rounded-lg
"
>
  {/* Icon untuk visual cue */}
  <svg className="w-5 h-5 text-green-600" /* ... */ />

  <div>
    <h4 className="text-sm font-medium text-green-900">Success!</h4>
    <p className="text-sm text-green-700">
      Your action was completed successfully.
    </p>
  </div>
</div>
````

**Elemen Alert**:

- **Icon**: Visual indicator untuk status (checkmark untuk success)
- **Title**: Ringkasan singkat
- **Message**: Penjelasan detail action/info
- **Background light**: Tidak terlalu mencolok
- **Border**: Emphasize alert boundary

#### Error Alert

**Error alert** menunjukkan error atau action yang gagal. Harus memberikan guidance untuk solve masalah.

```tsx
<div
  className="
  flex items-start gap-3
  p-4
  bg-red-50 border border-red-200
  rounded-lg
"
>
  {/* Error icon (X atau exclamation) */}
  <svg className="w-5 h-5 text-red-600" /* ... */ />

  <div>
    <h4 className="text-sm font-medium text-red-900">Error!</h4>
    <p className="text-sm text-red-700">
      Something went wrong. Please try again.
    </p>
  </div>
</div>
```

**Best Practices untuk Alerts**:

- Tampilkan di posisi yang prominent (top of page/section)
- Berikan pesan yang actionable (jelaskan apa yang harus dilakukan)
- Auto-dismiss untuk success (optional)
- Keep error alert sampai user action
- Gunakan semantic colors (hijau, merah, kuning, biru)

**Jenis Alert**:

- **Success**: Operasi berhasil, data tersimpan
- **Error**: Validation error, server error
- **Warning**: Peringatan yang perlu perhatian
- **Info**: Informasi umum, tips

---

### Loading States

Loading state penting untuk memberikan feedback bahwa aplikasi sedang memproses request. Mencegah user bingung atau multiple click.

#### Spinner

**Spinner** untuk loading state umum (fetch data, submit form, etc).

```tsx
<div className="flex items-center justify-center">
  <div
    className="
    w-8 h-8
    border-4 border-gray-200 border-t-blue-600
    rounded-full
    animate-spin
  "
  ></div>
</div>
```

**Kapan Menggunakan**:

- Saat fetch data dari API
- Saat submit form
- Saat load page/component
- Center screen atau inline dengan content

**Tips**:

- Tambahkan text "Loading..." untuk clarity
- Gunakan size yang sesuai konteks (8px, 16px, 24px)
- Warna spinner sesuai brand (blue-600)

#### Skeleton

**Skeleton loader** untuk preview structure sambil loading content. Lebih baik dari spinner karena user bisa lihat layout.

```tsx
<div className="space-y-4 animate-pulse">
  <div className="h-4 bg-gray-200 rounded w-3/4"></div>
  <div className="h-4 bg-gray-200 rounded w-full"></div>
  <div className="h-4 bg-gray-200 rounded w-5/6"></div>
</div>
```

**Keuntungan Skeleton**:

- User bisa lihat structure content yang akan muncul
- Mengurangi perceived loading time
- Lebih smooth transition dari loading ke content
- Better UX dibanding blank screen

**Kapan Menggunakan**:

- List items (course list, user list)
- Card layout (dashboard cards)
- Content page (article, profile)
- Table rows

**Best Practices**:

- Skeleton shape mirip dengan content actual
- Gunakan `animate-pulse` untuk subtle animation
- Gray-200 untuk skeleton color (subtle)

---

## Responsive Design

Responsive design memastikan aplikasi tampil baik di semua ukuran screen (mobile, tablet, desktop).

### Breakpoints

**Tailwind breakpoints** yang digunakan. Mobile-first approach: style default untuk mobile, tambahkan breakpoint untuk larger screen.

```typescript
// Tailwind breakpoints
const breakpoints = {
  sm: "640px", // Mobile landscape
  md: "768px", // Tablet portrait
  lg: "1024px", // Desktop / Tablet landscape
  xl: "1280px", // Large desktop
  "2xl": "1536px", // Extra large desktop / 4K
};
```

**Typical Screen Sizes**:

- **< 640px**: Mobile portrait (iPhone, Android)
- **640-768px**: Mobile landscape
- **768-1024px**: Tablet
- **1024-1280px**: Laptop/Desktop
- **> 1280px**: Large desktop

### Responsive Patterns

**Pattern umum** untuk responsive layout.

```tsx
{
  /* 1. Stack on mobile, grid on desktop */
}
{
  /* Mobile: 1 column, Tablet: 2 columns, Desktop: 3-4 columns */
}
<div
  className="
  grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4
  gap-4
"
>
  {/* Items */}
</div>;

{
  /* 2. Hide on mobile - untuk content non-essential */
}
<div className="hidden md:block">Desktop only content</div>;

{
  /* 3. Show only on mobile - untuk mobile navigation */
}
<div className="block md:hidden">Mobile only content</div>;

{
  /* 4. Responsive text size */
}
<h1 className="text-2xl md:text-3xl lg:text-4xl">Responsive Heading</h1>;

{
  /* 5. Responsive padding - lebih besar di desktop */
}
<div className="p-4 md:p-6 lg:p-8">Responsive padding</div>;
```

**Mobile-First Principles**:

- Default style untuk mobile (< 640px)
- Add styles untuk larger screens dengan breakpoints
- Prioritize essential content di mobile
- Touch-friendly tap targets (min 44x44px)

````

---

## Accessibility Standards

Accessibility (a11y) memastikan aplikasi bisa digunakan oleh semua orang, termasuk yang memiliki disabilitas (visual, motor, cognitive).

### ARIA Labels

**ARIA (Accessible Rich Internet Applications)** attributes membantu screen reader memahami content dan interaction.

```tsx
{/* Button dengan icon saja - perlu aria-label */}
<button aria-label="Close modal">
  <svg>×</svg>
</button>

{/* Input dengan helper text - link dengan aria-describedby */}
<input
  aria-describedby="email-helper"
  type="email"
/>
<p id="email-helper">We'll never share your email</p>

{/* Loading state - announce ke screen reader */}
<div role="status" aria-live="polite">
  Loading...
</div>
````

**Kapan Menggunakan ARIA**:

- `aria-label`: Untuk elements tanpa visible text (icon button)
- `aria-describedby`: Link element dengan description/helper text
- `aria-live`: Untuk dynamic content updates (notifications, loading)
- `role`: Define semantic role (button, status, dialog)

### Keyboard Navigation

**Keyboard navigation** penting untuk users yang tidak bisa pakai mouse (motor disability, power users).

```tsx
{
  /* Keyboard accessible interactive element */
}
<div
  tabIndex={0} // Make focusable dengan Tab key
  role="button" // Announce sebagai button ke screen reader
  onKeyPress={(e) => {
    // Handle Enter dan Space seperti click
    if (e.key === "Enter" || e.key === " ") {
      handleClick();
    }
  }}
  className="focus:outline-none focus:ring-2 focus:ring-blue-500"
>
  Interactive element
</div>;
```

**Keyboard Support Wajib**:

- `Tab`: Navigate between interactive elements
- `Enter`: Activate button/link
- `Space`: Activate button, toggle checkbox
- `Esc`: Close modal/dropdown
- Arrow keys: Navigate menu/list

**Tips**:

- Semua interactive elements harus focusable (button, link, input)
- Focus order harus logical (top to bottom, left to right)
- Focus indicator harus visible (ring, outline)

### Focus States

**Focus state** harus selalu visible untuk keyboard navigation.

```css
/* Always provide visible focus indicators */
.focus-visible:focus {
  outline: 2px solid blue;
  outline-offset: 2px;
}
```

**Best Practices**:

- Jangan gunakan `outline: none` tanpa alternative
- Focus ring harus kontras dengan background
- `focus-visible` untuk style focus hanya dari keyboard
- Test dengan Tab key untuk verify focus flow

**Common Focus Styles**:

- Ring: `focus:ring-2 focus:ring-blue-500`
- Outline: `focus:outline-2 focus:outline-blue-500`
- Background: `focus:bg-blue-50`

---

## Animations & Transitions

Animation dan transition membuat UI lebih hidup dan memberikan feedback visual. Tapi jangan berlebihan - subtle lebih baik.

### Transition Timing

**Duration dan easing** untuk smooth transitions.

```css
/* Duration - pilih sesuai konteks */
--transition-fast: 150ms; /* Quick hover, small changes */
--transition-base: 200ms; /* Default transition */
--transition-slow: 300ms; /* Large/complex transitions */

/* Easing - natural movement curve */
--ease-in: cubic-bezier(0.4, 0, 1, 1); /* Start slow, end fast */
--ease-out: cubic-bezier(0, 0, 0.2, 1); /* Start fast, end slow (best for UI) */
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1); /* Slow both ends */
```

**Kapan Menggunakan**:

- Fast (150ms): Hover color change, small UI feedback
- Base (200ms): Default untuk semua transitions
- Slow (300ms): Modal open/close, large element movement

### Common Animations

**Animation patterns** yang sering digunakan.

```tsx
{
  /* 1. Fade in - untuk new content appearing */
}
<div className="animate-fade-in">Fading content</div>;

{
  /* 2. Slide in - untuk modal, drawer */
}
<div className="animate-slide-in-right">Sliding content</div>;

{
  /* 3. Hover grow - untuk interactive cards */
}
<button className="hover:scale-105 transition-transform duration-200">
  Hover me
</button>;

{
  /* 4. Smooth color change - untuk buttons, backgrounds */
}
<div className="bg-blue-500 hover:bg-blue-600 transition-colors duration-200">
  Color transition
</div>;
```

**Best Practices**:

- Keep animations subtle (slight movement, not dramatic)
- Duration 150-300ms optimal (too fast = jarring, too slow = sluggish)
- Use `ease-out` untuk most UI transitions
- Respect `prefers-reduced-motion` untuk accessibility
- Animate `transform` dan `opacity` (performant)
- Avoid animating `width`, `height`, `top`, `left` (slow)

````

---

## Layout Patterns

Layout patterns adalah struktur umum untuk mengorganize content. Gunakan pattern yang konsisten untuk familiar user experience.

### Container

**Container** membatasi max width dan center content untuk readability optimal.

```tsx
{/* Max width 1280px (7xl), centered, dengan padding horizontal */}
<div className="container mx-auto px-4 max-w-7xl">
  {/* Content */}
</div>
````

**Mengapa Container Penting**:

- Line length terlalu panjang sulit dibaca (optimal: 60-80 characters)
- Center content lebih aesthetic di large screen
- Padding horizontal mencegah content mentok ke edge

### Two Column Layout

**Sidebar + Main content** pattern untuk dashboard, documentation, settings page.

```tsx
<div className="grid grid-cols-1 lg:grid-cols-12 gap-6">
  {/* Sidebar - 3 columns di desktop, full width di mobile */}
  <aside className="lg:col-span-3">Sidebar content</aside>

  {/* Main content - 9 columns di desktop, full width di mobile */}
  <main className="lg:col-span-9">Main content</main>
</div>
```

**Rasio Column**:

- 3:9 (25%:75%) - Narrow sidebar
- 4:8 (33%:67%) - Medium sidebar
- 5:7 (42%:58%) - Wide sidebar

### Dashboard Layout

**Full-height dashboard** dengan fixed sidebar dan scrollable main content.

```tsx
<div className="flex h-screen">
  {/* Sidebar - fixed width, dark background */}
  <aside className="w-64 bg-gray-900 text-white">Navigation</aside>

  {/* Main area - flex grow untuk fill remaining space */}
  <div className="flex-1 flex flex-col">
    {/* Header - fixed height */}
    <header className="h-16 bg-white border-b">Header</header>

    {/* Content - scrollable, grow untuk fill space */}
    <main className="flex-1 overflow-y-auto p-6">Content</main>
  </div>
</div>
```

**Structure**:

- Sidebar: Fixed width (256px), full height
- Header: Fixed height (64px), sticky/fixed
- Main: Scrollable, fills remaining space
- Mobile: Sidebar jadi drawer/collapsible

---

## UX Principles

Prinsip UX untuk menciptakan experience yang baik bagi user.

### 1. **Consistency (Konsistensi)**

**Definisi**: Elemen yang sama harus terlihat dan berperilaku sama di seluruh aplikasi.

**Implementasi**:

- Gunakan spacing, colors, dan typography yang konsisten
- Button primary selalu blue, secondary selalu outline
- Interaction pattern yang predictable (hover, click, focus)
- Reuse components untuk ensure consistency

**Mengapa Penting**: User belajar sekali, apply di semua tempat. Mengurangi cognitive load.

### 2. **Feedback (Umpan Balik)**

**Definisi**: Beri user feedback untuk setiap action mereka.

**Implementasi**:

- Loading state saat fetch data (spinner, skeleton)
- Success/error message setelah submit form
- Button hover state untuk show interactivity
- Disable button saat processing untuk prevent double click
- Toast notification untuk background actions

**Mengapa Penting**: User tahu action mereka diterima dan sedang diproses. Mengurangi anxiety dan confusion.

### 3. **Error Prevention (Pencegahan Error)**

**Definisi**: Prevent errors sebelum terjadi, bukan hanya handle setelah terjadi.

**Implementasi**:

- Validation real-time atau on blur
- Disable invalid action (button disabled sampai form valid)
- Confirmation dialog untuk destructive action (delete, logout)
- Clear input constraints (max length, format, required field)
- Provide undo/cancel options

**Mengapa Penting**: Error frustrating untuk user. Lebih baik prevent daripada cure.

### 4. **Progressive Disclosure (Pengungkapan Bertahap)**

**Definisi**: Tampilkan info essential dulu, reveal detail on demand.

**Implementasi**:

- Collapsible sections untuk detail info
- "Show more" button untuk long content
- Tooltip/popover untuk additional context
- Multi-step forms untuk complex input
- Tabs untuk organize related content

**Mengapa Penting**: Mengurangi overwhelm, user focus ke yang penting dulu.

### 5. **Mobile First**

**Definisi**: Design untuk mobile device dulu, scale up untuk larger screen.

**Implementasi**:

- Default styles untuk mobile (< 640px)
- Add complexity untuk larger screens dengan breakpoints
- Touch-friendly tap targets (min 44x44px)
- Thumb-friendly navigation (bottom navbar di mobile)
- Simplify mobile UI, enhance desktop UI

**Mengapa Penting**: Mayoritas traffic dari mobile. Lebih mudah enhance dari simple daripada simplify dari complex.

---

## Performance Guidelines

Performance adalah bagian dari UX. Slow = bad experience.

### Image Optimization

**Next.js Image component** untuk automatic optimization.

```tsx
import Image from "next/image";

{
  /* Always use Next.js Image component untuk optimization */
}
<Image
  src="/img/course.jpg"
  alt="Course thumbnail" // Alt text wajib untuk accessibility
  width={400}
  height={300}
  loading="lazy" // Lazy load untuk better performance
  className="rounded-lg"
/>;
```

**Benefits Next.js Image**:

- Automatic resize untuk device size
- Modern format (WebP, AVIF) jika browser support
- Lazy loading by default
- Prevent Cumulative Layout Shift (CLS)
- Optimized loading priority

**Tips**:

- Selalu specify width & height untuk prevent CLS
- Use appropriate image size (jangan load 4K untuk thumbnail)
- Compress images sebelum upload

### CSS Optimization

**Tailwind CSS** sudah optimize by default, tapi ada best practices.

- **Use Tailwind's purge**: Remove unused CSS (automatic di production)
- **Minimize custom CSS**: Leverage Tailwind utilities
- **Use JIT mode**: Just-In-Time compilation (faster builds)
- **Avoid @apply overuse**: Use utilities directly

### Animation Performance

**Animate properties** yang GPU-accelerated untuk smooth 60fps.

- **Prefer** `transform` dan `opacity` (GPU-accelerated)
- **Avoid** animating `width`, `height`, `top`, `left`, `margin` (slow, cause reflow)
- Use `will-change` sparingly (only untuk complex animations)
- Remove `will-change` after animation completes

**Example**:

```tsx
{
  /* ✅ Good - GPU accelerated */
}
<div className="transform hover:scale-105 transition-transform">
  Fast animation
</div>;

{
  /* ❌ Bad - causes reflow */
}
<div className="hover:w-full transition-all">Slow animation</div>;
```

---

## Design Checklist

Gunakan checklist ini sebelum merge/deploy untuk ensure quality standards.

### Visual Design

- [ ] Colors mengikuti design system (primary, semantic, grayscale)
- [ ] Typography konsisten (font size, weight, line height)
- [ ] Spacing menggunakan 4px baseline grid
- [ ] Border radius konsisten untuk element sejenis
- [ ] Shadow appropriate untuk element hierarchy

### Component Quality

- [ ] Components reusable dan well-structured
- [ ] Props typed dengan TypeScript
- [ ] States handled dengan baik (loading, error, success, empty)
- [ ] Semantic HTML digunakan (button, nav, main, aside)

### Responsive Design

- [ ] Tampil baik di semua breakpoints (mobile, tablet, desktop)
- [ ] Mobile-first approach digunakan
- [ ] Touch targets adequate di mobile (min 44x44px)
- [ ] Text readable tanpa horizontal scroll
- [ ] Images responsive dan optimized

### Accessibility

- [ ] Semantic HTML untuk structure
- [ ] ARIA labels untuk icon buttons dan complex interactions
- [ ] Keyboard navigation works (Tab, Enter, Space, Esc)
- [ ] Focus states visible dan clear
- [ ] Color contrast sufficient (WCAG AA: 4.5:1 untuk text)
- [ ] Alt text untuk images
- [ ] Form labels associated dengan inputs

### User Experience

- [ ] Loading states implemented untuk async operations
- [ ] Error states handled dengan helpful messages
- [ ] Success feedback provided (toast, alert, message)
- [ ] Empty states designed (no data, no results)
- [ ] Confirmation dialogs untuk destructive actions

### Performance

- [ ] Images optimized (Next.js Image component)
- [ ] Animations performant (transform/opacity)
- [ ] No unnecessary re-renders
- [ ] Code split dengan dynamic imports (jika perlu)
- [ ] Bundle size acceptable

### Code Quality

- [ ] No console errors atau warnings
- [ ] TypeScript types correct
- [ ] ESLint rules followed
- [ ] Code readable dan well-commented
- [ ] Reusable logic extracted ke hooks/utils

---

## Resources & Tools

### Design Tools

- **Figma**: Design mockups dan prototypes
- **Coolors**: Color palette generator
- **Material Design**: Color system reference
- **Tailwind CSS Docs**: Complete utility reference

### Accessibility Tools

- **WAVE**: Web accessibility evaluation tool
- **axe DevTools**: Browser extension untuk a11y testing
- **Lighthouse**: Chrome DevTools audit
- **Contrast Checker**: WCAG color contrast checker

### Testing Tools

- **Chrome DevTools**: Responsive design mode, performance profiling
- **React DevTools**: Component inspection
- **BrowserStack**: Cross-browser testing
- **Responsive Viewer**: Chrome extension

### Learning Resources

- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [Next.js Documentation](https://nextjs.org/docs)
- [WebAIM](https://webaim.org/) - Web accessibility guide
- [Laws of UX](https://lawsofux.com/) - UX principles
- [Refactoring UI](https://www.refactoringui.com/) - Design tips

---

**Happy Designing! 🎨✨**

Dokumentasi ini adalah living document. Update seiring project berkembang dan design system evolves.

---

[← Kembali: Coding Standards](development-guide-Coding-Standards.md) | [Selanjutnya: User Story Map →](project-planning-User-Story-Map.md)
