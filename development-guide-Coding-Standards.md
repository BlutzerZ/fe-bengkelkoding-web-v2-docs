# Panduan Coding Standards Next.js TypeScript

## Overview

Dokumen ini berisi panduan coding standards dan best practices untuk proyek Bengkel Koding Web V2 yang dibangun dengan Next.js 14, TypeScript, dan Tailwind CSS.

**Tujuan Dokumen**:

- Menjaga konsistensi code di seluruh project
- Memudahkan developer baru untuk onboarding
- Meningkatkan code quality dan maintainability
- Mengurangi bugs dengan best practices

**Tech Stack Utama**:

- **Next.js 14**: React framework dengan App Router
- **TypeScript**: Type-safe JavaScript
- **Tailwind CSS**: Utility-first CSS framework
- **Bun**: Fast JavaScript runtime dan package manager

---

## Project Structure

### Directory Organization

**Struktur folder** mengikuti Next.js 14 App Router conventions dengan route groups untuk organize berdasarkan concern.

```
bengkelkoding-web-v2/
├── app/                          # Next.js App Router (semua routes di sini)
│   ├── (auth)/                  # Route group: Authentication (masuk, daftar, lupa password)
│   ├── (landing)/               # Route group: Public pages (home, about, courses)
│   ├── (dashboard)/             # Route group: Protected pages (dashboard per role)
│   ├── api/                     # API integration modules (fetch data dari backend)
│   ├── component/               # Shared components (reusable UI components)
│   ├── interface/               # TypeScript interfaces (type definitions)
│   ├── lib/                     # Utility functions (helper functions)
│   ├── globals.css              # Global styles (Tailwind directives, custom CSS)
│   └── layout.tsx               # Root layout (wrapper untuk semua pages)
│
├── public/                       # Static assets (accessible via /)
│   ├── img/                     # Images (photos, icons, illustrations)
│   ├── logo/                    # Logos (brand logos)
│   └── data/                    # Static data files (JSON, CSV)
│
├── middleware.ts                 # Next.js middleware (authentication, authorization)
├── next.config.mjs              # Next.js configuration (domains, env, etc)
├── tailwind.config.ts           # Tailwind configuration (colors, fonts, plugins)
├── tsconfig.json                # TypeScript configuration (compiler options)
└── package.json                 # Dependencies (libraries dan scripts)
```

**Penjelasan Route Groups**:

- `(auth)`: Routes untuk authentication, tidak tampil di URL
- `(landing)`: Public pages yang bisa diakses semua user
- `(dashboard)`: Protected pages yang require authentication

**Mengapa Struktur Ini?**:

- **Organized**: Grouping by concern, mudah find files
- **Scalable**: Easy to add new features tanpa mess up structure
- **Convention**: Follow Next.js best practices

---

## File Naming Conventions

### General Rules

**Naming convention** yang konsisten penting untuk:

- Mudah search dan find files
- Understand file purpose dari nama
- Avoid naming conflicts
- Follow industry standards

| File Type              | Convention                  | Example                           | Penjelasan                                     |
| ---------------------- | --------------------------- | --------------------------------- | ---------------------------------------------- |
| **React Components**   | PascalCase                  | `UserProfile.tsx`                 | Nama class-like, capitalize setiap kata        |
| **Pages (App Router)** | kebab-case                  | `page.tsx`, `not-found.tsx`       | Next.js convention, lowercase dengan dash      |
| **API Modules**        | camelCase                   | `auth.ts`, `courses.ts`           | Start lowercase, capitalize kata berikutnya    |
| **Interfaces**         | PascalCase                  | `User.ts`, `Course.ts`            | Sama seperti component, represent types        |
| **Utilities**          | camelCase                   | `formatDate.ts`, `getRolePath.ts` | Function-like naming                           |
| **Hooks**              | camelCase with `use` prefix | `useAuth.ts`, `useCourse.ts`      | React hook convention, always start with 'use' |
| **Constants**          | UPPER_SNAKE_CASE            | `API_ENDPOINTS.ts`                | All caps dengan underscore, for constants      |

### Examples

```typescript
// ✅ Good - mengikuti convention
app / component / general / Button.tsx; // Component: PascalCase
app / api / auth.ts; // API module: camelCase
app / interface / User.ts; // Interface: PascalCase
app / lib / formatDate.ts; // Utility: camelCase
app / hooks / useAuth.ts; // Hook: use + camelCase
app / constants / API_ENDPOINTS.ts; // Constant: UPPER_SNAKE_CASE

// ❌ Bad - tidak konsisten
app / component / general / button.tsx; // Should be Button.tsx
app / api / Auth.ts; // Should be auth.ts
app / interface / user.ts; // Should be User.ts
app / lib / format - date.ts; // Should be formatDate.ts
app / hooks / auth.ts; // Should be useAuth.ts
```

**Tips**:

- Gunakan extension `.tsx` untuk files dengan JSX
- Gunakan extension `.ts` untuk files tanpa JSX
- Nama file harus descriptive, hindari abbreviations
- Keep names short tapi meaningful

---

## Code Style Guide

### TypeScript

TypeScript memberikan **type safety** untuk prevent bugs dan improve developer experience dengan autocomplete.

#### 1. Type Definitions

**Interface vs Type**: Gunakan interface untuk object shapes, type untuk unions dan complex types.

```typescript
// ✅ Good - Interface untuk object shapes
interface User {
  id: number;
  name: string;
  email: string;
  role: "student" | "asisten" | "dosen" | "admin" | "superadmin";
}

// Mengapa interface? Bisa di-extend, lebih semantic untuk objects
interface AdminUser extends User {
  permissions: string[];
}

// ✅ Good - Type untuk unions dan complex types
type Status = "active" | "inactive" | "pending";

type ApiResponse<T> = {
  data: T;
  meta: {
    status: number;
    message: string;
  };
};

// Type bisa combine dengan union, intersection, dll
type UserOrNull = User | null;
type RequiredUser = Required<User>; // All properties required

// ❌ Bad - Tidak konsisten
type User = {
  // Should use interface
  id: number;
  name: string;
};

interface Status {
  // Should use type (untuk union)
  value: "active" | "inactive";
}
```

