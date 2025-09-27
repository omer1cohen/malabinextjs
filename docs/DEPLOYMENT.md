# 🚀 Production Deployment Guide

## Overview
This document provides a comprehensive checklist and guide for deploying the Malabi E-commerce website to production, ensuring optimal performance, security, and user experience.

## 🔧 Environment Setup

### Environment Variables
```env
# .env.production
NODE_ENV=production

# Site Configuration
NEXT_PUBLIC_SITE_URL=https://malabi.co.il
NEXT_PUBLIC_SITE_NAME=מלבי הטעים

# Business Information
NEXT_PUBLIC_BUSINESS_NAME=מלבי הטעים
NEXT_PUBLIC_BUSINESS_EMAIL=info@malabi.co.il
NEXT_PUBLIC_BUSINESS_PHONE=050-123-4567
NEXT_PUBLIC_BUSINESS_ADDRESS=רחוב הדוגמה 123, תל אביב

# WhatsApp Integration
NEXT_PUBLIC_WHATSAPP_NUMBER=972501234567
WHATSAPP_ACCESS_TOKEN=your_production_token
WHATSAPP_PHONE_NUMBER_ID=your_production_phone_id

# Analytics & Monitoring
NEXT_PUBLIC_GA_ID=G-XXXXXXXXX
NEXT_PUBLIC_HOTJAR_ID=your_hotjar_id

# Email Service
RESEND_API_KEY=re_production_key

# Payment Processing (Future)
STRIPE_SECRET_KEY=sk_live_...
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_...

# Social Media
INSTAGRAM_ACCESS_TOKEN=production_token
FACEBOOK_APP_ID=production_app_id

# Security
NEXTAUTH_SECRET=your_super_secure_secret
NEXTAUTH_URL=https://malabi.co.il
```

### Build Configuration
```javascript
// next.config.js (Production)
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  poweredByHeader: false,
  compress: true,

  // Image optimization
  images: {
    formats: ['image/webp', 'image/avif'],
    domains: ['images.unsplash.com', 'via.placeholder.com'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },

  // Security headers
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'Referrer-Policy',
            value: 'strict-origin-when-cross-origin',
          },
          {
            key: 'Content-Security-Policy',
            value: "default-src 'self'; script-src 'self' 'unsafe-eval' 'unsafe-inline' *.googletagmanager.com *.google-analytics.com; style-src 'self' 'unsafe-inline'; img-src 'self' data: blob: *.googletagmanager.com *.google-analytics.com; font-src 'self'; connect-src 'self' *.google-analytics.com *.analytics.google.com *.googletagmanager.com; frame-src 'self';"
          }
        ],
      },
    ]
  },

  // Redirects
  async redirects() {
    return [
      {
        source: '/home',
        destination: '/',
        permanent: true,
      },
    ]
  },

  // Performance optimizations
  experimental: {
    optimizeCss: true,
    optimizeServerReact: true,
  },

  compiler: {
    removeConsole: process.env.NODE_ENV === 'production',
  },
}

module.exports = nextConfig
```

## 📋 Pre-Launch Checklist

### ✅ Performance Optimization
- [ ] **Images Optimized**
  - [ ] All images converted to WebP/AVIF format
  - [ ] Proper image sizing and responsive images
  - [ ] Lazy loading implemented for below-fold images
  - [ ] Image compression applied (< 100KB per image)

- [ ] **Bundle Optimization**
  - [ ] Code splitting implemented
  - [ ] Unused code removed (tree shaking)
  - [ ] Dynamic imports for heavy components
  - [ ] Bundle analyzer run and optimized

- [ ] **Core Web Vitals**
  - [ ] LCP < 2.5s ✓
  - [ ] FID < 100ms ✓
  - [ ] CLS < 0.1 ✓
  - [ ] Lighthouse score > 90 ✓

```bash
# Performance testing commands
npm run build
npm run analyze
npx lighthouse https://staging.malabi.co.il --output=html --output-path=./lighthouse-report.html
```

### ✅ SEO & Meta Tags
- [ ] **Meta Tags Configuration**
```tsx
// app/layout.tsx - Production Meta Tags
export const metadata: Metadata = {
  title: {
    template: '%s | מלבי הטעים - הטעם הכי טוב בעיר',
    default: 'מלבי הטעים - קינוחים טעימים עם משלוח חינם',
  },
  description: 'מלבי טעים ואיכותי עם מגוון טעמים מיוחדים. הזמינו עכשיו ותיהנו ממלבי הטוב ביותר בעיר! משלוח חינם מעל ₪50',
  keywords: ['מלבי', 'קינוח', 'טעים', 'משלוחים', 'הזמנות', 'תל אביב', 'קינוחים כשרים'],
  authors: [{ name: 'מלבי הטעים' }],
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
    },
  },
  openGraph: {
    type: 'website',
    locale: 'he_IL',
    url: 'https://malabi.co.il',
    siteName: 'מלבי הטעים',
    title: 'מלבי הטעים - הטעם הכי טוב בעיר',
    description: 'מלבי טעים ואיכותי עם מגוון טעמים מיוחדים',
    images: [
      {
        url: 'https://malabi.co.il/og-image.jpg',
        width: 1200,
        height: 630,
        alt: 'מלבי הטעים - קינוחים טעימים',
      },
    ],
  },
}
```

