# 🔗 Third-Party Integrations

## Overview
This document covers all third-party service integrations for the Malabi E-commerce website, including WhatsApp, analytics, payments, and social media connections.

## 📱 WhatsApp Integration

### 1. Order Processing via WhatsApp
```typescript
// lib/whatsapp.ts
const WHATSAPP_PHONE = process.env.NEXT_PUBLIC_WHATSAPP_NUMBER || '972501234567'

export interface OrderData {
  items: CartItem[]
  customer: CustomerDetails
  total: number
  orderNumber: string
}

export function sendWhatsAppOrder(orderData: OrderData) {
  const { items, customer, total, orderNumber } = orderData

  // Format order message in Hebrew
  const message = `
🍮 הזמנה חדשה - מספר ${orderNumber}

👤 פרטי לקוח:
שם: ${customer.name}
טלפון: ${customer.phone}
כתובת: ${customer.address}
${customer.email ? `אימייל: ${customer.email}` : ''}

🛒 פריטים בהזמנה:
${items.map(item =>
  `• ${item.name} x${item.quantity} - ₪${(item.price * item.quantity).toFixed(2)}`
).join('\n')}

💰 סה"כ לתשלום: ₪${total.toFixed(2)}

${customer.deliveryTime ? `⏰ זמן רצוי לאיסוף/משלוח: ${customer.deliveryTime}` : ''}
${customer.notes ? `📝 הערות: ${customer.notes}` : ''}

תודה רבה! 🙏
  `.trim()

  const encodedMessage = encodeURIComponent(message)
  const whatsappUrl = `https://wa.me/${WHATSAPP_PHONE}?text=${encodedMessage}`

  // Track conversion event
  trackEvent('order_submitted', {
    order_number: orderNumber,
    value: total,
    currency: 'ILS'
  })

  window.open(whatsappUrl, '_blank')
}
```

### 2. WhatsApp Business API Integration
```typescript
// lib/whatsapp-business.ts
interface WhatsAppBusinessConfig {
  accessToken: string
  phoneNumberId: string
  version: string
}

class WhatsAppBusinessAPI {
  private config: WhatsAppBusinessConfig

  constructor(config: WhatsAppBusinessConfig) {
    this.config = config
  }

  async sendMessage(to: string, message: string) {
    const url = `https://graph.facebook.com/v${this.config.version}/${this.config.phoneNumberId}/messages`

    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.config.accessToken}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        messaging_product: 'whatsapp',
        recipient_type: 'individual',
        to: to,
        type: 'text',
        text: { body: message }
      })
    })

    return response.json()
  }

  async sendOrderConfirmation(order: OrderData) {
    const message = `
🍮 אישור הזמנה - ${order.orderNumber}

שלום ${order.customer.name},
הזמנתך התקבלה בהצלחה!

📋 סיכום הזמנה:
${order.items.map(item => `• ${item.name} x${item.quantity}`).join('\n')}

💰 סכום לתשלום: ₪${order.total}

ניצור איתך קשר בקרוב לתיאום המשלוח.
תודה שבחרת במלבי הטעים! 🙏
    `.trim()

    return this.sendMessage(order.customer.phone, message)
  }
}

export const whatsappAPI = new WhatsAppBusinessAPI({
  accessToken: process.env.WHATSAPP_ACCESS_TOKEN!,
  phoneNumberId: process.env.WHATSAPP_PHONE_NUMBER_ID!,
  version: 'v18.0'
})
```

### 3. WhatsApp Widget Component
```tsx
// components/WhatsAppWidget.tsx
'use client'

import { MessageCircle } from 'lucide-react'
import { useState } from 'react'

export default function WhatsAppWidget() {
  const [isVisible, setIsVisible] = useState(true)
  const phoneNumber = process.env.NEXT_PUBLIC_WHATSAPP_NUMBER

  const openWhatsApp = () => {
    const message = encodeURIComponent('שלום! אני מעוניין/ת לקבל מידע על המלבי שלכם 🍮')
    const whatsappUrl = `https://wa.me/${phoneNumber}?text=${message}`
    window.open(whatsappUrl, '_blank')
  }

  if (!isVisible) return null

  return (
    <div className="fixed bottom-6 left-6 z-50">
      <button
        onClick={openWhatsApp}
        className="bg-green-500 hover:bg-green-600 text-white p-4 rounded-full shadow-lg transition-all duration-300 transform hover:scale-110 animate-pulse-glow"
        aria-label="פתח WhatsApp"
      >
        <MessageCircle size={24} />
      </button>

      {/* Tooltip */}
      <div className="absolute bottom-full left-1/2 transform -translate-x-1/2 mb-2 px-3 py-2 bg-gray-800 text-white text-sm rounded-lg opacity-0 group-hover:opacity-100 transition-opacity whitespace-nowrap">
        שלח הודעה ב-WhatsApp
      </div>
    </div>
  )
}
```

## 📊 Google Analytics Integration

### 1. Google Analytics 4 Setup
```typescript
// lib/analytics.ts
export const GA_TRACKING_ID = process.env.NEXT_PUBLIC_GA_ID

