---
description: How to configure Bando's widget to work with both EVM and Solana chains.
---

# Connecting to EVM and Solana

By default, Bando's widget is ready to run transactions on **EVM chains**.  
If you also want to enable transactions on **Solana (SVM)**, you need to update the widget configuration.

## Configuration Example

```typescript
const widgetConfig: WidgetConfig = {
  chains: {
    types: {
      // Enable support for both Solana (SVM) and EVM chains
      allow: [ChainType.SVM, ChainType.EVM],
    },
  },
};
```

With this configuration, the widget will be able to process transactions on both chain types.