- [ ] **Sitemap Generation**
```javascript
// app/sitemap.ts
import { MetadataRoute } from 'next'

export default function sitemap(): MetadataRoute.Sitemap {
  const baseUrl = 'https://malabi.co.il'

  return [
    {
      url: baseUrl,
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 1,
    },
    {
      url: `${baseUrl}/products`,
      lastModified: new Date(),
      changeFrequency: 'weekly',
      priority: 0.8,
    },
    {
      url: `${baseUrl}/about`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.6,
    },
    {
      url: `${baseUrl}/contact`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.5,
    },
  ]
}
```

- [ ] **Robots.txt**
```javascript
// app/robots.ts
import { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
      disallow: ['/admin/', '/api/'],
    },
    sitemap: 'https://malabi.co.il/sitemap.xml',
  }
}
```

### ✅ Functionality Testing
- [ ] **Core Features**
  - [ ] Product catalog displays correctly
  - [ ] Add to cart functionality works
  - [ ] Cart persistence (localStorage)
  - [ ] Quantity updates in cart
  - [ ] Cart total calculation accurate

- [ ] **Checkout Process**
  - [ ] Customer form validation
  - [ ] Hebrew text input handling
  - [ ] WhatsApp message formatting
  - [ ] Order confirmation flow
  - [ ] Error handling for failed submissions

- [ ] **Mobile Experience**
  - [ ] Touch targets ≥ 44px
  - [ ] Swipe gestures work
  - [ ] Responsive design on all devices
  - [ ] Mobile cart drawer functions
  - [ ] WhatsApp integration on mobile

### ✅ Security & Privacy
- [ ] **Security Headers**
  - [ ] CSP (Content Security Policy) configured
  - [ ] X-Frame-Options set to DENY
  - [ ] X-Content-Type-Options set to nosniff
  - [ ] HTTPS enforced (HSTS)

- [ ] **Data Protection**
  - [ ] Customer data not logged
  - [ ] No sensitive info in client bundle
  - [ ] Environment variables secured
  - [ ] GDPR compliance for EU visitors

- [ ] **Privacy Policy & Terms**
```tsx
// app/privacy/page.tsx
export default function PrivacyPage() {
  return (
    <div className="container mx-auto px-4 py-8" dir="rtl">
      <h1 className="text-3xl font-bold mb-6">מדיניות פרטיות</h1>

      <section className="space-y-6">
        <div>
          <h2 className="text-2xl font-semibold mb-3">איסוף מידע</h2>
          <p>אנו אוספים מידע שאתם מספקים לנו בעת ביצוע הזמנה:</p>
          <ul className="list-disc pr-6 mt-2">
            <li>שם מלא</li>
            <li>מספר טלפון</li>
            <li>כתובת למשלוח</li>
            <li>כתובת אימייל (אופציונלי)</li>
          </ul>
        </div>

        <div>
          <h2 className="text-2xl font-semibold mb-3">שימוש במידע</h2>
          <p>המידע שלכם משמש אותנו עבור:</p>
          <ul className="list-disc pr-6 mt-2">
            <li>עיבוד וביצוע הזמנות</li>
            <li>יצירת קשר לתיאום משלוח</li>
            <li>שיפור השירות שלנו</li>
          </ul>
        </div>

        <div>
          <h2 className="text-2xl font-semibold mb-3">אבטחת מידע</h2>
          <p>אנו מתחייבים לשמור על המידע שלכם בצורה מאובטחת ולא להעביר אותו לצדדים שלישיים.</p>
        </div>

        <div>
          <h2 className="text-2xl font-semibold mb-3">יצירת קשר</h2>
          <p>לשאלות בנושא פרטיות, ניתן ליצור קשר:</p>
          <p className="mt-2">
            📧 info@malabi.co.il<br/>
            📞 050-123-4567
          </p>
        </div>
      </section>
    </div>
  )
}
```

### ✅ Analytics & Monitoring
- [ ] **Google Analytics 4**
  - [ ] Tracking ID configured
  - [ ] E-commerce events tracked
  - [ ] Conversion goals set up
  - [ ] Privacy compliance implemented

- [ ] **Error Tracking**
```tsx
// lib/error-tracking.ts
export function trackError(error: Error, context?: Record<string, any>) {
  // Log to console in development
  if (process.env.NODE_ENV === 'development') {
    console.error('Error tracked:', error, context)
  }

  // Send to monitoring service in production
  if (process.env.NODE_ENV === 'production' && window.gtag) {
    window.gtag('event', 'exception', {
      description: error.message,
      fatal: false,
      custom_parameters: context
    })
  }
}
```