declare global {
  interface Window {
    gtag: (...args: any[]) => void
  }
}

// Track page views
export const pageview = (url: string) => {
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('config', GA_TRACKING_ID, {
      page_path: url,
    })
  }
}

// Track events
export const trackEvent = (
  action: string,
  parameters: {
    event_category?: string
    event_label?: string
    value?: number
    currency?: string
    [key: string]: any
  } = {}
) => {
  if (typeof window !== 'undefined' && window.gtag) {
    window.gtag('event', action, parameters)
  }
}

// E-commerce tracking
export const trackPurchase = (orderData: {
  transaction_id: string
  value: number
  currency: string
  items: Array<{
    item_id: string
    item_name: string
    category: string
    quantity: number
    price: number
  }>
}) => {
  trackEvent('purchase', orderData)
}

export const trackAddToCart = (item: {
  item_id: string
  item_name: string
  category: string
  quantity: number
  price: number
}) => {
  trackEvent('add_to_cart', {
    currency: 'ILS',
    value: item.price * item.quantity,
    items: [item]
  })
}

export const trackBeginCheckout = (items: any[], value: number) => {
  trackEvent('begin_checkout', {
    currency: 'ILS',
    value,
    items
  })
}
```

### 2. Analytics Component
```tsx
// components/Analytics.tsx
'use client'

import Script from 'next/script'
import { usePathname, useSearchParams } from 'next/navigation'
import { useEffect } from 'react'
import { pageview } from '@/lib/analytics'

export default function Analytics() {
  const pathname = usePathname()
  const searchParams = useSearchParams()

  useEffect(() => {
    const url = pathname + searchParams.toString()
    pageview(url)
  }, [pathname, searchParams])

  return (
    <>
      <Script
        strategy="afterInteractive"
        src={`https://www.googletagmanager.com/gtag/js?id=${process.env.NEXT_PUBLIC_GA_ID}`}
      />
      <Script
        id="google-analytics"
        strategy="afterInteractive"
        dangerouslySetInnerHTML={{
          __html: `
            window.dataLayer = window.dataLayer || [];
            function gtag(){dataLayer.push(arguments);}
            gtag('js', new Date());
            gtag('config', '${process.env.NEXT_PUBLIC_GA_ID}', {
              page_path: window.location.pathname,
            });
          `,
        }}
      />
    </>
  )
}
```

## 💳 Payment Integration (Future)

### 1. Stripe Integration Setup
```typescript
// lib/stripe.ts
import { Stripe } from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2023-10-16',
})

export async function createPaymentIntent(amount: number, currency = 'ils') {
  try {
    const paymentIntent = await stripe.paymentIntents.create({
      amount: amount * 100, // Convert to agorot
      currency,
      metadata: {
        business: 'malabi-shop'
      }
    })

    return paymentIntent
  } catch (error) {
    console.error('Error creating payment intent:', error)
    throw error
  }
}

export async function confirmPayment(paymentIntentId: string) {
  try {
    const paymentIntent = await stripe.paymentIntents.confirm(paymentIntentId)
    return paymentIntent
  } catch (error) {
    console.error('Error confirming payment:', error)
    throw error
  }
}
```

### 2. Payment Component
```tsx
// components/PaymentForm.tsx
'use client'

import { useState } from 'react'
import { loadStripe } from '@stripe/stripe-js'
import {
  Elements,
  CardElement,
  useStripe,
  useElements
} from '@stripe/react-stripe-js'

const stripePromise = loadStripe(process.env.NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY!)

interface PaymentFormProps {
  amount: number
  onSuccess: (paymentIntent: any) => void
  onError: (error: string) => void
}

function CheckoutForm({ amount, onSuccess, onError }: PaymentFormProps) {
  const stripe = useStripe()
  const elements = useElements()
  const [isLoading, setIsLoading] = useState(false)

  const handleSubmit = async (event: React.FormEvent) => {
    event.preventDefault()

    if (!stripe || !elements) return

    setIsLoading(true)

    try {
      // Create payment intent
      const response = await fetch('/api/create-payment-intent', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ amount })
      })

      const { clientSecret } = await response.json()

      // Confirm payment
      const { error, paymentIntent } = await stripe.confirmCardPayment(clientSecret, {
        payment_method: {
          card: elements.getElement(CardElement)!,
        }
      })

      if (error) {
        onError(error.message!)
      } else {
        onSuccess(paymentIntent)
      }
    } catch (error) {
      onError('שגיאה בעיבוד התשלום')
    } finally {
      setIsLoading(false)
    }
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-6">
      <div className="p-4 border rounded-lg">
        <CardElement
          options={{
            style: {
              base: {
                fontSize: '16px',
                color: '#424770',
                '::placeholder': {
                  color: '#aab7c4',
                },
              },
            },
          }}
        />
      </div>

      <button
        type="submit"
        disabled={!stripe || isLoading}
        className="w-full btn-primary disabled:opacity-50"
      >
        {isLoading ? 'מעבד תשלום...' : `שלם ₪${amount}`}
      </button>
    </form>
  )
}

