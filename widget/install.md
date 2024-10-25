---
description: "Install the widget page"
---

# Install the Bando Widget

To install the Bando Widget, you can follow these steps:

{% tabs %}

{% tab title="npm" %}

```bash
npm install @bandohq/widget
```

{% endtab %}

{% tab title="yarn" %}

```bash
yarn add @bandohq/widget
```

{% endtab %}

{% tab title="pnpm" %}

```bash
pnpm add @bandohq/widget
```

{% endtab %}

{% tab title="bun" %}

```bash
bun add @bandohq/widget
```

{% endtab %}
{% endtabs %}

## Example

Here is an example of how to use the Bando Widget:

```javascript
import { BandoWidget, WidgetConfig } from "@@bandohq/widget";

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
