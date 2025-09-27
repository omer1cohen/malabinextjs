# 🛠️ Development Workflow & Guidelines

## Overview
This document outlines the development workflow, coding standards, and Claude Code CLI commands for building the Malabi E-commerce website efficiently.

## 🚀 Getting Started

### Prerequisites
- Node.js 18+
- npm or yarn
- Git
- Claude Code CLI
- VS Code (recommended)

### Initial Setup
```bash
# Clone and setup
git clone <repository-url>
cd malabinextjs
npm install

# Start development server
npm run dev

# Open in browser
open http://localhost:3000
```

### Development Tools Installation
```bash
# Essential VS Code extensions
code --install-extension bradlc.vscode-tailwindcss
code --install-extension ms-vscode.vscode-typescript-next
code --install-extension esbenp.prettier-vscode
code --install-extension ms-vscode.vscode-eslint

# Optional but recommended
code --install-extension usernamehw.errorlens
code --install-extension christian-kohler.path-intellisense
code --install-extension formulahendry.auto-rename-tag
```

## 🤖 Claude Code CLI Commands

### 1. Initial Project Setup
```bash
# Create complete project structure
claude-code "Create a Next.js 15 e-commerce project for a malabi dessert shop with the following requirements:
- TypeScript configuration
- Tailwind CSS v4 with custom design system
- shadcn/ui components
- Hebrew/RTL support
- Zustand for state management
- Framer Motion for animations
- Responsive mobile-first design"
```

### 2. Type Definitions
```bash
# Create comprehensive type system
claude-code "Create TypeScript interfaces in src/types/index.ts for:
- Product interface with id, name, description, price, image, category, isPopular
- CartItem interface extending Product with quantity
- CustomerDetails interface with name, phone, email, address, deliveryTime, notes
- OrderData interface with items array, customer, total, orderNumber, timestamp
Include proper JSDoc comments for all interfaces"
```

### 3. State Management
```bash
# Setup Zustand store
claude-code "Create a Zustand store in lib/store/cart-store.ts with:
- persist middleware for localStorage
- addItem, removeItem, updateQuantity, clearCart functions
- getItemCount, getTotalPrice computed values
- TypeScript interfaces for proper typing
- RTK Query-style optimistic updates"
```

### 4. Core Components
```bash
# Create Hero section
claude-code "Create a Hero component in components/sections/Hero.tsx with:
- Animated gradient background using CSS variables
- Floating malabi shapes with Framer Motion
- Hebrew text with proper RTL support
- Call-to-action button with hover animations
- Mobile-responsive design with proper breakpoints"

# Create Product Card
claude-code "Create a ProductCard component in components/ui/ProductCard.tsx with:
- Product image with Next.js Image optimization
- Add to cart button with loading state
- Like/favorite button with heart animation
- Price display in Hebrew formatting
- Hover effects and smooth transitions
- Accessibility attributes for screen readers"

# Create Cart Drawer
claude-code "Create a CartDrawer component in components/cart/CartDrawer.tsx with:
- Slide-out panel from right side (RTL)
- Cart items list with quantity controls
- Total price calculation
- Empty cart state illustration
- Checkout button leading to order form
- Close button and overlay click handling"
```

### 5. Layout Components
```bash
# Create Header with navigation
claude-code "Create a Header component in components/layout/Header.tsx with:
- Logo with malabi icon
- Navigation menu for mobile and desktop
- Cart icon with item count badge
- Hebrew menu items with proper spacing
- Sticky positioning with glassmorphism effect
- Mobile hamburger menu with animations"

# Create Footer
claude-code "Create a Footer component in components/layout/Footer.tsx with:
- Business contact information
- Social media links (Instagram, Facebook, WhatsApp)
- Operating hours in Hebrew
- Copyright notice
- Newsletter signup form
- Background gradient matching brand colors"
```

### 6. Pages and Sections
```bash
# Create main homepage
claude-code "Create the main page in app/page.tsx with:
- Hero section with animated background
- BestSellers carousel with product cards
- Story section about the business
- Testimonials with customer reviews
- Newsletter signup with email validation
- Proper page metadata for SEO"

# Create checkout page
claude-code "Create a checkout page in app/checkout/page.tsx with:
- Order summary with cart items
- Customer details form with validation
- WhatsApp integration for order submission
- Order confirmation modal
- Loading states and error handling
- Form validation with Hebrew error messages"
```

