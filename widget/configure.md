---
description: Configure the Bando Widget.
---

# Configuration

## Configure The Widget

With the Bando widget, you can configure the following options:

- Preselect default country
- Restrict products from countries or regions

### Initialize Form Values

The Bando Widget utilizes several form values to retrieve and execute routes. These include `fromToken`,`blockedCountries`, `product` and `country`. Typically, these values are set through the widget's UI, but they can also be programmatically initialized and updated.

By preconfiguring these options, you can enhance the user experience by ensuring that the widget is preloaded with the appropriate chains, tokens, amounts, and addresses for a transaction. This reduces the need for manual input, guiding users through a smoother flow.

#### How to Initialize:

- **Widget Configuration**: You can add values such as `fromToken\`, `product\`,`blockedCountries\`or`country\` directly into the widget configuration.
- **URL Search Parameters**: If the `buildUrl` option in the widget config is set to `true`, these form values can be added as URL parameters on the page where the widget is displayed.

When you set these form values via configuration or URL parameters, the corresponding form fields in the widget UI will automatically update to reflect those values.

#### Initialize by Config Values

The Bando Widget allows you to preconfigure default chains, products, and countries, making it easy to set up the desired parameters right from the start. Below is an example of how to configure the widget with specific default blocked countries, and country values.

```typescript
import type { WidgetConfig } from '@bandohq/widget';
import { ChainType } from '@bandohq/widget';

const widgetConfig: WidgetConfig = {
  // set fromToken
  fromToken: '0x0000000000000000000000000000000000000000',

  // set blocked countries
  blockedCountries: ['CN', 'RU'],

  // set country (example country)
  country: 'USA',

  // set product (product id)
  product: '1234-4567-8901',
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```

{% hint style="info" %}
You can look for the registered products [using our API](fulfiller-api/guides/get-available-products.md).
{% endhint %}

To set form values in the Bando widget using URL search parameters, you need to ensure that buildUrl is set to true in the widget config.

````typescript
import type { WidgetConfig } from '@bandohq/widget';

const widgetConfig: WidgetConfig = {
  // Enable the widget to use and construct URL search params
  buildUrl: true,
};
You can then include URL search params when navigating to the page that features the widget:

```js
https://yourwidgetpage.com/?country=USA
````

It's important to note that this method only works during the widget's initial load. Dynamically changing the URL search params without reloading the page will not update the form values inside the widget.

{% hint style="info" %}
If you are using URL search params to populate the widget's form values on initialization, make sure that the same values are not already defined in the widget config (such as blocked countries or country). The widget config takes priority, and any form values present in both the config and the URL will be overwritten by the config settings upon page load.
{% endhint %}

## Update Form Values

After the widget has initialized, there are two ways to update the form values:

1. **Using the widget config**: This method allows for reactive updates by changing the config values. It requires managing these values for updates.
2. **Using the `formRef`**: This method provides a function that allows you to programmatically update values stored in the widget's form.

#### Updating by Widget Config

Once the widget has loaded, you can modify the form values by updating the widget configuration. For example, to change values like `blockedCountries` or `fromToken`, you only need to include these values in the config. It’s recommended to also include a `formUpdateKey` to force the widget to recognize the update.

Here's an example configuration:

```typescript
import type { WidgetConfig } from '@bandohq/widget';

const widgetConfig: WidgetConfig = {
  blockedCountries: ['CN', 'RU'],
  fromToken: '0x0000000000000000000000000000000000000000',
  formUpdateKey: Date.now(),
};
```

You can also reset fields to an empty state by using `undefined`:

```typescript
const widgetConfig: WidgetConfig = {
  blockedCountries: undefined,
  fromToken: undefined,
  formUpdateKey: Date.now(),
};
```