**Best Practices**:

- Interface: Object shapes, dapat di-extend
- Type: Unions, intersections, mapped types
- Gunakan generic (`<T>`) untuk reusable types
- Export types yang digunakan di multiple files

#### 2. Function Signatures

**Explicit return types** penting untuk type safety dan documentation.

```typescript
// ✅ Good - Explicit return types
export const fetchUser = async (id: number): Promise<User> => {
  const response = await api.get(`/users/${id}`);
  return response.data;
};

// ✅ Good - Type parameters untuk generics
// Generic function yang bisa return any type
export const fetchData = async <T>(url: string): Promise<T> => {
  const response = await api.get(url);
  return response.data;
};

// Usage:
const user = await fetchData<User>("/users/1");
const courses = await fetchData<Course[]>("/courses");

// ✅ Good - Optional parameters dengan default
export const createUser = (
  name: string,
  email: string,
  role: string = "student" // Default value
): User => {
  return { name, email, role };
};

// ❌ Bad - No return type (TypeScript infer, tapi not explicit)
export const fetchUser = async (id: number) => {
  const response = await api.get(`/users/${id}`);
  return response.data; // Type unclear untuk reader
};

// ❌ Bad - Any type (defeats purpose of TypeScript)
export const fetchUser = async (id: any): Promise<any> => {
  // Loses type safety
};
```

**Mengapa Penting**:

- **Autocomplete**: IDE bisa suggest properties
- **Error catching**: TypeScript catch errors sebelum runtime
- **Documentation**: Type sebagai inline documentation
- **Refactoring**: Easier to refactor dengan type safety

#### 3. Component Props

**Props interface** untuk type-safe components.

```typescript
// ✅ Good - Interface untuk component props
interface ButtonProps {
  label: string; // Required prop
  onClick: () => void; // Function prop
  variant?: "primary" | "secondary"; // Optional dengan union type
  disabled?: boolean; // Optional boolean
  children?: React.ReactNode; // Untuk child elements
  className?: string; // Additional styling
}

// React.FC (Function Component) dengan typed props
export const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  variant = "primary", // Default value
  disabled = false,
  className = "",
}) => {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant} ${className}`}
    >
      {label}
    </button>
  );
};

// ✅ Good - Extending HTML attributes
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
}

export const Input: React.FC<InputProps> = ({
  label,
  error,
  ...rest // Spread remaining HTML attributes
}) => {
  return (
    <div>
      <label>{label}</label>
      <input {...rest} />
      {error && <span className="error">{error}</span>}
    </div>
  );
};

// ❌ Bad - No props interface
export const Button = ({ label, onClick, variant, disabled }) => {
  // No type safety, no autocomplete
  // Typo in prop name tidak akan ketahuan
};

// ❌ Bad - Using 'any' for props
interface ButtonProps {
  onClick: any; // Should be () => void
  data: any; // Should have specific type
}
```

**Best Practices**:

- Always define props interface
- Use optional (`?`) untuk optional props
- Provide default values dalam destructuring
- Use union types untuk limited options
- Extend HTML attributes untuk native elements

---

### React Components

React components adalah building blocks aplikasi. Structure yang baik membuat code readable dan maintainable.

#### 1. Component Structure

**Struktur yang konsisten** memudahkan reading dan maintenance.

```typescript
// ✅ Good - Clean component structure
"use client"; // Hanya jika component butuh client-side features (useState, useEffect, event handlers)

// 1. External imports
import React, { useState, useEffect } from "react";
import { SomeComponent } from "@/app/component/SomeComponent";

// 2. Type imports (dengan 'type' keyword)
import type { User } from "@/app/interface/User";

// 3. Props interface
interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void; // Optional callback
}

// 4. Component definition
export const UserCard: React.FC<UserCardProps> = ({ user, onEdit }) => {
  // === COMPONENT BODY ===

  // A. State declarations
  const [isEditing, setIsEditing] = useState(false);
  const [isLoading, setIsLoading] = useState(false);

  // B. Effects (side effects, data fetching)
  useEffect(() => {
    // Fetch additional data, subscribe to events, etc
    console.log("User card mounted");

    return () => {
      // Cleanup function
      console.log("User card unmounted");
    };
  }, []); // Empty dependency = run once on mount

  // C. Event handlers
  const handleEdit = () => {
    setIsEditing(true);
    onEdit?.(user); // Optional chaining - call jika exist
  };

  const handleSave = async () => {
    setIsLoading(true);
    try {
      // Save logic here
      await saveUser(user);
    } catch (error) {
      console.error("Failed to save", error);
    } finally {
      setIsLoading(false);
    }
  };

  // D. Render helpers (computed values, conditional logic)
  const displayName = user.name.toUpperCase();
  const canEdit = user.role !== "student";

  // E. Return JSX
  return (
    <div className="user-card">
      <h3>{displayName}</h3>
      <p>{user.email}</p>

      {canEdit && onEdit && (
        <button onClick={handleEdit} disabled={isLoading}>
          {isLoading ? "Saving..." : "Edit"}
        </button>
      )}
    </div>
  );
};
```

**Order of Elements** (penting untuk consistency):

1. Directive (`'use client'` jika perlu)
2. Imports
3. Type definitions
4. Component function
5. Inside component: State → Effects → Handlers → Helpers → JSX

**Best Practices**:

- One component per file (unless small related components)
- Extract complex logic ke custom hooks
- Keep components small (<200 lines)
- Use meaningful variable names
- Comment complex logic

#### 2. Server vs Client Components

**Next.js 14** memiliki Server Components (default) dan Client Components. Pilih yang sesuai use case.

```typescript
// ✅ Good - Server Component (default, NO 'use client')
// app/courses/page.tsx
import { fetchCourses } from "@/app/api/courses";

// Server Component bisa async
export default async function CoursesPage() {
  // Fetch data langsung di server (fast, SEO-friendly)
  const courses = await fetchCourses();

  return (
    <div>
      <h1>Courses</h1>
      {courses.map((course) => (
        <CourseCard key={course.id} course={course} />
      ))}
    </div>
  );
}