export default function PaymentForm(props: PaymentFormProps) {
  return (
    <Elements stripe={stripePromise}>
      <CheckoutForm {...props} />
    </Elements>
  )
}
```

## 🔍 SEO & Meta Tags

### 1. Dynamic Meta Tags
```tsx
// app/layout.tsx
import { Metadata } from 'next'

export const metadata: Metadata = {
  title: {
    template: '%s | מלבי הטעים',
    default: 'מלבי הטעים - הטעם הכי טוב בעיר',
  },
  description: 'מלבי טעים ואיכותי עם מגוון טעמים מיוחדים. הזמינו עכשיו ותיהנו ממלבי הטוב ביותר בעיר!',
  keywords: ['מלבי', 'קינוח', 'טעים', 'משלוחים', 'הזמנות'],
  authors: [{ name: 'מלבי הטעים' }],
  creator: 'מלבי הטעים',
  publisher: 'מלבי הטעים',
  formatDetection: {
    email: false,
    address: false,
    telephone: false,
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
        alt: 'מלבי הטעים',
      },
    ],
  },
  twitter: {
    card: 'summary_large_image',
    title: 'מלבי הטעים - הטעם הכי טוב בעיר',
    description: 'מלבי טעים ואיכותי עם מגוון טעמים מיוחדים',
    images: ['https://malabi.co.il/og-image.jpg'],
  },
  robots: {
    index: true,
    follow: true,
    googleBot: {
      index: true,
      follow: true,
      'max-video-preview': -1,
      'max-image-preview': 'large',
      'max-snippet': -1,
    },
  },
}
```

### 2. Structured Data (JSON-LD)
```tsx
// components/StructuredData.tsx
interface Product {
  name: string
  description: string
  price: number
  image: string
  category: string
}

interface StructuredDataProps {
  product?: Product
  business?: {
    name: string
    address: string
    phone: string
    email: string
  }
}

export default function StructuredData({ product, business }: StructuredDataProps) {
  const productSchema = product ? {
    "@context": "https://schema.org",
    "@type": "Product",
    "name": product.name,
    "description": product.description,
    "image": product.image,
    "category": product.category,
    "offers": {
      "@type": "Offer",
      "price": product.price,
      "priceCurrency": "ILS",
      "availability": "https://schema.org/InStock"
    }
  } : null

  const businessSchema = business ? {
    "@context": "https://schema.org",
    "@type": "LocalBusiness",
    "name": business.name,
    "address": business.address,
    "telephone": business.phone,
    "email": business.email,
    "servesCuisine": "קינוחים",
    "priceRange": "₪₪"
  } : null

  return (
    <>
      {productSchema && (
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: JSON.stringify(productSchema)
          }}
        />
      )}
      {businessSchema && (
        <script
          type="application/ld+json"
          dangerouslySetInnerHTML={{
            __html: JSON.stringify(businessSchema)
          }}
        />
      )}
    </>
  )
}
```

## 📧 Email Integration

### 1. Resend Email Service
```typescript
// lib/email.ts
import { Resend } from 'resend'

const resend = new Resend(process.env.RESEND_API_KEY)

export async function sendOrderConfirmation(orderData: {
  customerEmail: string
  customerName: string
  orderNumber: string
  items: any[]
  total: number
}) {
  try {
    const { data, error } = await resend.emails.send({
      from: 'הזמנות <orders@malabi.co.il>',
      to: [orderData.customerEmail],
      subject: `אישור הזמנה ${orderData.orderNumber} - מלבי הטעים`,
      html: `
        <div dir="rtl" style="font-family: Arial, sans-serif;">
          <h2>שלום ${orderData.customerName},</h2>
          <p>הזמנתך התקבלה בהצלחה! 🍮</p>

          <h3>פרטי ההזמנה:</h3>
          <p><strong>מספר הזמנה:</strong> ${orderData.orderNumber}</p>

          <h4>פריטים:</h4>
          <ul>
            ${orderData.items.map(item =>
              `<li>${item.name} x${item.quantity} - ₪${(item.price * item.quantity).toFixed(2)}</li>`
            ).join('')}
          </ul>

          <p><strong>סה"כ: ₪${orderData.total.toFixed(2)}</strong></p>

          <p>ניצור איתך קשר בקרוב לתיאום המשלוח.</p>
          <p>תודה שבחרת במלבי הטעים!</p>
        </div>
      `
    })

    if (error) {
      console.error('Email error:', error)
      return { success: false, error }
    }

    return { success: true, data }
  } catch (error) {
    console.error('Email sending failed:', error)
    return { success: false, error }
  }
}
```

## 🚀 Social Media Integration

### 1. Instagram Feed
```typescript
// lib/instagram.ts
interface InstagramPost {
  id: string
  caption: string
  media_url: string
  media_type: 'IMAGE' | 'VIDEO' | 'CAROUSEL_ALBUM'
  permalink: string
  timestamp: string
}

