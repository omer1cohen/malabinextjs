# 🎨 Design System & Theme

## Overview
This document outlines the design system, styling guidelines, and visual identity for the Malabi E-commerce website. The design focuses on modern, attractive aesthetics with smooth animations and Hebrew/RTL support.

## 🎨 Color Palette & Gradients

### Custom CSS Variables
```css
/* app/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    /* Primary Brand Colors */
    --gradient-pink: linear-gradient(135deg, #ec4899 0%, #a855f7 100%);
    --gradient-purple: linear-gradient(135deg, #8b5cf6 0%, #6366f1 100%);
    --gradient-warm: linear-gradient(135deg, #f97316 0%, #ec4899 100%);

    /* Shadows & Effects */
    --shadow-glow: 0 0 30px rgba(236, 72, 153, 0.3);
    --shadow-card: 0 10px 40px rgba(0, 0, 0, 0.1);
    --shadow-soft: 0 4px 20px rgba(0, 0, 0, 0.05);

    /* Glass Effects */
    --glass-bg: rgba(255, 255, 255, 0.7);
    --glass-border: rgba(255, 255, 255, 0.2);
  }

  /* Dark mode support */
  .dark {
    --glass-bg: rgba(0, 0, 0, 0.3);
    --glass-border: rgba(255, 255, 255, 0.1);
  }
}
```

### Brand Colors
- **Primary Pink**: `#ec4899` - Used for CTAs and highlights
- **Primary Purple**: `#a855f7` - Secondary actions and accents
- **Orange Accent**: `#f97316` - Warm highlights and emphasis
- **Neutral Gray**: `#6b7280` - Text and subtle elements

## ✨ Animations & Effects

### Custom Keyframes
```css
@layer utilities {
  /* Floating Animation */
  @keyframes float {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-20px); }
  }

  /* Pulsing Glow Effect */
  @keyframes pulse-glow {
    0%, 100% { box-shadow: 0 0 20px rgba(236, 72, 153, 0.5); }
    50% { box-shadow: 0 0 40px rgba(236, 72, 153, 0.8); }
  }

  /* Gradient Shift */
  @keyframes gradient-shift {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }

  /* Bounce In */
  @keyframes bounce-in {
    0% {
      transform: scale(0);
      opacity: 0;
    }
    50% {
      transform: scale(1.1);
    }
    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

  /* Custom Animation Classes */
  .animate-float {
    animation: float 6s ease-in-out infinite;
  }

  .animate-pulse-glow {
    animation: pulse-glow 2s ease-in-out infinite;
  }

  .animate-gradient {
    background-size: 200% 200%;
    animation: gradient-shift 4s ease infinite;
  }

  .animate-bounce-in {
    animation: bounce-in 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  }
}
```

## 🌟 Design Components

### Glassmorphism Effects
```css
@layer utilities {
  /* Glass Card */
  .glass {
    @apply bg-white/70 backdrop-blur-xl border border-white/20;
  }

  .glass-dark {
    @apply bg-black/30 backdrop-blur-xl border border-white/10;
  }

  /* Glass Button */
  .glass-button {
    @apply glass hover:bg-white/80 transition-all duration-300;
  }

  /* Glass Navigation */
  .glass-nav {
    @apply glass sticky top-0 z-50;
  }
}
```

### Gradient Text Effects
```css
@layer utilities {
  /* Primary Gradient Text */
  .gradient-text {
    @apply bg-gradient-to-r from-pink-500 to-purple-600 bg-clip-text text-transparent;
  }

  .gradient-text-warm {
    @apply bg-gradient-to-r from-orange-500 to-pink-500 bg-clip-text text-transparent;
  }

  .gradient-text-purple {
    @apply bg-gradient-to-r from-purple-600 to-indigo-600 bg-clip-text text-transparent;
  }
}
```

### Button Variants
```css
@layer components {
  /* Primary Button */
  .btn-primary {
    @apply bg-gradient-to-r from-pink-500 to-purple-600 text-white font-semibold px-6 py-3 rounded-full;
    @apply hover:from-pink-600 hover:to-purple-700 transition-all duration-300;
    @apply shadow-lg hover:shadow-xl transform hover:-translate-y-1;
  }

  /* Secondary Button */
  .btn-secondary {
    @apply glass text-purple-600 font-semibold px-6 py-3 rounded-full;
    @apply hover:bg-white/80 transition-all duration-300;
    @apply border-2 border-purple-200 hover:border-purple-300;
  }

  /* Ghost Button */
  .btn-ghost {
    @apply text-purple-600 font-semibold px-6 py-3 rounded-full;
    @apply hover:bg-purple-50 transition-all duration-300;
  }
}
```

## 📱 Responsive Design Breakpoints

```css
/* Tailwind CSS Breakpoints */
/* sm: 640px */
/* md: 768px */
/* lg: 1024px */
/* xl: 1280px */
/* 2xl: 1536px */

/* Custom Breakpoints for Mobile-First Design */
@media (max-width: 480px) {
  /* Extra small mobile devices */
}

@media (min-width: 481px) and (max-width: 768px) {
  /* Tablets and large mobile */
}
```

## 🔤 Typography Scale

