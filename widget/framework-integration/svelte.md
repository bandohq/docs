---
description: Configure the Bando Widget in Svelte.
---

# Integration with Svelte

To use the Bando Widget with Svelte, you can follow these steps:

{% tabs %}

{% tab title="ReactAdapter.svelte" %}

```svelte
<script lang="ts">
import { createElement } from 'react'
import type { Root } from 'react-dom/client'
import { createRoot } from 'react-dom/client'
import { onDestroy, onMount } from 'svelte'

let container: HTMLDivElement
let root: Root

onMount(() => {
  const { element, children, class: _, ...props } = $$props
  try {
    root = createRoot(container)
    root.render(createElement(element, props, children))
  } catch (e) {
    console.warn('ReactAdapter failed to mount.', e)
  }
})

onDestroy(() => {
  try {
    root.unmount()
  } catch (e) {
    console.warn('ReactAdapter failed to unmount.', e)
  }
})
</script>

<div bind:this={container} class={$$props.class} />
```

{% endtab %}

{% tab title="lib/BandoWidget.svelte" %}

```svelte
<script>
import { BandoWidget } from '@bandohq/widget'
import ReactAdapter from './ReactAdapter.svelte'
</script>

<ReactAdapter
  element={BandoWidget}
  config={{
    theme: {
      container: {
        boxShadow: '0px 8px 32px rgba(0, 0, 0, 0.08)',
        borderRadius: '16px',
      },
    },
  }}
  integrator="svelte-example"
/>

<style>
  /**
   * Styling a React Component from within a Svelte Component.
   */
</style>
```

{% endtab %}

{% tab title="App.svelte" %}

```svelte
<script lang="ts">
import BandoWidget from './lib/BandoWidget.svelte'
</script>

<BandoWidget />

<style>
</style>
```

{% endtab %}

{% endtabs %}