// ✅ Good - Client Component (dengan 'use client')
// app/component/CourseCard.tsx
("use client");

import { useState } from "react";

export const CourseCard = ({ course }) => {
  // Client component untuk interactivity
  const [enrolled, setEnrolled] = useState(false);

  const handleEnroll = () => {
    setEnrolled(true);
    // Call API to enroll
  };

  return (
    <div className={enrolled ? "enrolled" : ""} onClick={handleEnroll}>
      <h3>{course.title}</h3>
      <p>{course.description}</p>
      {enrolled && <span>✓ Enrolled</span>}
    </div>
  );
};
```

**Kapan Gunakan Server Component**:

- ✅ Fetch data dari database/API
- ✅ Access backend resources (files, env variables)
- ✅ Keep sensitive data di server (API keys)
- ✅ Large dependencies yang tidak perlu di client
- ✅ SEO-friendly pages (content rendered di server)

**Kapan Gunakan Client Component**:

- ✅ Use React hooks (useState, useEffect, useContext)
- ✅ Event handlers (onClick, onChange, onSubmit)
- ✅ Browser APIs (localStorage, window, document)
- ✅ Interactive UI (forms, modals, tooltips)
- ✅ Custom hooks

**Tips**:

- Default: Server Component (faster, better SEO)
- Add `'use client'` only when needed
- Nest Client Components inside Server Components OK
- Cannot nest Server inside Client (akan jadi Client)

---

### API Integration

API integration adalah cara aplikasi berkomunikasi dengan backend. Organize dengan baik untuk maintainability.

#### 1. API Module Structure

**Centralized API calls** di folder `app/api/` untuk easy maintenance dan reusability.

```typescript
// app/api/courses.ts
import { createRequest, createPostRequest } from "./request";
import type { Course, CourseResponse } from "@/app/interface/Course";

const API_URL = process.env.NEXT_PUBLIC_API_URL_BENGKEL_KODING || "";

/**
 * Fetch all courses
 * @returns Promise<CourseResponse>
 */
export const fetchCourses = async (): Promise<CourseResponse> => {
  try {
    const response = await createRequest("/api/v1/courses");
    return response.data;
  } catch (error) {
    console.error("Error fetching courses:", error);
    throw error;
  }
};

/**
 * Fetch single course by ID
 * @param id - Course ID
 * @returns Promise<Course>
 */
export const fetchCourseById = async (id: number): Promise<Course> => {
  try {
    const response = await createRequest(`/api/v1/courses/${id}`);
    return response.data;
  } catch (error) {
    console.error(`Error fetching course ${id}:`, error);
    throw error;
  }
};

/**
 * Create a new course
 * @param courseData - Course data to create
 * @returns Promise<Course>
 */
export const createCourse = async (
  courseData: Partial<Course>
): Promise<Course> => {
  try {
    const response = await createPostRequest("/api/v1/courses", courseData);
    return response.data;
  } catch (error) {
    console.error("Error creating course:", error);
    throw error;
  }
};

/**
 * Update existing course
 * @param id - Course ID
 * @param courseData - Updated course data
 * @returns Promise<Course>
 */
export const updateCourse = async (
  id: number,
  courseData: Partial<Course>
): Promise<Course> => {
  try {
    const response = await createPostRequest(
      `/api/v1/courses/${id}`,
      courseData
    );
    return response.data;
  } catch (error) {
    console.error(`Error updating course ${id}:`, error);
    throw error;
  }
};

/**
 * Delete course
 * @param id - Course ID
 * @returns Promise<void>
 */
export const deleteCourse = async (id: number): Promise<void> => {
  try {
    await createRequest(`/api/v1/courses/${id}/delete`);
  } catch (error) {
    console.error(`Error deleting course ${id}:`, error);
    throw error;
  }
};
```

**Best Practices**:

- **One file per resource** (courses.ts, users.ts, auth.ts)
- **JSDoc comments** untuk documentation
- **Explicit return types** untuk type safety
- **Error logging** dengan context
- **Named exports** (bukan default export)
- **Consistent naming**: fetch, create, update, delete

**Mengapa Centralized?**:

- Single source of truth untuk API calls
- Easy to mock untuk testing
- Easy to add interceptors, retry logic
- Type-safe with TypeScript
- Reusable across components

#### 2. Error Handling

**Comprehensive error handling** untuk better user experience.

```typescript
// ✅ Good - Comprehensive error handling
export const fetchUserData = async (userId: number): Promise<User> => {
  try {
    const response = await createRequest(`/api/v1/users/${userId}`);
    return response.data;
  } catch (error: any) {
    // Error bisa dari berbagai source

    if (error.response) {
      // Server responded dengan error status (4xx, 5xx)
      const status = error.response.status;
      const message = error.response.data?.meta?.message || "Server error";

      console.error(`API Error [${status}]:`, message);

      // Handle specific status codes
      if (status === 401) {
        // Unauthorized - redirect ke login
        throw {
          message: "Session expired. Please login again.",
          status: 401,
          shouldRedirect: "/masuk",
        };
      } else if (status === 404) {
        throw {
          message: "User not found.",
          status: 404,
        };
      } else if (status === 500) {
        throw {
          message: "Server error. Please try again later.",
          status: 500,
        };
      }

      throw {
        message,
        status,
      };
    } else if (error.request) {
      // Request dibuat tapi no response (network issue)
      console.error("Network Error:", error.request);
      throw {
        message: "Network error. Please check your connection.",
        status: 0,
      };
    } else {
      // Something wrong saat setup request
      console.error("Request Error:", error.message);
      throw {
        message: "An unexpected error occurred.",
        status: -1,
      };
    }
  }
};

// ✅ Good - With retry logic
export const fetchUserDataWithRetry = async (
  userId: number,
  maxRetries: number = 3
): Promise<User> => {
  let lastError: any;

  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fetchUserData(userId);
    } catch (error) {
      lastError = error;

      // Don't retry for client errors (4xx)
      if (error.status >= 400 && error.status < 500) {
        throw error;
      }

      // Wait before retry (exponential backoff)
      if (i < maxRetries - 1) {
        const delay = Math.pow(2, i) * 1000; // 1s, 2s, 4s
        await new Promise((resolve) => setTimeout(resolve, delay));
      }
    }
  }

  throw lastError;
};

