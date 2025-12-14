# Panduan Instalasi - Bengkel Koding Web V2

## Daftar Isi

1. [Prasyarat Sistem](#1-prasyarat-sistem)
2. [Instalasi Development Environment](#2-instalasi-development-environment)
3. [Clone dan Setup Project](#3-clone-dan-setup-project)
4. [Konfigurasi Environment](#4-konfigurasi-environment)
5. [Menjalankan Aplikasi](#5-menjalankan-aplikasi)
6. [Troubleshooting](#6-troubleshooting)

---

## 1. Prasyarat Sistem

### 1.1 Hardware Requirements

| Komponen     | Minimum           | Recommended        |
| ------------ | ----------------- | ------------------ |
| **CPU**      | Dual-core 2.0 GHz | Quad-core 2.5 GHz+ |
| **RAM**      | 4 GB              | 8 GB+              |
| **Storage**  | 5 GB free space   | 10 GB+ SSD         |
| **Internet** | 1 Mbps            | 5 Mbps+            |

### 1.2 Software Requirements

#### Sistem Operasi

- Windows 10/11
- macOS 10.15+
- Linux (Ubuntu 20.04+, Debian, Fedora)

#### Tools yang Diperlukan

- **Node.js**: Version 18.x atau lebih tinggi
- **Bun**: Package manager (recommended) atau npm/yarn
- **Git**: Version control
- **Code Editor**: VS Code (recommended), WebStorm, atau Sublime Text

---

## 2. Instalasi Development Environment

### 2.1 Install Node.js

#### Windows

1. Download installer dari [nodejs.org](https://nodejs.org/)
2. Pilih versi **LTS (18.x atau lebih tinggi)**
3. Jalankan installer dan ikuti instruksi
4. Restart terminal setelah instalasi

**Verifikasi instalasi**:

```bash
node --version
# Output: v18.x.x atau lebih tinggi

npm --version
# Output: 9.x.x atau lebih tinggi
```

#### macOS

**Menggunakan Homebrew** (recommended):

```bash
# Install Homebrew jika belum ada
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Node.js
brew install node@18
```

**Verifikasi**:

```bash
node --version
npm --version
```

#### Linux (Ubuntu/Debian)

```bash
# Update package manager
sudo apt update

# Install Node.js 18.x
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Verifikasi
node --version
npm --version
```

### 2.2 Install Bun (Package Manager)

Bun adalah JavaScript runtime yang sangat cepat dan digunakan sebagai package manager untuk project ini.

#### Windows

```bash
# Install via PowerShell (as Administrator)
powershell -c "irm bun.sh/install.ps1 | iex"
```

#### macOS / Linux

```bash
# Install via curl
curl -fsSL https://bun.sh/install | bash

# Restart terminal atau load profile
source ~/.bashrc  # atau ~/.zshrc untuk macOS
```

**Verifikasi**:

```bash
bun --version
# Output: 1.x.x
```

### 2.3 Install Git

#### Windows

1. Download dari [git-scm.com](https://git-scm.com/download/win)
2. Jalankan installer
3. Pilih opsi default atau customize sesuai kebutuhan
4. Restart terminal

#### macOS

```bash
# Install via Homebrew
brew install git
```

#### Linux

```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora
sudo dnf install git
```

**Verifikasi**:

```bash
git --version
# Output: git version 2.x.x
```

**Konfigurasi Git** (jika baru pertama kali):

```bash
git config --global user.name "Nama Anda"
git config --global user.email "email@anda.com"
```

### 2.4 Install Code Editor (VS Code)

1. Download dari [code.visualstudio.com](https://code.visualstudio.com/)
2. Install sesuai sistem operasi
3. **Recommended Extensions**:
   - ESLint
   - Prettier - Code formatter
   - Tailwind CSS IntelliSense
   - TypeScript and JavaScript Language Features
   - GitLens
   - Error Lens

**Install extensions via terminal**:

```bash
code --install-extension dbaeumer.vscode-eslint
code --install-extension esbenp.prettier-vscode
code --install-extension bradlc.vscode-tailwindcss
code --install-extension eamodio.gitlens
code --install-extension usernamehw.errorlens
```

---

## 3. Clone dan Setup Project

### 3.1 Clone Repository

```bash
# Clone dari GitHub
git clone https://github.com/bengkelkodingdev/bengkelkoding-web-v2.git

# Masuk ke folder project
cd bengkelkoding-web-v2

# Checkout ke branch development
git checkout development
```

### 3.2 Struktur Project

Setelah clone, struktur folder akan seperti ini:

```
bengkelkoding-web-v2/
├── app/                    # Next.js app directory
│   ├── (auth)/            # Authentication routes
│   ├── (landing)/         # Public pages
│   ├── (dashboard)/       # Protected routes
│   ├── api/               # API integration
│   ├── component/         # React components
│   ├── interface/         # TypeScript interfaces
│   ├── lib/               # Utility functions
│   ├── globals.css        # Global styles
│   └── layout.tsx         # Root layout
│
├── public/                 # Static assets
│
├── .gitignore             # Git ignore rules
├── bun.lockb              # Bun lock file
├── middleware.ts          # Next.js middleware
├── next.config.mjs        # Next.js config
├── package.json           # Dependencies
├── postcss.config.mjs     # PostCSS config
├── tailwind.config.ts     # Tailwind config
├── tsconfig.json          # TypeScript config
└── README.md              # Project readme
```

### 3.3 Install Dependencies

**Menggunakan Bun** (recommended):

```bash
bun install
```

**Menggunakan npm** (alternative):

```bash
npm install
```

**Menggunakan yarn** (alternative):

```bash
yarn install
```

Proses instalasi akan:

- ✅ Download semua package yang diperlukan
- ✅ Membuat `node_modules` folder
- ✅ Generate lock file (`bun.lockb` atau `package-lock.json`)

**Output yang diharapkan**:

```
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...

✨  Done in 15.32s
```

---

## 4. Konfigurasi Environment

### 4.1 File Environment Variables

Project menggunakan environment variables untuk konfigurasi. Buat file `.env.local` di root project:

```bash
# Copy dari example file
cp env.local.example .env.local
```

### 4.2 Konfigurasi .env.local

Buka file `.env.local` dan atur variabel berikut:

```env
# ==============================================
# API CONFIGURATION
# ==============================================
# URL Backend API (Development)
NEXT_PUBLIC_API_URL_BENGKEL_KODING=http://localhost:8080
NEXT_PUBLIC_GETAPICLASSROOM_URL_BENGKEL_KODING=http://localhost:8080

# URL Backend API (Production - jika diperlukan untuk testing)
# NEXT_PUBLIC_API_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id

# ==============================================
# AUTHENTICATION
# ==============================================
# Login with Google endpoint
NEXT_PUBLIC_API_LOGIN_WITH_GOOGLE=http://localhost:8080

# ==============================================
# IMAGE DOMAINS
# ==============================================
# Domain yang diizinkan untuk next/image
# Pisahkan dengan koma jika lebih dari satu
NEXT_PUBLIC_IMAGE_DOMAINS=bengkod-api.rayhanashlikh.my.id,mahasiswa.dinus.ac.id,simpeg.dinus.ac.id,localhost

# ==============================================
# RECAPTCHA (Google reCAPTCHA v2)
# ==============================================
# Get your keys from: https://www.google.com/recaptcha/admin
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=6LfczHkqAxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
RECAPTCHA_SECRET_KEY=6Lfcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# ==============================================
# OPTIONAL: Development Settings
# ==============================================
# Next.js development mode
NODE_ENV=development

# Port (default: 3000)
# PORT=3000
```

### 4.3 Environment Variables Penting

| Variable                             | Deskripsi               | Required |
| ------------------------------------ | ----------------------- | -------- |
| `NEXT_PUBLIC_API_URL_BENGKEL_KODING` | Base URL backend API    | ✅ Yes   |
| `NEXT_PUBLIC_IMAGE_DOMAINS`          | Domain untuk next/image | ✅ Yes   |
| `NEXT_PUBLIC_RECAPTCHA_SITE_KEY`     | reCAPTCHA site key      | ✅ Yes   |
| `RECAPTCHA_SECRET_KEY`               | reCAPTCHA secret key    | ✅ Yes   |

> **⚠️ PENTING**:
>
> - File `.env.local` **TIDAK** di-commit ke Git (sudah ada di `.gitignore`)
> - Jangan share secret keys ke public
> - Untuk production, gunakan environment variables management system

### 4.4 Mendapatkan reCAPTCHA Keys

1. Buka [Google reCAPTCHA Admin](https://www.google.com/recaptcha/admin)
2. Login dengan Google Account
3. Klik **"+"** untuk register site baru
4. Isi form:
   - **Label**: Bengkel Koding Development
   - **reCAPTCHA type**: v2 "I'm not a robot"
   - **Domains**: `localhost`
5. Submit dan copy **Site Key** dan **Secret Key**
6. Paste ke `.env.local`

---

## 5. Menjalankan Aplikasi

### 5.1 Development Mode

Jalankan development server:

**Menggunakan Bun**:

```bash
bun dev
```

**Menggunakan npm**:

```bash
npm run dev
```

**Output yang diharapkan**:

```
   ▲ Next.js 14.2.3
   - Local:        http://localhost:3000
   - Environments: .env.local

 ✓ Ready in 2.3s
 ○ Compiling / ...
 ✓ Compiled / in 1.2s
```

### 5.2 Akses Aplikasi

Buka browser dan akses:

```
http://localhost:3000
```

**Halaman yang tersedia**:

- 🏠 **Homepage**: `http://localhost:3000`
- 🔐 **Login**: `http://localhost:3000/masuk`
- 📚 **Kursus**: `http://localhost:3000/kursus`
- 🛤️ **Learning Path**: `http://localhost:3000/learning-path`
- 📊 **Dashboard**: `http://localhost:3000/dashboard/[role]`

[← Back to Wiki Structure](Wiki-Structure.md) | [Next: Error Handling →](development-guide-Error-Handling.md)
