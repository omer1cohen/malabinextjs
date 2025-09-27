# 🚀 Performance Optimization Guide

## Overview
This document outlines performance optimization strategies for the Malabi E-commerce website, focusing on Core Web Vitals, loading speed, and user experience improvements.

## 📊 Performance Metrics Goals

### Core Web Vitals Targets
- **LCP (Largest Contentful Paint)**: < 2.5s
- **FID (First Input Delay)**: < 100ms
- **CLS (Cumulative Layout Shift)**: < 0.1
- **FCP (First Contentful Paint)**: < 1.8s
- **TTI (Time to Interactive)**: < 3.5s

## 🖼️ Image Optimization

### 1. Optimized Image Component
```tsx
// components/ui/custom/OptimizedImage.tsx
import Image from 'next/image'
import { useState } from 'react'

interface OptimizedImageProps {
  src: string
  alt: string
  className?: string
  priority?: boolean
  sizes?: string
}

export default function OptimizedImage({
  src,
  alt,
  className,
  priority = false,
  sizes = "(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
}: OptimizedImageProps) {
  const [isLoading, setIsLoading] = useState(true)

  // Generate shimmer placeholder
  const shimmer = (w: number, h: number) => `
    <svg width="${w}" height="${h}" version="1.1" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <linearGradient id="g">
          <stop stop-color="#f6f7f8" offset="20%" />
          <stop stop-color="#edeef1" offset="50%" />
          <stop stop-color="#f6f7f8" offset="70%" />
        </linearGradient>
      </defs>
      <rect width="${w}" height="${h}" fill="#f6f7f8" />
      <rect id="r" width="${w}" height="${h}" fill="url(#g)" />
      <animate xlink:href="#r" attributeName="x" from="-${w}" to="${w}" dur="1s" repeatCount="indefinite" />
    </svg>`

  const toBase64 = (str: string) =>
    typeof window === 'undefined'
      ? Buffer.from(str).toString('base64')
      : window.btoa(str)

  const dataUrl = `data:image/svg+xml;base64,${toBase64(shimmer(400, 400))}`

  return (
    <div className="relative w-full h-full overflow-hidden">
      <Image
        src={src}
        alt={alt}
        fill
        className={`object-cover transition-opacity duration-500 ${
          isLoading ? 'opacity-0' : 'opacity-100'
        } ${className}`}
        onLoad={() => setIsLoading(false)}
        placeholder="blur"
        blurDataURL={dataUrl}
        priority={priority}
        sizes={sizes}
      />
      {isLoading && (
        <div className="absolute inset-0 bg-gray-200 animate-pulse" />
      )}
    </div>
  )
}
```

### 2. Image Format Optimization
```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    formats: ['image/webp', 'image/avif'],
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'images.unsplash.com',
      },
      {
        protocol: 'https',
        hostname: 'via.placeholder.com',
      },
    ],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
}

module.exports = nextConfig
```

### 3. Progressive Image Loading
```tsx
// components/ui/custom/ProgressiveImage.tsx
import { useState, useEffect } from 'react'
import Image from 'next/image'

interface ProgressiveImageProps {
  src: string
  lowResSrc: string
  alt: string
  className?: string
}

export default function ProgressiveImage({
  src,
  lowResSrc,
  alt,
  className
}: ProgressiveImageProps) {
  const [imageSrc, setImageSrc] = useState(lowResSrc)
  const [isLoading, setIsLoading] = useState(true)

  useEffect(() => {
    const img = new window.Image()
    img.src = src
    img.onload = () => {
      setImageSrc(src)
      setIsLoading(false)
    }
  }, [src])

  return (
    <div className="relative w-full h-full">
      <Image
        src={imageSrc}
        alt={alt}
        fill
        className={`object-cover transition-all duration-700 ${
          isLoading ? 'blur-sm scale-110' : 'blur-0 scale-100'
        } ${className}`}
      />
    </div>
  )
}
```

## ⚡ Component Lazy Loading