// Usage dalam component
const UserProfile = ({ userId }: { userId: number }) => {
  const [user, setUser] = useState<User | null>(null);
  const [error, setError] = useState<string>("");
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const loadUser = async () => {
      try {
        setLoading(true);
        setError("");

        const userData = await fetchUserData(userId);
        setUser(userData);
      } catch (err: any) {
        setError(err.message || "Failed to load user");

        // Handle redirect jika perlu
        if (err.shouldRedirect) {
          window.location.href = err.shouldRedirect;
        }
      } finally {
        setLoading(false);
      }
    };

    loadUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div className="error">{error}</div>;
  if (!user) return <div>No user found</div>;

  return <div>{user.name}</div>;
};
```

**Error Handling Levels**:

1. **API Layer**: Catch dan transform errors
2. **Component Layer**: Display errors ke user
3. **Global Layer**: Handle uncaught errors (Error Boundary)

**Best Practices**:

- Always use try-catch untuk async operations
- Log errors dengan context untuk debugging
- Provide user-friendly error messages
- Handle different error types (network, server, validation)
- Consider retry logic untuk transient errors

---

### Styling dengan Tailwind CSS

Tailwind CSS adalah utility-first CSS framework. Compose styles dengan utility classes alih-alih menulis custom CSS.

#### 1. Class Organization

**Organize classes** untuk readability. Group by concern: layout, spacing, colors, effects.

```typescript
// ✅ Good - Organized dan readable
<div className="
  /* Layout */
  flex items-center justify-between

  /* Spacing */
  p-4 rounded-lg

  /* Colors & Background */
  bg-white hover:bg-gray-50
  border border-gray-200

  /* Effects */
  transition-colors duration-200
  shadow-sm hover:shadow-md
">
  <h3 className="
    /* Typography */
    text-lg font-semibold
    text-gray-900
  ">
    Title
  </h3>

  <button className="
    /* Spacing */
    px-4 py-2

    /* Colors */
    bg-blue-600 hover:bg-blue-700
    text-white font-medium

    /* Shape */
    rounded-md

    /* Effects */
    transition-colors
    disabled:opacity-50 disabled:cursor-not-allowed
  ">
    Action
  </button>
</div>

// ❌ Bad - Unorganized, sulit dibaca
<div className="flex p-4 bg-white border rounded-lg items-center justify-between border-gray-200 hover:bg-gray-50 transition-colors duration-200">
  {/* All classes dalam satu line, sulit scan */}
</div>

// ❌ Bad - Random order
<div className="hover:bg-gray-50 flex border-gray-200 rounded-lg p-4 items-center bg-white justify-between border">
  {/* No logical grouping */}
</div>
```

**Grouping Order** (recommended):

1. **Layout**: flex, grid, block, inline
2. **Positioning**: relative, absolute, top, left
3. **Sizing**: w-_, h-_, max-w-\*
4. **Spacing**: p-_, m-_, gap-\*
5. **Typography**: text-_, font-_, leading-\*
6. **Colors**: bg-_, text-_, border-\*
7. **Effects**: transition-_, shadow-_, opacity-\*
8. **States**: hover:_, focus:_, active:\*

**Tips**:

- Break long className ke multiple lines
- Use comments untuk separate groups (optional)
- Consistent order di seluruh project

#### 2. Reusable Styles

**Extract repeated patterns** ke constants atau components untuk DRY (Don't Repeat Yourself).

```typescript
// ✅ Good - Style constants untuk repeated patterns
const buttonStyles = {
  base: "px-4 py-2 rounded-md font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2",
  primary: "bg-blue-600 hover:bg-blue-700 text-white focus:ring-blue-500",
  secondary: "bg-gray-200 hover:bg-gray-300 text-gray-900 focus:ring-gray-500",
  danger: "bg-red-600 hover:bg-red-700 text-white focus:ring-red-500",
  outline: "border-2 border-blue-600 text-blue-600 hover:bg-blue-50 focus:ring-blue-500",
};

// Usage
<button className={`${buttonStyles.base} ${buttonStyles.primary}`}>
  Submit
</button>

<button className={`${buttonStyles.base} ${buttonStyles.secondary}`}>
  Cancel
</button>

