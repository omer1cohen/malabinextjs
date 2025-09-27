# Malabi E-commerce Website

A modern e-commerce website built with Next.js for selling delicious malabi desserts.

## Tech Stack

- **Next.js 15.5.4** - React framework with App Router and Turbopack
- **React 19.1.0** - Frontend library
- **TypeScript** - Type-safe development
- **Tailwind CSS v4** - Utility-first CSS framework
- **shadcn/ui** - Beautifully designed components
- **Lucide React** - Beautiful & consistent icon pack
- **class-variance-authority** - Creating type-safe component variants

## Features

- 🛒 E-commerce functionality for malabi products
- 🎨 Modern UI with shadcn/ui components
- 📱 Responsive design with Tailwind CSS
- ⚡ Fast development with Turbopack
- 🔒 Type-safe with TypeScript
- 🎯 Component-based architecture

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn

### Installation

1. Clone the repository
```bash
git clone <repository-url>
cd malabinextjs
```

2. Install dependencies
```bash
npm install
```

3. Run the development server
```bash
npm run dev
```

4. Open [http://localhost:3000](http://localhost:3000) in your browser

## Available Scripts

- `npm run dev` - Start development server with Turbopack
- `npm run build` - Build the application for production
- `npm run start` - Start production server
- `npm run lint` - Run ESLint

## Project Structure

```
malabinextjs/
├── src/
│   ├── app/          # App Router pages and layouts
│   ├── components/   # React components
│   ├── lib/          # Utility functions and configurations
│   └── types/        # TypeScript definitions
├── public/           # Static assets
├── docs/             # Project documentation
├── components.json   # shadcn/ui configuration
└── package.json      # Dependencies and scripts
```

## 📚 Documentation

Comprehensive documentation is available in the `docs/` directory:

- **[🎨 Design System](docs/DESIGN.md)** - Design tokens, components, and styling guidelines
- **[🚀 Performance Guide](docs/PERFORMANCE.md)** - Optimization strategies and best practices
- **[🔗 Integrations](docs/INTEGRATIONS.md)** - WhatsApp, Analytics, and third-party services
- **[🛠️ Development Workflow](docs/DEVELOPMENT.md)** - Setup, coding standards, and Claude Code commands
- **[🌐 Deployment Guide](docs/DEPLOYMENT.md)** - Production checklist and deployment procedures
- **[📝 Changelog](CHANGELOG.md)** - Version history and release notes

## shadcn/ui Configuration

This project uses shadcn/ui with the following configuration:
- **Style**: New York
- **Base Color**: Neutral
- **CSS Variables**: Enabled
- **Icon Library**: Lucide React
- **RSC**: Enabled

## Development

The project is set up with:
- TypeScript for type safety
- ESLint for code linting
- Tailwind CSS for styling
- Component aliases configured in `tsconfig.json`

## Contributing

1. Create a feature branch
2. Make your changes
3. Test thoroughly
4. Submit a pull request

## License

Private project for malabi e-commerce website.
