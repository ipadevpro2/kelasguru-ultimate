# Implementing React with Shadcn UI while Maintaining CORS Compliance

This guide explains how to convert the current Teacher Administration Dashboard to React using Shadcn UI components while preserving the CORS bypass approach for the Google Apps Script backend.

## Step 1: Create a React Project

```bash
npx create-react-app . --template typescript
```

## Step 2: Install Tailwind CSS (required for Shadcn UI)

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Configure your `tailwind.config.js`:

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: 0 },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: 0 },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}
```

## Step 3: Setup Shadcn UI

```bash
# Install the Shadcn CLI
npm install -D @shadcn/ui

# Initialize Shadcn UI
npx shadcn-ui init
```

When prompted, choose these settings:
- Style: Default
- Base color: Slate
- CSS variables: Yes
- React Server Components: No
- Path aliases: Yes, use @/* for src directory

## Step 4: Install Required Components

```bash
# Install the components you need
npx shadcn-ui add button
npx shadcn-ui add card
npx shadcn-ui add avatar
npx shadcn-ui add dropdown-menu
npx shadcn-ui add input
npx shadcn-ui add table
npx shadcn-ui add tabs
npx shadcn-ui add form
```

## Step 5: Create API Service

Create a file at `src/services/api.ts` for API communication that maintains CORS compliance:

```typescript
// Replace with your deployed Google Apps Script web app URL
const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID_HERE/exec';

// Generic API function for making requests to the GAS backend
export async function callApi(action: string, params = {}) {
  try {
    // Combine action with other parameters
    const data = {
      action,
      ...params
    };
    
    // Create URLSearchParams object (produces application/x-www-form-urlencoded format)
    const formData = new URLSearchParams(data);
    
    // Make fetch request following the CORS rules
    const response = await fetch(API_URL, {
      method: 'POST',
      body: formData
      // Note: No custom headers, no Content-Type set explicitly
    });
    
    // Parse the JSON response
    const result = await response.json();
    
    return result;
  } catch (error) {
    console.error('API Error:', error);
    throw error;
  }
}

// === Authentication API ===
export async function login(username: string, password: string) {
  return callApi('login', { username, password });
}

// === Kelas (Class) API ===
export async function getKelas(id?: string | number) {
  return callApi('getKelas', id ? { id } : {});
}

// ... Include the other API functions following the same pattern
```

## Step 6: Create Layout Components

Create a layout structure that resembles your current dashboard:

```typescript
// src/components/layout/MainLayout.tsx
import { ReactNode, useState } from "react";
import Sidebar from "./Sidebar";
import TopNavigation from "./TopNavigation";

interface MainLayoutProps {
  children: ReactNode;
}

export default function MainLayout({ children }: MainLayoutProps) {
  const [sidebarOpen, setSidebarOpen] = useState(false);

  return (
    <div className="flex h-screen overflow-hidden">
      {/* Mobile sidebar backdrop */}
      {sidebarOpen && (
        <div 
          className="fixed inset-0 z-40 bg-black bg-opacity-50 lg:hidden"
          onClick={() => setSidebarOpen(false)}
        />
      )}
      
      {/* Sidebar */}
      <Sidebar isOpen={sidebarOpen} setIsOpen={setSidebarOpen} />
      
      {/* Main content */}
      <div className="flex-1 overflow-auto">
        <TopNavigation onMenuClick={() => setSidebarOpen(true)} />
        <main className="p-4 sm:p-6 lg:p-8">
          {children}
        </main>
      </div>
    </div>
  );
}
```

## Step 7: Convert Existing Pages to React Components

Convert your dashboard page to React components using Shadcn UI:

```typescript
// src/pages/Dashboard.tsx
import { Card, CardContent } from "@/components/ui/card";
import { getKelas, getSiswa } from "@/services/api";
import { useEffect, useState } from "react";

export default function Dashboard() {
  const [stats, setStats] = useState({
    totalSiswa: 0,
    totalKelas: 0,
    jadwalHariIni: 0,
    eventMendatang: 0,
  });

  useEffect(() => {
    async function loadData() {
      try {
        // Uncomment when backend is ready
        // const kelasData = await getKelas();
        // const siswaData = await getSiswa();
        // setStats({
        //   totalSiswa: siswaData.data.length,
        //   totalKelas: kelasData.data.length,
        //   jadwalHariIni: 3,
        //   eventMendatang: 2,
        // });
        
        // For development, use mock data
        setStats({
          totalSiswa: 128,
          totalKelas: 6,
          jadwalHariIni: 3,
          eventMendatang: 2,
        });
      } catch (error) {
        console.error("Error loading dashboard data:", error);
      }
    }
    
    loadData();
  }, []);

  return (
    <div>
      <h1 className="text-2xl font-bold mb-6">Dashboard</h1>
      
      {/* Stats cards */}
      <div className="grid grid-cols-1 gap-5 sm:grid-cols-2 lg:grid-cols-4">
        <StatCard 
          title="Total Siswa" 
          value={stats.totalSiswa} 
          icon="students" 
          color="blue" 
        />
        <StatCard 
          title="Total Kelas" 
          value={stats.totalKelas} 
          icon="classes" 
          color="green" 
        />
        <StatCard 
          title="Jadwal Hari Ini" 
          value={stats.jadwalHariIni} 
          icon="schedule" 
          color="yellow" 
        />
        <StatCard 
          title="Event Mendatang" 
          value={stats.eventMendatang} 
          icon="event" 
          color="red" 
        />
      </div>
      
      {/* Rest of the dashboard components */}
    </div>
  );
}

// Stat card component
function StatCard({ title, value, icon, color }) {
  return (
    <Card className="transition-all duration-200 hover:shadow-lg hover:-translate-y-1">
      <CardContent className="p-4 flex items-center">
        <div className={`flex-shrink-0 bg-${color}-500 rounded-md p-3`}>
          {/* Icon based on type */}
          {/* Using a simplified version here */}
          <span className="text-white">{icon}</span>
        </div>
        <div className="ml-5 flex-1">
          <h3 className="text-sm font-medium text-gray-500 truncate">{title}</h3>
          <p className="text-2xl font-semibold text-gray-900">{value}</p>
        </div>
      </CardContent>
    </Card>
  );
}
```

## Important Notes for CORS Compliance

1. When making API calls, always use the `api.ts` service which follows the CORS bypass rules:
   - Uses URLSearchParams for form-encoded data
   - Does not set custom headers
   - Does not explicitly set Content-Type

2. Don't introduce any libraries or methods that might modify headers or change how requests are sent.

3. Keep the backend (code.gs) unchanged - it's already designed to work with the CORS bypass technique.

## Testing CORS Compliance

After implementation, verify that:

1. API calls work correctly without CORS errors
2. The network tab shows requests are being sent as `application/x-www-form-urlencoded`
3. No custom headers are being added to the requests

This approach allows you to use modern React with Shadcn UI components while maintaining the CORS bypass technique required for communicating with your Google Apps Script backend. 