### 1. Dynamic Imports with Loading States
```tsx
// app/page.tsx
import dynamic from 'next/dynamic'

// Lazy load heavy components with custom loading
const Hero = dynamic(() => import('@/components/sections/Hero'), {
  loading: () => (
    <div className="h-screen bg-gradient-to-r from-pink-50 to-purple-50 animate-pulse" />
  )
})

const BestSellers = dynamic(() => import('@/components/sections/BestSellers'), {
  loading: () => (
    <div className="h-96 bg-gray-50 animate-pulse rounded-2xl mx-4" />
  ),
  ssr: false // Disable SSR for client-only components
})

const Testimonials = dynamic(() => import('@/components/sections/Testimonials'), {
  loading: () => (
    <div className="h-64 bg-gradient-to-r from-purple-50 to-pink-50 animate-pulse" />
  )
})

const Newsletter = dynamic(() => import('@/components/sections/Newsletter'), {
  loading: () => <div className="h-32 bg-gray-50 animate-pulse" />
})

export default function HomePage() {
  return (
    <>
      <Hero />
      <BestSellers />
      <Testimonials />
      <Newsletter />
    </>
  )
}
```

### 2. Intersection Observer Lazy Loading
```tsx
// hooks/useIntersectionObserver.ts
import { useState, useEffect, useRef } from 'react'

interface UseIntersectionObserverProps {
  threshold?: number
  rootMargin?: string
  triggerOnce?: boolean
}

export function useIntersectionObserver({
  threshold = 0.1,
  rootMargin = '50px',
  triggerOnce = true
}: UseIntersectionObserverProps = {}) {
  const [isIntersecting, setIsIntersecting] = useState(false)
  const elementRef = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const element = elementRef.current
    if (!element) return

    const observer = new IntersectionObserver(
      ([entry]) => {
        setIsIntersecting(entry.isIntersecting)
        if (entry.isIntersecting && triggerOnce) {
          observer.unobserve(element)
        }
      },
      { threshold, rootMargin }
    )

    observer.observe(element)

    return () => observer.disconnect()
  }, [threshold, rootMargin, triggerOnce])

  return { elementRef, isIntersecting }
}
```

### 3. Lazy Component Wrapper
```tsx
// components/ui/LazyWrapper.tsx
import { ReactNode } from 'react'
import { useIntersectionObserver } from '@/hooks/useIntersectionObserver'

interface LazyWrapperProps {
  children: ReactNode
  fallback?: ReactNode
  className?: string
}

export default function LazyWrapper({
  children,
  fallback,
  className
}: LazyWrapperProps) {
  const { elementRef, isIntersecting } = useIntersectionObserver({
    threshold: 0.1,
    rootMargin: '100px'
  })

  return (
    <div ref={elementRef} className={className}>
      {isIntersecting ? children : fallback}
    </div>
  )
}
```

## 🎯 Code Splitting & Bundle Optimization

### 1. Route-based Code Splitting
```tsx
// app/layout.tsx - Optimize font loading
import { Inter, Heebo } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  preload: true,
  fallback: ['system-ui', 'arial']
})

const heebo = Heebo({
  subsets: ['hebrew'],
  display: 'swap',
  preload: true,
  fallback: ['system-ui', 'arial']
})

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="he" dir="rtl">
      <body className={`${inter.className} ${heebo.className}`}>
        {children}
      </body>
    </html>
  )
}
```

### 2. Bundle Analysis Configuration
```javascript
// next.config.js
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true',
})

/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    optimizeCss: true,
    optimizeServerReact: true,
  },
  compiler: {
    removeConsole: process.env.NODE_ENV === 'production',
  },
  // Enable SWC minification
  swcMinify: true,

  // Optimize imports
  modularizeImports: {
    'lucide-react': {
      transform: 'lucide-react/dist/esm/icons/{{member}}',
    },
  },
}

module.exports = withBundleAnalyzer(nextConfig)
```

## 🔄 Caching Strategies

### 1. Service Worker for Asset Caching
```javascript
// public/sw.js
const CACHE_NAME = 'malabi-v1'
const urlsToCache = [
  '/',
  '/static/css/main.css',
  '/static/js/main.js',
  '/images/logo.png'
]

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then((cache) => cache.addAll(urlsToCache))
  )
})

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request)
      .then((response) => {
        // Return cached version or fetch from network
        return response || fetch(event.request)
      })
  )
})
```

### 2. HTTP Caching Headers
```javascript
// next.config.js
const nextConfig = {
  async headers() {
    return [
      {
        source: '/images/:path*',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
      {
        source: '/_next/static/:path*',
        headers: [
          {
            key: 'Cache-Control',
            value: 'public, max-age=31536000, immutable',
          },
        ],
      },
    ]
  },
}
```

## 📱 Mobile Performance

