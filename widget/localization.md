---
description: Bando's widget internationalizaion.
---

# Localization

Bando's Widget provides internationalization (i18n) support for your app, allowing you to customize the widget's appearance and behavior according to your language preferences.

## Configure languages

The default language is set to English (`en`), but you have the option to set a different default language. You can also control which languages are available in the widget or specify the language that users will see if the built-in language selector is hidden.

There are `allow`, `deny`, and `default` options for language configuration.

```javascript
import { BandoWidget, WidgetConfig } from "@bandohq/widget";

const widgetConfig: WidgetConfig = {
  // hide the language selection part of the UI
  // hiddenUI: ['language'],
  languages: {
    // default to English
    default: "en",
    // allow English and Spanish languages only
    allow: ["en", "es"],
    // disable Chinese from being shown in the languages list
    // deny: ['zh'],
  },
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```

## Language resources

You can configure the widget to accommodate any language required by your dApp by supplying the necessary language resources.

{% tabs %}
{% tab title="WidgetPage.tsx" %}
```javascript
import { BandoWidget, WidgetConfig } from "@bandohq/widget";
import es from "./i18n/es.json";

const widgetConfig: WidgetConfig = {
  languageResources: {
    es,
  },
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```
{% endtab %}

{% tab title="es.json" %}
```json
{
  "language": {
    "name": "Español",
    "title": "Idioma"
  }
}
```
{% endtab %}
{% endtabs %}

Additionally, you can modify the existing language resources if you need to change specific text. You can find the full set of key-value pairs in the reference `en.json` file available in [our repository](https://github.com/bandohq/widget).

```javascript
import { BandoWidget, WidgetConfig } from "@bandohq/widget";

const widgetConfig: WidgetConfig = {
  languageResources: {
    en: {
      button: { product: "A thing that I want" },
    },
  },
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```
