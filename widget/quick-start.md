---
description: Overview and installation guide for the Bando Spend Widget.
---

# Bando Widget

## Overview

The **Bando Widget** is a collection of prebuilt UI components designed to simplify the purchase of products and payment for services across various institutions using tokens and other digital assets. It provides flexible styling options to seamlessly integrate with your web application, helping you enhance your platform’s functionality and attract a broader user base.

### Features

- Supports all ecosystems, chains, and products available in Bando.
- Flexible styling options to match your app's design.
- Built-in wallet management UI, with the option to use your own (Wagmi, Solana).
- Fully localized UI, adapting to your users’ preferred language.
- Compatibility with multiple frontend frameworks:
  - **React**, **Next.js**, **Vue**, **Nuxt.js**, **Svelte**, **Remix**, **Gatsby**, **Vite**, **CRA**, **RainbowKit**.

---

## Installation

To install the Bando Widget, follow the instructions for your package manager:

{% tabs %}
{% tab title="npm" %}

```bash
npm install @bandohq/widget wagmi @bigmi/react @solana/wallet-adapter-react @tanstack/react-query
```

{% endtab %}

{% tab title="yarn" %}

```bash
yarn add @bandohq/widget wagmi @bigmi/react @solana/wallet-adapter-react @tanstack/react-query
```

{% endtab %}

{% tab title="pnpm" %}

```bash
pnpm add @bandohq/widget wagmi @bigmi/react @solana/wallet-adapter-react @tanstack/react-query
```

{% endtab %}

{% tab title="bun" %}

```bash
bun add @bandohq/widget wagmi @bigmi/react @solana/wallet-adapter-react @tanstack/react-query
```

{% endtab %}
{% endtabs %}

---

## Getting Started

Once the widget is installed, you can integrate it into your application with the following steps:

### 1. Import and Configure the Widget

Create a configuration object to customize the widget’s appearance and behavior:

```tsx
import { BandoWidget, WidgetConfig } from '@bandohq/widget';

const widgetConfig: WidgetConfig = {
  theme: {
    container: {
      border: '1px solid rgb(234, 234, 234)',
      borderRadius: '16px',
    },
  },
};
```

### 2. Render the Widget

Use the `BandoWidget` component inside your app, passing the `config` and `integrator` props:

```tsx
export const WidgetPage = () => {
  return <BandoWidget integrator="<your_app_name>" config={widgetConfig} />;
};
```

---

## Next Steps

- **[Configure the Widget](widget/configuration.md)** – Learn how to adjust settings.
- **[Localization](widget/localization.md)** – Customize the language support.
- **[Customize Styles](widget/customize-styles.md)** – Modify UI elements to match your brand.
- **[Wallet Management](widget/wallet-management.md)** – Manage wallets with built-in options or external solutions.
- **Guides:**
  - [Next.js](widget/guides/next.md)
  - [Svelte](widget/guides/svelte.md)

---

This guide should give you everything you need to get started with the Bando Widget. If you encounter any issues, refer to our documentation or reach out to our support team.