### 7. Business Logic
```bash
# Create WhatsApp integration
claude-code "Create WhatsApp integration in lib/whatsapp.ts with:
- Order formatting function in Hebrew
- Customer details validation
- Message URL encoding for special characters
- Order number generation
- Integration with analytics tracking
- Error handling for failed submissions"

# Create product data
claude-code "Create sample product data in lib/products.ts with:
- 10 malabi products with Hebrew names and descriptions
- Variety of flavors (vanilla, chocolate, strawberry, pistachio, etc.)
- Realistic pricing in Israeli Shekels
- High-quality placeholder images
- Categories and popularity flags
- Kosher certification indicators"
```

### 8. Animations and Interactions
```bash
# Add scroll animations
claude-code "Add scroll-triggered animations using Framer Motion:
- Stagger animations for product grids
- Fade-in animations for sections on scroll
- Parallax effects for hero background
- Smooth page transitions
- Mobile-optimized reduced motion support
- Performance-optimized transform animations"

# Create loading states
claude-code "Create loading components in components/ui/loading/:
- Skeleton loaders for product cards
- Shimmer effects for images
- Loading spinners with brand colors
- Page transition loading overlay
- Cart update loading indicators
- Form submission loading states"
```

## 🏗️ Project Structure

```
malabinextjs/
├── src/
│   ├── app/                    # Next.js 15 App Router
│   │   ├── globals.css         # Global styles and Tailwind
│   │   ├── layout.tsx          # Root layout with providers
│   │   ├── page.tsx            # Homepage
│   │   ├── checkout/           # Checkout flow
│   │   └── products/           # Product pages
│   ├── components/             # React components
│   │   ├── ui/                 # Reusable UI components
│   │   ├── layout/             # Layout components
│   │   ├── sections/           # Page sections
│   │   └── cart/               # Cart-related components
│   ├── lib/                    # Utilities and configurations
│   │   ├── store/              # Zustand stores
│   │   ├── utils.ts            # Utility functions
│   │   ├── products.ts         # Product data
│   │   └── whatsapp.ts         # WhatsApp integration
│   └── types/                  # TypeScript definitions
├── public/                     # Static assets
├── docs/                       # Documentation
├── components.json             # shadcn/ui config
├── tailwind.config.js          # Tailwind configuration
├── next.config.js              # Next.js configuration
└── package.json                # Dependencies
```

## 📝 Coding Standards

### TypeScript Guidelines
```typescript
// Always use interfaces for object shapes
interface Product {
  id: string
  name: string
  price: number
  description?: string // Optional properties with ?
}

// Use enums for constants
enum OrderStatus {
  PENDING = 'pending',
  CONFIRMED = 'confirmed',
  DELIVERED = 'delivered'
}

// Prefer type inference where possible
const products = getProducts() // TypeScript infers Product[]

// Use generic types for reusable components
interface ApiResponse<T> {
  data: T
  success: boolean
  message?: string
}
```

### Component Structure
```tsx
// components/ExampleComponent.tsx
import { ReactNode } from 'react'
import { cn } from '@/lib/utils'

interface ExampleComponentProps {
  children: ReactNode
  variant?: 'primary' | 'secondary'
  className?: string
}

export default function ExampleComponent({
  children,
  variant = 'primary',
  className
}: ExampleComponentProps) {
  return (
    <div className={cn(
      'base-styles',
      variant === 'primary' && 'primary-styles',
      variant === 'secondary' && 'secondary-styles',
      className
    )}>
      {children}
    </div>
  )
}
```

### CSS/Tailwind Guidelines
```css
/* Use CSS custom properties for design tokens */
:root {
  --color-primary: #ec4899;
  --color-secondary: #a855f7;
  --spacing-unit: 0.25rem;
}

/* Prefer Tailwind utilities over custom CSS */
.card {
  @apply bg-white rounded-lg shadow-md p-6;
}

/* Use @layer for custom utilities */
@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
}
```

### RTL/Hebrew Support
```tsx
// Proper RTL component structure
export default function RTLComponent() {
  return (
    <div className="flex items-center space-x-4 rtl:space-x-reverse">
      <span className="text-right">טקסט בעברית</span>
      <button className="mr-2 rtl:mr-0 rtl:ml-2">
        כפתור
      </button>
    </div>
  )
}
```

## 🎨 Design System Implementation

### Color Palette Usage
```bash
# Create design tokens
claude-code "Create a design system in lib/design-tokens.ts with:
- Color palette with primary, secondary, accent colors
- Typography scale with Hebrew font support
- Spacing scale following 8pt grid
- Shadow definitions for elevation
- Border radius values for consistency
- Animation timing functions"
```

