# API Documentation - Netlify Feature Tour

A comprehensive guide to all public APIs, functions, and components in the Netlify Feature Tour project.

## Table of Contents

1. [Project Overview](#project-overview)
2. [Components](#components)
3. [Pages](#pages)
4. [Configuration](#configuration)
5. [Styling](#styling)
6. [Usage Examples](#usage-examples)
7. [Development](#development)

## Project Overview

The Netlify Feature Tour is an Astro-based demonstration site showcasing key Netlify features including:
- Deploy Previews
- Instant Rollbacks
- Netlify Functions
- Netlify Forms

### Technology Stack
- **Framework**: Astro v2.0.0
- **UI Library**: Preact
- **Styling**: CSS-in-JS with Astro styles
- **Deployment**: Netlify

---

## Components

### 1. Layout Component (`src/components/layout.astro`)

The main layout component that wraps all pages with consistent styling and structure.

#### Props
```typescript
interface LayoutProps {
  title: string;           // Page title for <head> and h1
  description: string;     // Page description for meta and display
  home?: boolean;          // Optional flag to hide back navigation (default: false)
}
```

#### Usage Example
```astro
---
import Layout from '../components/layout.astro';
---

<Layout
  title="Page Title"
  description="Page description text"
  home={true}
>
  <main>
    <!-- Your page content here -->
  </main>
</Layout>
```

#### Features
- Responsive design with CSS Grid
- SEO-optimized meta tags
- Font preloading for performance
- Favicon and manifest configuration
- Consistent footer with legal links
- CSS custom properties for theming

#### CSS Custom Properties
```css
:root {
  --heading: var(--neutral-dark-900);
  --text: var(--neutral-dark-900);
  --text-muted: var(--neutral-dark-600);
  --primary: var(--blue-700);
  --primary-darker: var(--blue-800);
  --highlight: var(--teal-300);
  --button: var(--neutral-light-000);
  --lightBorder: var(--neutral-light-200);
  --light: var(--neutral-light-100);
  --white: var(--neutral-light-000);
  --card-radius: var(--border-radius-3);
  --font-primary: Pacaembu, sans-serif;
  --font-secondary: Mulish, sans-serif;
}
```

### 2. Function Tester Component (`src/components/function-tester.jsx`)

Interactive Preact component for testing Netlify Functions with real-time feedback.

#### Props
```typescript
interface FunctionTesterProps {
  children: React.ReactNode;  // Code example to display
}
```

#### Sub-components

##### ExerciseStart
Displays instructions and test button for function creation.

```typescript
interface ExerciseStartProps {
  handleClick: () => void;
  output: string | undefined;
  children: React.ReactNode;
}
```

##### ExerciseFinish
Success message component shown after successful function test.

#### Usage Example
```jsx
import FunctionTester from '../components/function-tester.jsx';
import { Code } from 'astro/components';

const functionCode = `
exports.handler = async () => {
  return {
    statusCode: 200,
    body: 'hello world!',
  };
};
`;

<FunctionTester client:visible>
  <Code code={functionCode} lang="js" />
</FunctionTester>
```

#### API Endpoints Used
- `GET /.netlify/functions/hello-world` - Tests the user-created function

#### State Management
- Uses Preact's `useState` for managing test output
- Handles success/error states with conditional rendering

---

## Pages

### 1. Homepage (`src/pages/index.astro`)

The main landing page showcasing all Netlify features as clickable cards.

#### Features
- Responsive grid layout (1-4 columns based on screen size)
- Feature cards with descriptions and navigation
- Semantic HTML structure

#### Navigation Cards
1. **Deploy Previews** → `/deploy-previews`
2. **Instant Rollbacks** → `/instant-rollbacks`
3. **Netlify Functions** → `/functions`
4. **Netlify Forms** → `/netlify-forms`

#### Usage
```astro
---
import Layout from '../components/layout.astro';
---

<Layout
  title="Netlify Feature Tour"
  description="This is a small site to show the most powerful features of Netlify quickly!"
  home
>
  <main class="steps">
    <!-- Feature cards -->
  </main>
</Layout>
```

### 2. Functions Page (`src/pages/functions.astro`)

Interactive demonstration of Netlify Functions with code examples.

#### Features
- Live code example with syntax highlighting
- Interactive testing component
- Documentation links

#### Dependencies
- `astro/components` Code component
- `function-tester.jsx` component

#### Usage Example
```astro
---
import { Code } from 'astro/components';
import Layout from '../components/layout.astro';
import FunctionTester from '../components/function-tester.jsx';

const functionCode = `
exports.handler = async () => {
  return {
    statusCode: 200,
    body: 'hello world!',
  };
};
`.trim();
---

<Layout
  title="Netlify Functions"
  description="Serverless functions demonstration"
>
  <main>
    <FunctionTester client:visible>
      <Code code={functionCode} lang="js" />
    </FunctionTester>
  </main>
</Layout>
```

### 3. Netlify Forms Page (`src/pages/netlify-forms.astro`)

Demonstrates HTML form handling with Netlify Forms.

#### Form Configuration
```html
<form
  class="form"
  method="POST"
  name="contact"
  action="/success"
>
  <!-- Form fields -->
</form>
```

#### Form Fields
- **Name**: `<input type="text" name="name" />`
- **Email**: `<input type="email" name="email" />`
- **Message**: `<textarea name="message"></textarea>`

#### Features
- Accessible form labels
- Responsive form layout
- Auto-submission handling via Netlify

### 4. Deploy Previews Page (`src/pages/deploy-previews.astro`)

Information page about Netlify's Deploy Preview feature.

#### Content
- Step-by-step instructions for testing deploy previews
- GitHub workflow integration
- Links to documentation

### 5. Instant Rollbacks Page (`src/pages/instant-rollbacks.astro`)

Visual demonstration of the rollback feature with explanatory image.

#### Features
- Centered layout with image
- Humorous illustration of deployment concerns
- Documentation links

### 6. Success Page (`src/pages/success.astro`)

Simple confirmation page for form submissions.

#### Usage
- Automatically displayed after form submission
- Minimal design with success message

---

## Configuration

### Astro Configuration (`astro.config.mjs`)

```javascript
import { defineConfig } from "astro/config";
import preact from "@astrojs/preact";
import sitemap from '@astrojs/sitemap';

export default defineConfig({
  site: 'https://feature-tour.netlify.app',
  integrations: [
    preact(),
    sitemap({
      canonicalURL: 'https://feature-tour.netlify.app'
    })
  ],
});
```

#### Integrations
- **Preact**: Enables React components in Astro
- **Sitemap**: Generates XML sitemap for SEO

### Package Configuration (`package.json`)

```json
{
  "name": "@example/starter",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "dev": "astro dev",
    "start": "astro dev",
    "build": "astro build",
    "preview": "astro preview"
  },
  "devDependencies": {
    "@astrojs/preact": "^1.0.2",
    "@astrojs/sitemap": "^1.0.0",
    "@netlify/netlify-marketing-tokens": "^1.1.0",
    "astro": "^2.0.0"
  }
}
```

---

## Styling

### Global Styles (`public/global.css`)

Contains base styles and utility classes used throughout the application.

### Component Styles

Each component uses scoped CSS with Astro's styling system:

```astro
<style>
  .component-class {
    /* Scoped styles */
  }
</style>
```

### CSS Architecture

- **Custom Properties**: Consistent theming system
- **Responsive Design**: Mobile-first approach
- **Accessibility**: Focus states and semantic HTML
- **Performance**: Optimized font loading

---

## Usage Examples

### Creating a New Feature Page

1. **Create the page file**:
```astro
---
// src/pages/new-feature.astro
import Layout from '../components/layout.astro';
---

<Layout
  title="New Feature"
  description="Description of the new feature"
>
  <main>
    <h2>New Feature Content</h2>
    <p>Feature description and examples</p>
  </main>
</Layout>
```

2. **Add navigation card to homepage**:
```astro
<section class="step">
  <h2 class="stepHeading">New Feature</h2>
  <p>Feature description for the card</p>
  <a href="/new-feature" class="button">
    Learn more
  </a>
</section>
```

### Adding Interactive Components

1. **Create a Preact component**:
```jsx
// src/components/my-component.jsx
import { useState } from "preact/hooks";

export default function MyComponent({ title }) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>{title}</h2>
      <button onClick={() => setCount(count + 1)}>
        Count: {count}
      </button>
    </div>
  );
}
```

2. **Use in an Astro page**:
```astro
---
import MyComponent from '../components/my-component.jsx';
---

<MyComponent client:visible title="Interactive Demo" />
```

### Creating Netlify Functions

1. **Create function file**:
```javascript
// netlify/functions/my-function.js
exports.handler = async (event, context) => {
  return {
    statusCode: 200,
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      message: 'Hello from Netlify Functions!',
      timestamp: new Date().toISOString(),
    }),
  };
};
```

2. **Test the function**:
```javascript
// In a component or page
fetch('/.netlify/functions/my-function')
  .then(response => response.json())
  .then(data => console.log(data));
```

---

## Development

### Getting Started

1. **Clone and install**:
```bash
git clone <repository-url>
cd feature-tour
npm install
```

2. **Development server**:
```bash
npm run dev
# or
netlify dev  # For Netlify Functions support
```

3. **Build for production**:
```bash
npm run build
```

4. **Preview production build**:
```bash
npm run preview
```

### File Structure

```
src/
├── components/
│   ├── layout.astro          # Main layout component
│   └── function-tester.jsx   # Interactive function tester
├── pages/
│   ├── index.astro           # Homepage
│   ├── functions.astro       # Functions demo
│   ├── netlify-forms.astro   # Forms demo
│   ├── deploy-previews.astro # Deploy previews info
│   ├── instant-rollbacks.astro # Rollbacks info
│   └── success.astro         # Form success page
└── env.d.ts                  # TypeScript definitions
```

### Best Practices

1. **Component Organization**: Keep components focused and reusable
2. **Styling**: Use CSS custom properties for consistent theming
3. **Accessibility**: Include proper ARIA labels and semantic HTML
4. **Performance**: Leverage Astro's static generation and island architecture
5. **SEO**: Use proper meta tags and structured data

### Extending the Project

1. **Add new Netlify features**: Create new pages following the existing pattern
2. **Enhance interactivity**: Add more Preact components with `client:visible`
3. **Improve styling**: Extend the CSS custom properties system
4. **Add API endpoints**: Create new Netlify Functions for backend functionality

---

## API Reference Summary

### Components API

| Component | Props | Description |
|-----------|-------|-------------|
| `Layout` | `title`, `description`, `home?` | Main layout wrapper |
| `FunctionTester` | `children` | Interactive function testing |

### Pages API

| Page | Route | Purpose |
|------|-------|---------|
| `index.astro` | `/` | Homepage with feature cards |
| `functions.astro` | `/functions` | Netlify Functions demo |
| `netlify-forms.astro` | `/netlify-forms` | Forms demo |
| `deploy-previews.astro` | `/deploy-previews` | Deploy previews info |
| `instant-rollbacks.astro` | `/instant-rollbacks` | Rollbacks info |
| `success.astro` | `/success` | Form submission success |

### Function Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/.netlify/functions/hello-world` | GET | Example function for testing |

This documentation provides a complete reference for all public APIs, functions, and components in the Netlify Feature Tour project, with practical examples for development and extension.