### Hebrew Font Stack
```css
@layer base {
  body {
    font-family: 'Inter', 'Heebo', 'Noto Sans Hebrew', system-ui, sans-serif;
  }

  .font-hebrew {
    font-family: 'Heebo', 'Noto Sans Hebrew', system-ui, sans-serif;
  }

  .font-english {
    font-family: 'Inter', system-ui, sans-serif;
  }
}
```

### Text Sizes
- **Headline**: `text-5xl md:text-6xl` (48px-60px)
- **Subheading**: `text-3xl md:text-4xl` (30px-36px)
- **Body Large**: `text-lg` (18px)
- **Body**: `text-base` (16px)
- **Small**: `text-sm` (14px)
- **Caption**: `text-xs` (12px)

## 🌈 Component Design Patterns

### Card Hover Effects
```css
@layer components {
  .card-hover {
    @apply transition-all duration-300 ease-in-out;
    @apply hover:scale-105 hover:shadow-2xl hover:-translate-y-2;
  }

  .card-product {
    @apply card-hover bg-white rounded-2xl p-6 shadow-lg;
    @apply border border-gray-100 hover:border-pink-200;
  }
}
```

### Loading States
```css
@layer components {
  .skeleton {
    @apply animate-pulse bg-gray-200 rounded;
  }

  .skeleton-text {
    @apply skeleton h-4 w-full mb-2;
  }

  .skeleton-avatar {
    @apply skeleton h-12 w-12 rounded-full;
  }

  .shimmer {
    background: linear-gradient(90deg, #f0f0f0 25%, #e0e0e0 50%, #f0f0f0 75%);
    background-size: 200% 100%;
    animation: shimmer 1.5s infinite;
  }

  @keyframes shimmer {
    0% { background-position: -200% 0; }
    100% { background-position: 200% 0; }
  }
}
```

## 🎯 RTL (Right-to-Left) Support

### Hebrew Text Direction
```css
@layer base {
  [dir="rtl"] {
    text-align: right;
  }

  /* RTL-specific spacing */
  [dir="rtl"] .space-x-4 > * + * {
    margin-left: 0;
    margin-right: 1rem;
  }

  /* RTL-specific positioning */
  [dir="rtl"] .left-4 {
    left: auto;
    right: 1rem;
  }
}
```

### Component RTL Adjustments
```tsx
// Usage in components
<div className="flex items-center space-x-4 rtl:space-x-reverse">
  <span>טקסט בעברית</span>
</div>
```

## 🖼️ Image & Media Guidelines

### Image Aspect Ratios
- **Product Images**: `aspect-square` (1:1)
- **Hero Images**: `aspect-video` (16:9)
- **Story Images**: `aspect-[4/3]` (4:3)
- **Avatar Images**: `aspect-square` (1:1)

### Image Optimization Classes
```css
@layer components {
  .img-optimized {
    @apply object-cover transition-transform duration-300;
  }

  .img-hover-zoom {
    @apply img-optimized hover:scale-110;
  }

  .img-rounded {
    @apply img-optimized rounded-2xl;
  }
}
```

## 📐 Spacing & Layout

### Container Sizes
```css
@layer components {
  .container-custom {
    @apply mx-auto px-4 sm:px-6 lg:px-8;
    max-width: 1200px;
  }

  .container-narrow {
    @apply mx-auto px-4 sm:px-6 lg:px-8;
    max-width: 800px;
  }

  .container-wide {
    @apply mx-auto px-4 sm:px-6 lg:px-8;
    max-width: 1400px;
  }
}
```

### Grid Patterns
```css
@layer utilities {
  .grid-auto-fit {
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  }

  .grid-auto-fill {
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  }
}
```

## 🎨 Theme Customization

### Dark Mode Variables
```css
@layer base {
  .dark {
    --gradient-pink: linear-gradient(135deg, #be185d 0%, #7c3aed 100%);
    --gradient-purple: linear-gradient(135deg, #6d28d9 0%, #4338ca 100%);
    --shadow-glow: 0 0 30px rgba(190, 24, 93, 0.4);
  }
}
```

### Color Scheme Toggle
```tsx
// components/ThemeToggle.tsx
import { useTheme } from 'next-themes'

export default function ThemeToggle() {
  const { theme, setTheme } = useTheme()

  return (
    <button
      onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')}
      className="btn-ghost"
    >
      {theme === 'dark' ? '🌞' : '🌙'}
    </button>
  )
}
```

## 🔧 Design Tokens

### Spacing Scale
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
        '100': '25rem',
      }
    }
  }
}
```

### Custom Colors
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        malabi: {
          50: '#fdf2f8',
          100: '#fce7f3',
          500: '#ec4899',
          600: '#db2777',
          700: '#be185d',
        }
      }
    }
  }
}
```

## 🎬 Animation Guidelines

### Performance Considerations
- Use `transform` and `opacity` for animations
- Prefer CSS animations over JavaScript for simple effects
- Use `will-change` property sparingly
- Implement `prefers-reduced-motion` support

### Motion Accessibility
```css
@media (prefers-reduced-motion: reduce) {
  .animate-float,
  .animate-pulse-glow,
  .animate-gradient {
    animation: none;
  }

  .transition-all {
    transition: none;
  }
}
```

This design system ensures consistency across the malabi e-commerce website while maintaining excellent performance and accessibility standards.