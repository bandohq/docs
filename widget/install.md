---
description: "Install the widget page"
---

# Install the Bando Widget

To install the Bando Widget, you can follow these steps:

{% tabs %}

{% tab title="npm" %}

```bash
npm install @bando/widget
```

{% endtab %}

{% tab title="yarn" %}

```bash
yarn add @bando/widget
```

{% endtab %}

{% tab title="pnpm" %}

```bash
pnpm add @bando/widget
```

{% endtab %}

{% tab title="bun" %}

```bash
bun add @bando/widget
```

{% endtab %}
{% endtabs %}

## Example

Here is an example of how to use the Bando Widget:

```javascript
import { BandoWidget, WidgetConfig } from "@lifi/widget";

const widgetConfig: WidgetConfig = {
  theme: {
    container: {
      border: "1px solid rgb(234, 234, 234)",
      borderRadius: "16px",
    },
  },
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```