### Component Variants
```bash
# Create variant system
claude-code "Create a component variant system using class-variance-authority:
- Button variants (primary, secondary, ghost, outline)
- Card variants (default, elevated, outlined)
- Text variants (heading, body, caption, label)
- Size variants (xs, sm, md, lg, xl)
- Color variants matching brand palette"
```

## 🧪 Testing Strategy

### Unit Testing Setup
```bash
# Setup Jest and Testing Library
npm install -D jest @testing-library/react @testing-library/jest-dom
npm install -D jest-environment-jsdom

# Create test configuration
claude-code "Create Jest configuration in jest.config.js with:
- jsdom environment for React components
- TypeScript support with ts-jest
- Path mapping for @ imports
- Setup files for testing library
- Coverage reporting configuration"
```

### Component Testing Examples
```typescript
// __tests__/components/ProductCard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import ProductCard from '@/components/ui/ProductCard'

describe('ProductCard', () => {
  const mockProduct = {
    id: '1',
    name: 'מלבי וניל',
    price: 15,
    image: '/images/vanilla.jpg',
    description: 'מלבי וניל קלאסי'
  }

  test('renders product information correctly', () => {
    render(<ProductCard product={mockProduct} />)

    expect(screen.getByText('מלבי וניל')).toBeInTheDocument()
    expect(screen.getByText('₪15')).toBeInTheDocument()
  })

  test('adds product to cart when button clicked', () => {
    const mockAddToCart = jest.fn()
    render(<ProductCard product={mockProduct} onAddToCart={mockAddToCart} />)

    fireEvent.click(screen.getByText('הוסף לעגלה'))
    expect(mockAddToCart).toHaveBeenCalledWith(mockProduct)
  })
})
```

## 🔍 Code Quality Tools

### ESLint Configuration
```javascript
// .eslintrc.json
{
  "extends": [
    "next/core-web-vitals",
    "@typescript-eslint/recommended",
    "prettier"
  ],
  "rules": {
    "prefer-const": "error",
    "no-unused-vars": "error",
    "@typescript-eslint/no-explicit-any": "warn",
    "react-hooks/exhaustive-deps": "error"
  }
}
```

### Prettier Configuration
```javascript
// .prettierrc
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80,
  "endOfLine": "lf"
}
```

### Pre-commit Hooks
```bash
# Setup Husky for pre-commit hooks
npm install -D husky lint-staged

# Create pre-commit configuration
claude-code "Setup pre-commit hooks with husky and lint-staged:
- ESLint check on staged files
- Prettier formatting
- TypeScript type checking
- Test execution for changed files
- Commit message validation"
```

## 🚀 Development Workflow

### Git Workflow
```bash
# Feature development workflow
git checkout -b feature/product-catalog
# Make changes
git add .
git commit -m "feat: add product catalog with filtering"
git push origin feature/product-catalog
# Create pull request
```

### Branch Naming Convention
- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation updates
- `refactor/` - Code refactoring
- `test/` - Test additions/updates

### Commit Message Convention
```
type(scope): description

feat(cart): add quantity update functionality
fix(checkout): resolve WhatsApp message encoding
docs(readme): update installation instructions
style(ui): improve button hover animations
```

## 🔧 Development Scripts

### Package.json Scripts
```json
{
  "scripts": {
    "dev": "next dev --turbopack",
    "build": "next build",
    "start": "next start",
    "lint": "eslint src/ --ext .ts,.tsx",
    "lint:fix": "eslint src/ --ext .ts,.tsx --fix",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "analyze": "ANALYZE=true npm run build"
  }
}
```

### Custom Development Tools
```bash
# Create development helpers
claude-code "Create development utilities in scripts/dev-helpers.js:
- Component generator script
- Page generator with boilerplate
- Type definition generator
- Mock data generator for testing
- Build analyzer for bundle optimization"
```

## 📊 Performance Monitoring

### Development Performance
```bash
# Monitor build performance
npm run build -- --debug
npm run analyze

# Check bundle size
npx bundlephobia analyze package.json

# Lighthouse CI for performance
npm install -g @lhci/cli
lhci autorun --upload.target=temporary-public-storage
```

### Code Quality Metrics
```bash
# TypeScript strict checking
npx tsc --noEmit --strict

# Unused code detection
npx unimported

# Dependency analysis
npx depcheck
```

This development guide ensures consistent, high-quality code and efficient workflow for building the malabi e-commerce website.