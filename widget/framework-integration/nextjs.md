---
description: "Detailed guide for integrating Bando's payment widget with Next.js. Learn how to add crypto payments to your Next.js application."
---

# Integration with Next.js

Because Next.js uses server-side rendering (SSR) and wallet libraries require client-side handling, the Bando Widget must be rendered on the client side. To achieve this, you should use the `'use client'` directive, which ensures the widget is loaded only in the client environment, bypassing SSR.

Here’s the setup for integrating the Bando widget with Next.js:

{% tabs %}

{% tab title="Widget.tsx" %}

```tsx
"use client";

import type { WidgetConfig } from "@bandohq/widget";
import { BandoWidget, WidgetSkeleton } from "@bandohq/widget";
import { ClientOnly } from "./ClientOnly";

export function Widget() {
  const config = {
    appearance: "light",
    theme: {
      container: {
        boxShadow: "0px 8px 32px rgba(0, 0, 0, 0.08)",
        borderRadius: "16px",
      },
    },
  } as Partial<WidgetConfig>;

  return (
    <div>
      {/* Renders the widget only after JavaScript is active on the client side, displaying the fallback during the server-side render */}
      <ClientOnly fallback={<WidgetSkeleton config={config} />}>
        <BandoWidget config={config} integrator="nextjs-example" />
      </ClientOnly>
    </div>
  );
}
```

{% endtab %}

{% tab title="ClientOnly.tsx" %}

```tsx
import { useSyncExternalStore, type PropsWithChildren } from "react";

function subscribe() {
  return () => {};
}

/**
 * Determines if JavaScript has hydrated, indicating that it's client-rendered.
 * For SSR, it will always return false, but once hydrated on the client,
 * it remains true for subsequent renders.
 */
export function useHydrated() {
  return useSyncExternalStore(
    subscribe,
    () => true,
    () => false
  );
}

interface ClientOnlyProps extends PropsWithChildren {
  fallback?: React.ReactNode;
}

/**
 * Only renders children components once JavaScript has loaded on the client.
 * Shows an optional fallback component until hydration is complete.
 */
export function ClientOnly({ children, fallback = null }: ClientOnlyProps) {
  const hydrated = useHydrated();
  return hydrated ? children : fallback;
}
```

{% endtab %}

{% tab title="App.tsx" %}

```tsx
import { Widget } from "./components/Widget";

export default function Home() {
  return (
    <main>
      <Widget />
    </main>
  );
}
```

{% endtabs %}

Using `'use client'` at the beginning of your component file ensures the widget operates strictly on the client side, making it compatible with SSR in Next.js.