// ✅ Better - Reusable Button component
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger' | 'outline';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  children,
  onClick,
  disabled
}) => {
  const baseClasses = "px-4 py-2 rounded-md font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2";

  const variantClasses = {
    primary: "bg-blue-600 hover:bg-blue-700 text-white focus:ring-blue-500",
    secondary: "bg-gray-200 hover:bg-gray-300 text-gray-900 focus:ring-gray-500",
    danger: "bg-red-600 hover:bg-red-700 text-white focus:ring-red-500",
    outline: "border-2 border-blue-600 text-blue-600 hover:bg-blue-50 focus:ring-blue-500",
  };

  return (
    <button
      className={`${baseClasses} ${variantClasses[variant]} ${disabled ? 'opacity-50 cursor-not-allowed' : ''}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};

// Usage - much cleaner
<Button variant="primary" onClick={handleSubmit}>
  Submit
</Button>

<Button variant="secondary" onClick={handleCancel}>
  Cancel
</Button>

// ✅ Good - Using clsx/classnames library untuk conditional classes
import clsx from 'clsx';

<button
  className={clsx(
    'px-4 py-2 rounded-md font-medium',
    {
      'bg-blue-600 text-white': variant === 'primary',
      'bg-gray-200 text-gray-900': variant === 'secondary',
      'opacity-50 cursor-not-allowed': disabled,
    }
  )}
>
  Button
</button>
```

**When to Extract**:

- Pattern digunakan 3+ times → Extract ke constant
- Pattern digunakan 5+ times → Create component
- Complex conditional logic → Use clsx/classnames

**Benefits**:

- DRY principle - no duplication
- Easier to update styles (single source)
- Consistent styling across app
- Type-safe dengan TypeScript

#### 3. Responsive Design

**Mobile-first approach**: Default styles untuk mobile, add breakpoints untuk larger screens.

```typescript
// ✅ Good - Mobile-first responsive
<div className="
  /* Mobile (default, < 640px) */
  flex flex-col gap-2 p-4

  /* Tablet (md: 768px) */
  md:flex-row md:gap-4 md:p-6

  /* Desktop (lg: 1024px) */
  lg:gap-6 lg:p-8

  /* Large Desktop (xl: 1280px) */
  xl:max-w-7xl xl:mx-auto
">
  Content
</div>

// ✅ Good - Responsive grid
<div className="
  grid gap-4
  grid-cols-1
  sm:grid-cols-2
  md:grid-cols-3
  lg:grid-cols-4
  xl:grid-cols-5
">
  {items.map(item => <Card key={item.id} {...item} />)}
</div>

// ✅ Good - Responsive text
<h1 className="
  text-2xl
  sm:text-3xl
  md:text-4xl
  lg:text-5xl
  font-bold
">
  Responsive Heading
</h1>

// ✅ Good - Show/hide on different screens
<div>
  {/* Mobile navigation */}
  <nav className="block md:hidden">
    Mobile Nav
  </nav>

  {/* Desktop navigation */}
  <nav className="hidden md:block">
    Desktop Nav
  </nav>
</div>
```

**Tailwind Breakpoints**:

- `sm`: 640px (mobile landscape)
- `md`: 768px (tablet)
- `lg`: 1024px (desktop)
- `xl`: 1280px (large desktop)
- `2xl`: 1536px (extra large)

**Best Practices**:

- Design mobile-first
- Test di berbagai screen sizes
- Use Chrome DevTools responsive mode
- Consider touch-friendly sizes di mobile (min 44x44px)

---

## Authentication & Authorization

Authentication (siapa user) dan Authorization (apa yang boleh diakses) adalah critical untuk security.

### Middleware Pattern

**Next.js Middleware** runs sebelum request completed, perfect untuk authentication check.

```typescript
// middleware.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

/**
 * Middleware untuk authentication dan authorization
 * Runs di setiap request ke protected routes
 */
export function middleware(request: NextRequest) {
  // 1. Get authentication credentials dari cookies
  const accessToken = request.cookies.get("access_token")?.value;
  const role = request.cookies.get("user_role")?.value;

  // 2. Check authentication - harus ada token dan role
  if (!accessToken || !role) {
    console.log("No credentials found, redirecting to login");
    return NextResponse.redirect(new URL("/masuk", request.url));
  }

  // 3. Validate JWT token
  try {
    // Decode JWT payload (part antara dua dots)
    const payload = JSON.parse(
      Buffer.from(accessToken.split(".")[1], "base64").toString()
    );

    // 4. Check token expiry
    const currentTime = Math.floor(Date.now() / 1000);
    if (payload.exp < currentTime) {
      console.log("Token expired");
      throw new Error("Token expired");
    }

    // 5. Verify role matches payload
    if (payload.role !== role) {
      console.log("Role mismatch");
      throw new Error("Role mismatch");
    }

    // 6. Check authorization - apakah role ini boleh akses path ini?
    const allowedPaths = getRoleAllowedPaths(role);
    const currentPath = request.nextUrl.pathname;

    if (!isPathAllowed(currentPath, allowedPaths)) {
      console.log(`Role ${role} not authorized for ${currentPath}`);
      return NextResponse.redirect(new URL("/unauthorized", request.url));
    }

    // 7. All checks passed - continue request
    return NextResponse.next();
  } catch (error) {
    // Token invalid atau expired - clear cookies dan redirect
    console.error("Authentication failed:", error);

    const response = NextResponse.redirect(new URL("/masuk", request.url));
    response.cookies.delete("access_token");
    response.cookies.delete("user_role");

    return response;
  }
}

/**
 * Get allowed paths untuk setiap role
 */
function getRoleAllowedPaths(role: string): string[] {
  const pathMap: Record<string, string[]> = {
    student: ["/dashboard/student"],
    asisten: ["/dashboard/asisten"],
    dosen: ["/dashboard/dosen"],
    admin: ["/dashboard/admin"],
    superadmin: ["/dashboard/superadmin"],
  };

  return pathMap[role] || [];
}

/**
 * Check apakah path diizinkan untuk role
 */
function isPathAllowed(path: string, allowedPaths: string[]): boolean {
  return allowedPaths.some((allowedPath) => path.startsWith(allowedPath));
}

// Configure which routes middleware applies to
export const config = {
  matcher: [
    "/dashboard/:path*", // All dashboard routes
    // Add more protected routes here
  ],
};
```

**Flow Authentication**:

1. User login → Server return JWT + role
2. JWT disimpan di HTTP-only cookie (secure)
3. Every request ke protected route → Middleware check token
4. Token valid → Continue
5. Token invalid/expired → Redirect to login

**Best Practices**:

- Use HTTP-only cookies (prevent XSS)
- Always validate token expiry
- Check both authentication (valid user) dan authorization (allowed access)
- Clear cookies on logout
- Use secure cookies di production (HTTPS only)

**Security Notes**:

- Never store sensitive data di localStorage (vulnerable to XSS)
- JWT should have expiration (biasanya 1-24 hours)
- Refresh token strategy untuk long-lived sessions
- Rate limiting untuk prevent brute force

---

## Testing Guidelines

Testing memastikan code works as expected dan prevent regressions. Write tests untuk critical functionality.

### Component Testing

**Test user-facing behavior**, bukan implementation details.

```typescript
// __tests__/components/Button.test.tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { Button } from "@/app/component/general/Button";

describe("Button Component", () => {
  // Test 1: Rendering
  it("renders with correct label", () => {
    render(<Button label="Click me" onClick={() => {}} />);

    // Check if button dengan text "Click me" ada di document
    expect(screen.getByText("Click me")).toBeInTheDocument();
  });

  // Test 2: User interaction
  it("calls onClick when clicked", () => {
    // Mock function untuk track calls
    const handleClick = jest.fn();

    render(<Button label="Click me" onClick={handleClick} />);

    // Simulate user click
    fireEvent.click(screen.getByText("Click me"));

    // Verify function dipanggil 1 kali
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  // Test 3: Different states
  it("is disabled when disabled prop is true", () => {
    render(<Button label="Click me" onClick={() => {}} disabled />);

    const button = screen.getByText("Click me");

    // Verify button disabled
    expect(button).toBeDisabled();

    // Verify button tidak bisa di-click
    fireEvent.click(button);
    // onClick should not be called
  });

  // Test 4: Different variants
  it("applies correct styles for primary variant", () => {
    render(<Button label="Primary" onClick={() => {}} variant="primary" />);

    const button = screen.getByText("Primary");

    // Check if has correct class
    expect(button).toHaveClass("bg-blue-600");
  });

  // Test 5: Loading state
  it("shows loading text when loading", () => {
    render(<Button label="Submit" onClick={() => {}} loading />);

    expect(screen.getByText("Loading...")).toBeInTheDocument();
  });
});
```

**Testing Best Practices**:

- **Test behavior**, bukan implementation
- Use `screen.getByRole` over `getByTestId` (more semantic)
- Test user interactions (click, type, submit)
- Test different states (loading, error, success, disabled)
- Mock API calls dengan jest.fn()
- Keep tests simple dan focused (one concept per test)

**What to Test**:

- ✅ User interactions work
- ✅ Conditional rendering (show/hide based on state)
- ✅ Props affect behavior correctly
- ✅ Error states handled
- ❌ Implementation details (internal state, private methods)
- ❌ Styling (unless critical for functionality)

### API Testing

```typescript
// __tests__/api/courses.test.ts
import { fetchCourses, createCourse } from "@/app/api/courses";
import { createRequest, createPostRequest } from "@/app/api/request";

// Mock API request functions
jest.mock("@/app/api/request");

describe("Courses API", () => {
  beforeEach(() => {
    // Clear mocks sebelum each test
    jest.clearAllMocks();
  });

  describe("fetchCourses", () => {
    it("returns courses on success", async () => {
      // Setup mock response
      const mockCourses = [
        { id: 1, title: "Course 1" },
        { id: 2, title: "Course 2" },
      ];

      (createRequest as jest.Mock).mockResolvedValue({
        data: mockCourses,
      });

      // Call function
      const result = await fetchCourses();

      // Verify
      expect(result).toEqual(mockCourses);
      expect(createRequest).toHaveBeenCalledWith("/api/v1/courses");
    });

    it("throws error on failure", async () => {
      // Setup mock error
      const mockError = new Error("Network error");
      (createRequest as jest.Mock).mockRejectedValue(mockError);

      // Call dan expect error
      await expect(fetchCourses()).rejects.toThrow("Network error");
    });
  });

  describe("createCourse", () => {
    it("creates course successfully", async () => {
      const newCourse = { title: "New Course", description: "Description" };
      const mockResponse = { id: 3, ...newCourse };

      (createPostRequest as jest.Mock).mockResolvedValue({
        data: mockResponse,
      });

      const result = await createCourse(newCourse);

      expect(result).toEqual(mockResponse);
      expect(createPostRequest).toHaveBeenCalledWith(
        "/api/v1/courses",
        newCourse
      );
    });
  });
});
```

**Running Tests**:

```bash
# Run all tests
bun test

# Run tests in watch mode (re-run on file changes)
bun test --watch

# Run tests dengan coverage report
bun test --coverage

# Run specific test file
bun test Button.test.tsx
```

---

## State Management dengan React Hooks

**React hooks** (built-in Next.js/React) untuk manage component state. Sederhana, powerful, dan tidak perlu external library.

### useState - Local Component State

**useState** untuk state yang hanya dibutuhkan dalam satu component.

```typescript
"use client";

import { useState } from "react";

export const Counter = () => {
  // Declare state dengan initial value
  const [count, setCount] = useState(0);
  const [isActive, setIsActive] = useState(false);

  // Update state dengan setter function
  const increment = () => {
    setCount(count + 1);

    // Atau dengan functional update (recommended untuk based on previous)
    setCount((prevCount) => prevCount + 1);
  };

  const decrement = () => {
    setCount((prevCount) => prevCount - 1);
  };

  const toggle = () => {
    setIsActive((prevActive) => !prevActive);
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>

      <div>
        Status: {isActive ? "Active" : "Inactive"}
        <button onClick={toggle}>Toggle</button>
      </div>
    </div>
  );
};
```

**Kapan Gunakan useState**:

- ✅ Form inputs (controlled components)
- ✅ UI state (modal open/close, tab active)
- ✅ Local data yang tidak perlu shared
- ✅ Toggle states (show/hide, enabled/disabled)

**Best Practices**:

- Use functional updates jika based on previous state
- Don't mutate state directly (always use setter)
- Initialize dengan correct type
- Keep state as local as possible

### useEffect - Side Effects

**useEffect** untuk side effects (data fetching, subscriptions, DOM manipulation).

```typescript
"use client";

import { useState, useEffect } from "react";

export const UserProfile = ({ userId }: { userId: number }) => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string>("");

  // Effect runs setelah component render
  useEffect(() => {
    // Fetch data saat component mount atau userId berubah
    const fetchUser = async () => {
      try {
        setLoading(true);
        setError("");

        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();

        setUser(data);
      } catch (err) {
        setError("Failed to load user");
      } finally {
        setLoading(false);
      }
    };

    fetchUser();
  }, [userId]); // Dependency array - re-run jika userId berubah

  // Cleanup function (optional)
  useEffect(() => {
    // Setup: Subscribe ke event
    const handleResize = () => console.log("Window resized");
    window.addEventListener("resize", handleResize);

    // Cleanup: Unsubscribe saat unmount
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // Empty array = run once on mount

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return <div>{user.name}</div>;
};
```

**Dependency Array**:

- `[]` - Run once on mount (component load pertama kali)
- `[dep1, dep2]` - Run on mount dan when dependencies berubah
- No array - Run after every render (rarely needed, bisa cause infinite loop)

**Kapan Gunakan useEffect**:

- ✅ Data fetching
- ✅ Subscriptions (WebSocket, event listeners)
- ✅ DOM manipulation
- ✅ Timers (setTimeout, setInterval)
- ✅ Side effects yang perlu cleanup

### useContext - Shared State

**useContext** untuk share state across multiple components tanpa prop drilling.

```typescript
// 1. Create Context
// app/context/AuthContext.tsx
"use client";

import { createContext, useContext, useState, ReactNode } from "react";

interface User {
  id: number;
  name: string;
  role: string;
}

interface AuthContextType {
  user: User | null;
  isAuthenticated: boolean;
  login: (user: User) => void;
  logout: () => void;
}

// Create context dengan default value
const AuthContext = createContext<AuthContextType | undefined>(undefined);

// Provider component
export const AuthProvider = ({ children }: { children: ReactNode }) => {
  const [user, setUser] = useState<User | null>(null);

  const login = (userData: User) => {
    setUser(userData);
    // Save to localStorage, cookies, etc
  };

  const logout = () => {
    setUser(null);
    // Clear storage
  };

  const value = {
    user,
    isAuthenticated: user !== null,
    login,
    logout,
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
};

// Custom hook untuk consume context
export const useAuth = () => {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
};

// 2. Wrap app dengan Provider
// app/layout.tsx
import { AuthProvider } from "./context/AuthContext";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <AuthProvider>{children}</AuthProvider>
      </body>
    </html>
  );
}

// 3. Use context dalam any component
// app/component/UserMenu.tsx
("use client");

import { useAuth } from "@/app/context/AuthContext";

export const UserMenu = () => {
  const { user, isAuthenticated, logout } = useAuth();

  if (!isAuthenticated) {
    return <a href="/masuk">Login</a>;
  }

  return (
    <div>
      <span>Hello, {user.name}</span>
      <button onClick={logout}>Logout</button>
    </div>
  );
};
```

**Kapan Gunakan useContext**:

- ✅ Theme (dark/light mode)
- ✅ Authentication state
- ✅ User preferences
- ✅ Language/localization
- ✅ Data yang dibutuhkan banyak components
- ❌ Frequently changing data (dapat cause re-renders)
- ❌ State yang hanya dibutuhkan 1-2 components (use props)

### Custom Hooks - Reusable Logic

**Custom hooks** untuk extract dan reuse stateful logic.

```typescript
// app/hooks/useFetch.ts
"use client";

import { useState, useEffect } from "react";

interface UseFetchResult<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
  refetch: () => void;
}

/**
 * Custom hook untuk data fetching dengan loading dan error states
 */
export const useFetch = <T>(url: string): UseFetchResult<T> => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);
  const [refetchTrigger, setRefetchTrigger] = useState(0);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch(url);
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err instanceof Error ? err.message : "An error occurred");
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url, refetchTrigger]);

  const refetch = () => {
    setRefetchTrigger((prev) => prev + 1);
  };

  return { data, loading, error, refetch };
};

// Usage
export const CourseList = () => {
  const {
    data: courses,
    loading,
    error,
    refetch,
  } = useFetch<Course[]>("/api/courses");

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      {courses?.map((course) => (
        <div key={course.id}>{course.title}</div>
      ))}
    </div>
  );
};
```

**Custom Hook Examples**:

```typescript
// useLocalStorage - sync state dengan localStorage
const useLocalStorage = (key: string, initialValue: any) => {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
};

// useDebounce - delay state updates
const useDebounce = (value: string, delay: number) => {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
};

// useWindowSize - track window dimensions
const useWindowSize = () => {
  const [size, setSize] = useState({ width: 0, height: 0 });

  useEffect(() => {
    const handleResize = () => {
      setSize({ width: window.innerWidth, height: window.innerHeight });
    };

    handleResize();
    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
};
```

**Custom Hook Benefits**:

- ✅ Reusable logic across components
- ✅ Cleaner component code
- ✅ Easier testing (test hook independently)
- ✅ Share stateful logic tanpa HOC atau render props

**Best Practices**:

- Name starts dengan "use" (React convention)
- Return array untuk positional values `[value, setValue]`
- Return object untuk named values `{ data, loading, error }`
- Keep hooks focused (single responsibility)
- Document complex hooks dengan JSDoc

---

## Environment Setup

### Development Environment

**Setup local development** untuk start coding.

```bash
# 1. Install dependencies dengan Bun (fast package manager)
bun install

# 2. Run development server (dengan hot reload)
bun dev
# App akan available di http://localhost:3000

# 3. Build untuk production (optimize dan minify)
bun build
# Creates optimized production build di .next/

# 4. Start production server (setelah build)
bun start
# Run production build locally untuk testing

# 5. Lint code (check untuk errors dan style issues)
bun lint
# Fix auto-fixable issues
bun lint --fix

# 6. Type check (verify TypeScript types tanpa build)
bun run type-check

# 7. Format code dengan Prettier (jika configured)
bun run format
```

**Development Scripts** (dari package.json):

- `dev`: Start dev server dengan hot reload
- `build`: Build production-ready app
- `start`: Serve production build
- `lint`: Run ESLint untuk find issues
- `type-check`: Check TypeScript types

### Environment Variables

**Environment variables** untuk configuration yang berbeda per environment (dev, staging, production).

#### Local Development (.env.local)

```env
# API Configuration
NEXT_PUBLIC_API_URL_BENGKEL_KODING=http://localhost:8080
NEXT_PUBLIC_GETAPICLASSROOM_URL_BENGKEL_KODING=http://localhost:8080
NEXT_PUBLIC_API_LOGIN_WITH_GOOGLE=http://localhost:8080

# Image Configuration
NEXT_PUBLIC_IMAGE_DOMAIN_1=localhost
NEXT_PUBLIC_IMAGE_DOMAIN_2=127.0.0.1
NEXT_PUBLIC_IMG_ASSET_URL_BENGKEL_KODING=http://localhost:8080/storage

# Google reCAPTCHA (for forms)
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=your_site_key_here
RECAPTCHA_SECRET_KEY=your_secret_key_here

# Environment
NODE_ENV=development
```

#### Production (.env.production)

```env
# API Configuration (production URLs)
NEXT_PUBLIC_API_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id
NEXT_PUBLIC_GETAPICLASSROOM_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id
NEXT_PUBLIC_API_LOGIN_WITH_GOOGLE=https://api.bengkelkoding.dinus.ac.id

# Image Configuration (production domains)
NEXT_PUBLIC_IMAGE_DOMAIN_1=api.bengkelkoding.dinus.ac.id
NEXT_PUBLIC_IMAGE_DOMAIN_2=bengkelkoding.dinus.ac.id
NEXT_PUBLIC_IMG_ASSET_URL_BENGKEL_KODING=https://api.bengkelkoding.dinus.ac.id/storage

# Google reCAPTCHA (production keys)
NEXT_PUBLIC_RECAPTCHA_SITE_KEY=production_site_key
RECAPTCHA_SECRET_KEY=production_secret_key

# Environment
NODE_ENV=production
```

**Environment Variable Naming**:

- `NEXT_PUBLIC_*`: Exposed ke browser (client-side accessible)
- Tanpa prefix: Server-only (tidak exposed ke browser)

**Security Notes**:

- ⚠️ **Never commit** `.env.local` ke Git
- ✅ Commit `.env.local.example` as template
- ✅ Store secrets di secure location (1Password, AWS Secrets Manager)
- ⚠️ Variables dengan `NEXT_PUBLIC_` visible di browser source
- ✅ Keep sensitive keys (database, API secrets) tanpa `NEXT_PUBLIC_`

**Using Environment Variables**:

```typescript
// Client-side (dengan NEXT_PUBLIC_)
const apiUrl = process.env.NEXT_PUBLIC_API_URL_BENGKEL_KODING;

// Server-side (semua variables accessible)
const secretKey = process.env.RECAPTCHA_SECRET_KEY;

// Type-safe environment variables
interface Env {
  NEXT_PUBLIC_API_URL_BENGKEL_KODING: string;
  RECAPTCHA_SECRET_KEY: string;
}

declare global {
  namespace NodeJS {
    interface ProcessEnv extends Env {}
  }
}
```

### Git Workflow

**Branch strategy** untuk organized development:

```bash
# 1. Create feature branch dari development
git checkout development
git pull origin development
git checkout -b feature/user-profile

# 2. Work on feature, commit often dengan clear messages
git add .
git commit -m "feat: add user profile component"
git commit -m "fix: resolve profile image loading issue"

# 3. Push branch ke remote
git push origin feature/user-profile

# 4. Create Pull Request (PR) di GitHub
# - Title: Clear description
# - Description: What changed, why, how to test
# - Request review dari team

# 5. After PR approved dan merged, delete branch
git checkout development
git pull origin development
git branch -d feature/user-profile
```

**Commit Message Convention** (Conventional Commits):

- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code style (formatting, no logic change)
- `refactor:` Code refactoring
- `test:` Add/update tests
- `chore:` Maintenance tasks

**Example**:

```bash
feat: add course enrollment feature
fix: resolve image upload bug in course form
docs: update README with setup instructions
refactor: extract duplicate API call logic to hook
test: add unit tests for Button component
```

### Code Quality Tools

**Tools untuk maintain code quality**:

1. **ESLint** - Linting (find code issues)

```bash
# Run linter
bun lint

# Fix auto-fixable issues
bun lint --fix
```

2. **TypeScript** - Type checking

```bash
# Check types without build
bun run type-check
```

3. **Prettier** - Code formatting (jika installed)

```bash
# Format all files
bun run format

# Check if files formatted
bun run format:check
```

4. **Husky** - Git hooks (run checks sebelum commit)

```bash
# Pre-commit hook runs automatically:
# - Lint staged files
# - Type check
# - Run tests
```

**IDE Setup** (VS Code recommended):

Install extensions:

- ESLint
- Prettier
- TypeScript and JavaScript Language Features
- Tailwind CSS IntelliSense
- Path Intellisense

Settings (`.vscode/settings.json`):

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "tailwindCSS.experimental.classRegex": [
    ["className\\s*=\\s*['\"`]([^'\"`]*)['\"`]"]
  ]
}
```

---

## 📚 Best Practices Summary

### Do's ✅

1. **Type Everything**: Use TypeScript types untuk all functions, components, dan data
2. **Component Structure**: Follow consistent order (imports, types, component, hooks, handlers, JSX)
3. **Server First**: Prefer Server Components, use Client Components only when needed
4. **Error Handling**: Always handle errors dengan try-catch dan user-friendly messages
5. **Organized Styles**: Group Tailwind classes logically untuk readability
6. **Document Code**: Add JSDoc comments untuk complex functions
7. **Test Critical Paths**: Write tests untuk important features
8. **Small Commits**: Commit often dengan clear messages
9. **Code Review**: Request review untuk all changes
10. **Keep DRY**: Extract repeated logic ke hooks atau utilities

### Don'ts ❌

1. **Don't Use `any`**: Defeats TypeScript purpose, use proper types
2. **Don't Mutate State**: Always use setter functions (`setState`)
3. **Don't Ignore Errors**: Always handle dan log errors
4. **Don't Commit Secrets**: Never commit `.env.local` atau API keys
5. **Don't Inline Styles**: Use Tailwind classes, avoid `style` prop
6. **Don't Make Huge Components**: Break down ke smaller components
7. **Don't Skip Testing**: Test critical functionality
8. **Don't Use `var`**: Use `const` atau `let`
9. **Don't Prop Drill**: Use Context atau composition untuk deep nesting
10. **Don't Forget Accessibility**: Add ARIA labels, keyboard navigation

### Performance Tips ⚡

1. **Use Server Components** untuk data fetching
2. **Lazy Load** heavy components dengan `dynamic()`
3. **Optimize Images** dengan Next.js Image component
4. **Debounce** search inputs dan frequent updates
5. **Memoize** expensive computations dengan `useMemo`
6. **Virtualize** long lists dengan react-window
7. **Code Split** large bundles
8. **Avoid Unnecessary Re-renders** dengan `React.memo` dan `useCallback`

---

[← Kembali: Database Schema](development-guide-Database-Schema.md) | [Selanjutnya: UI/UX Standards →](development-guide-UI-UX-Standards.md)