export async function getInstagramFeed(): Promise<InstagramPost[]> {
  try {
    const accessToken = process.env.INSTAGRAM_ACCESS_TOKEN
    const response = await fetch(
      `https://graph.instagram.com/me/media?fields=id,caption,media_url,media_type,permalink,timestamp&access_token=${accessToken}`
    )

    const data = await response.json()
    return data.data || []
  } catch (error) {
    console.error('Failed to fetch Instagram feed:', error)
    return []
  }
}
```

### 2. Social Share Buttons
```tsx
// components/SocialShare.tsx
interface SocialShareProps {
  url: string
  title: string
  description?: string
}

export default function SocialShare({ url, title, description }: SocialShareProps) {
  const shareOnFacebook = () => {
    const facebookUrl = `https://www.facebook.com/sharer/sharer.php?u=${encodeURIComponent(url)}`
    window.open(facebookUrl, '_blank', 'width=600,height=400')
  }

  const shareOnWhatsApp = () => {
    const text = `${title} - ${description || ''}`
    const whatsappUrl = `https://wa.me/?text=${encodeURIComponent(text + ' ' + url)}`
    window.open(whatsappUrl, '_blank')
  }

  const shareOnInstagram = () => {
    // Instagram doesn't support direct sharing, so copy to clipboard
    navigator.clipboard.writeText(url)
    alert('הקישור הועתק! אפשר להדביק באינסטגרם')
  }

  return (
    <div className="flex space-x-4 rtl:space-x-reverse">
      <button
        onClick={shareOnFacebook}
        className="btn-secondary"
        aria-label="שתף בפייסבוק"
      >
        📘 Facebook
      </button>

      <button
        onClick={shareOnWhatsApp}
        className="btn-secondary"
        aria-label="שתף בווטסאפ"
      >
        💬 WhatsApp
      </button>

      <button
        onClick={shareOnInstagram}
        className="btn-secondary"
        aria-label="שתף באינסטגרם"
      >
        📸 Instagram
      </button>
    </div>
  )
}
```

## 🔧 Environment Variables

### Required Environment Variables
```env
# .env.local

# WhatsApp
NEXT_PUBLIC_WHATSAPP_NUMBER=972501234567
WHATSAPP_ACCESS_TOKEN=your_whatsapp_business_access_token
WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id

# Analytics
NEXT_PUBLIC_GA_ID=G-XXXXXXXXX

# Payments (Future)
STRIPE_SECRET_KEY=sk_test_...
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...

# Email
RESEND_API_KEY=re_...

# Social Media
INSTAGRAM_ACCESS_TOKEN=your_instagram_access_token
FACEBOOK_APP_ID=your_facebook_app_id

# Business Info
NEXT_PUBLIC_BUSINESS_NAME=מלבי הטעים
NEXT_PUBLIC_BUSINESS_EMAIL=info@malabi.co.il
NEXT_PUBLIC_BUSINESS_PHONE=050-123-4567
NEXT_PUBLIC_BUSINESS_ADDRESS=רחוב הדוגמה 123, תל אביב
```

## 📊 Integration Testing

### Test WhatsApp Integration
```typescript
// __tests__/whatsapp.test.ts
import { sendWhatsAppOrder } from '@/lib/whatsapp'

describe('WhatsApp Integration', () => {
  test('formats order message correctly', () => {
    const orderData = {
      orderNumber: 'ORD-001',
      customer: {
        name: 'יוסי כהן',
        phone: '050-123-4567',
        address: 'רחוב הדוגמה 123',
        email: 'yossi@example.com'
      },
      items: [
        { name: 'מלבי וניל', quantity: 2, price: 15 }
      ],
      total: 30
    }

    // Test message formatting
    const spy = jest.spyOn(window, 'open').mockImplementation(() => null)
    sendWhatsAppOrder(orderData)

    expect(spy).toHaveBeenCalledWith(
      expect.stringContaining('wa.me'),
      '_blank'
    )
  })
})
```

This integration guide covers all major third-party services needed for a successful malabi e-commerce website.