- [ ] **Performance Monitoring**
```tsx
// components/PerformanceMonitor.tsx
'use client'

import { useEffect } from 'react'

export default function PerformanceMonitor() {
  useEffect(() => {
    // Track Core Web Vitals
    import('web-vitals').then(({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
      getCLS(console.log)
      getFID(console.log)
      getFCP(console.log)
      getLCP(console.log)
      getTTFB(console.log)
    })
  }, [])

  return null
}
```

## 🌐 Deployment Platforms

### Vercel Deployment (Recommended)
```bash
# Install Vercel CLI
npm i -g vercel

# Deploy to staging
vercel --prod=false

# Deploy to production
vercel --prod

# Custom domain setup
vercel domains add malabi.co.il
```

### Vercel Configuration
```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": ".next",
  "installCommand": "npm install",
  "framework": "nextjs",
  "regions": ["fra1"],
  "env": {
    "NODE_ENV": "production"
  },
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    }
  ]
}
```

### Netlify Deployment Alternative
```toml
# netlify.toml
[build]
  command = "npm run build"
  publish = ".next"

[build.environment]
  NODE_ENV = "production"
  NEXT_TELEMETRY_DISABLED = "1"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
```

## 🔍 Domain & DNS Setup

### Domain Configuration
```bash
# DNS Records for malabi.co.il
Type    Name    Value                   TTL
A       @       76.76.19.61            300
CNAME   www     malabi.co.il           300
TXT     @       v=spf1 include:_spf.google.com ~all   300
```

### SSL Certificate
- [ ] SSL certificate installed and configured
- [ ] HTTPS redirect enabled
- [ ] HSTS header configured
- [ ] Certificate auto-renewal set up

## 📧 Email Setup

### Business Email Configuration
```bash
# Google Workspace or similar
MX Records:
Priority    Hostname        Points to
1          malabi.co.il    ASPMX.L.GOOGLE.COM
5          malabi.co.il    ALT1.ASPMX.L.GOOGLE.COM
5          malabi.co.il    ALT2.ASPMX.L.GOOGLE.COM
10         malabi.co.il    ALT3.ASPMX.L.GOOGLE.COM
10         malabi.co.il    ALT4.ASPMX.L.GOOGLE.COM
```

## 🎯 Post-Launch Checklist

### ✅ Immediate Post-Launch (Day 1)
- [ ] **Monitoring Active**
  - [ ] Google Analytics tracking working
  - [ ] Error tracking functional
  - [ ] Performance monitoring active
  - [ ] WhatsApp integration tested

- [ ] **Functionality Tests**
  - [ ] Place test order end-to-end
  - [ ] Verify WhatsApp message received
  - [ ] Test on multiple devices
  - [ ] Check loading speeds

### ✅ First Week
- [ ] **Performance Review**
  - [ ] Check Lighthouse scores daily
  - [ ] Monitor Core Web Vitals
  - [ ] Review error logs
  - [ ] Analyze user behavior

- [ ] **SEO Setup**
  - [ ] Submit sitemap to Google Search Console
  - [ ] Set up Google My Business listing
  - [ ] Add structured data for rich snippets
  - [ ] Monitor search console for issues

### ✅ First Month
- [ ] **Analytics Review**
  - [ ] Analyze conversion rates
  - [ ] Review most popular products
  - [ ] Identify user journey bottlenecks
  - [ ] Optimize based on data

- [ ] **Content Updates**
  - [ ] Add customer testimonials
  - [ ] Update product descriptions
  - [ ] Add seasonal products
  - [ ] Create blog content for SEO

## 🔄 Maintenance Schedule

### Daily
- [ ] Monitor error logs
- [ ] Check site availability
- [ ] Review order submissions

### Weekly
- [ ] Performance audit
- [ ] Security scan
- [ ] Backup verification
- [ ] Analytics review

### Monthly
- [ ] Dependency updates
- [ ] Security patch review
- [ ] Performance optimization
- [ ] Content updates

## 🚨 Emergency Procedures

### Site Down Protocol
1. **Check Status**
   ```bash
   curl -I https://malabi.co.il
   ping malabi.co.il
   ```

2. **Rollback Procedure**
   ```bash
   vercel rollback
   # or
   git revert HEAD
   vercel --prod
   ```

3. **Emergency Contacts**
   - Domain registrar support
   - Hosting provider support
   - CDN support (if applicable)

### Data Recovery
- [ ] Database backups automated
- [ ] Asset backups configured
- [ ] Configuration backups stored
- [ ] Recovery procedures documented

This deployment guide ensures your malabi e-commerce website launches successfully and maintains high performance in production.