---
description: Integration with Vue.js
---

# Integration with Vue.js

To use the Bando Widget with Vue.js, you can follow these steps:

{% tabs %}

{% tab title="Widget.vue" %}

```vue
<template>
  <Widget :config="config" />
</template>

<script lang="ts">
// This is a React component
import { BandoWidget } from "@bandohq/widget";
import { applyPureReactInVue } from "veaury";

export default {
  components: {
    // Use HOC 'applyReactInVue' or 'applyPureReactInVue'
    // Basic: applyReactInVue(BandoWidget),
    Widget: applyPureReactInVue(BandoWidget),
  },
  setup() {
    return {
      config: {
        theme: {
          container: {
            border: "1px solid rgb(234, 234, 234)",
            borderRadius: "16px",
          },
        },
        integrator: "vue-example",
      },
    };
  },
};
</script>
```

{% endtab %}

{% tab title="App.vue" %}

```vue
<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import Widget from "./components/Widget.vue";
</script>

<template>
  <Widget msg="LI.FI Widget + Vue + Vite" />
</template>
```

{% endtabs %}