### 1. Responsive Images
```tsx
// components/ResponsiveImage.tsx
import Image from 'next/image'

interface ResponsiveImageProps {
  src: string
  alt: string
  className?: string
}

export default function ResponsiveImage({ src, alt, className }: ResponsiveImageProps) {
  return (
    <Image
      src={src}
      alt={alt}
      sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
      className={className}
      priority={false}
    />
  )
}
```

### 2. Touch-Optimized Interactions
```css
/* Touch target optimization */
@layer utilities {
  .touch-target {
    min-height: 44px;
    min-width: 44px;
  }

  .touch-friendly {
    @apply touch-target p-3 rounded-lg;
  }

  /* Reduce motion for mobile */
  @media (max-width: 768px) {
    .animate-float {
      animation-duration: 3s;
    }
  }
}
```

## 🎨 CSS Optimization

### 1. Critical CSS Extraction
```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <head>
        {/* Critical CSS inlined */}
        <style dangerouslySetInnerHTML={{
          __html: `
            body { margin: 0; font-family: system-ui; }
            .hero { min-height: 100vh; background: linear-gradient(135deg, #ec4899 0%, #a855f7 100%); }
          `
        }} />
      </head>
      <body>{children}</body>
    </html>
  )
}
```

### 2. Purge Unused CSS
```javascript
// tailwind.config.js
module.exports = {
  content: [
    './src/**/*.{js,ts,jsx,tsx}',
    './app/**/*.{js,ts,jsx,tsx}',
    './components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

## 🔍 Performance Monitoring

### 1. Core Web Vitals Tracking
```tsx
// app/layout.tsx
import { reportWebVitals } from '@/lib/analytics'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html>
      <body>
        {children}
        <script
          dangerouslySetInnerHTML={{
            __html: `
              new PerformanceObserver((list) => {
                for (const entry of list.getEntries()) {
                  console.log(entry.name, entry.value);
                }
              }).observe({entryTypes: ['measure']});
            `,
          }}
        />
      </body>
    </html>
  )
}
```

### 2. Performance Analytics
```typescript
// lib/performance.ts
export function trackPerformance() {
  if (typeof window !== 'undefined') {
    // Track LCP
    new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        console.log('LCP:', entry.startTime)
        // Send to analytics
      }
    }).observe({ entryTypes: ['largest-contentful-paint'] })

    // Track FID
    new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        console.log('FID:', entry.processingStart - entry.startTime)
        // Send to analytics
      }
    }).observe({ entryTypes: ['first-input'] })

    // Track CLS
    new PerformanceObserver((list) => {
      for (const entry of list.getEntries()) {
        if (!entry.hadRecentInput) {
          console.log('CLS:', entry.value)
          // Send to analytics
        }
      }
    }).observe({ entryTypes: ['layout-shift'] })
  }
}
```

## 🛠️ Development Tools

### Performance Testing Commands
```bash
# Lighthouse CI
npm install -g @lhci/cli
lhci autorun

# Bundle analysis
npm run build && npm run analyze

# Performance profiling
npm run dev -- --profile

# Core Web Vitals testing
npx unlighthouse --site https://your-site.com
```

### Performance Budget
```json
// performance-budget.json
{
  "budget": [
    {
      "path": "/*",
      "timings": [
        {
          "metric": "first-contentful-paint",
          "budget": 2000
        },
        {
          "metric": "largest-contentful-paint",
          "budget": 2500
        },
        {
          "metric": "speed-index",
          "budget": 3000
        }
      ],
      "resourceSizes": [
        {
          "resourceType": "script",
          "budget": 300
        },
        {
          "resourceType": "total",
          "budget": 500
        }
      ]
    }
  ]
}
```

## 📈 Performance Checklist

### Pre-Launch Optimization
- [ ] Images optimized (WebP/AVIF format)
- [ ] Critical CSS inlined
- [ ] Unused CSS purged
- [ ] JavaScript code-split by routes
- [ ] Fonts preloaded and optimized
- [ ] Service worker implemented
- [ ] Proper caching headers set
- [ ] Bundle size analyzed and optimized
- [ ] Core Web Vitals measured
- [ ] Mobile performance tested

### Monitoring & Maintenance
- [ ] Performance analytics implemented
- [ ] Real User Monitoring (RUM) set up
- [ ] Regular Lighthouse audits scheduled
- [ ] Performance budget defined
- [ ] Error tracking implemented
- [ ] CDN properly configured
- [ ] Database queries optimized
- [ ] API response caching implemented

This performance guide ensures your malabi e-commerce site loads quickly and provides an excellent user experience across all devices.