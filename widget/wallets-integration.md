---
description: Learn how to integrate the Bando Widget inside wallet mini-apps (Farcaster, Binance, MiniPay, World App, and more).
---

# Third Party Wallet Integrations

Build once. Run anywhere.  
The **Bando Widget** is designed to work seamlessly inside wallet environments — even when your dApp runs as a mini-app.

By default, the widget is ready to run inside:

- **Farcaster**
- **Binance**
- **Opera MiniPay**

If you want to integrate it inside **World App**, you’ll just need one extra setup step.

---

## World App Integration

To use the Bando Widget inside **World App**, you must first install and configure the **World MiniKit**:

- [Install World MiniKit](https://docs.world.org/mini-apps/quick-start/installing)
- [Authenticate the user wallet](https://docs.world.org/mini-apps/commands/wallet-auth#using-the-command)

Once installed, simply pass the `MiniKit` object as a **transaction provider** to the Bando Widget.

### Example

```typescript
import { MiniKit } from "@worldcoin/minikit-js";

export const Widget = () => {
  const config = {
    buildUrl: true,
    transactionProvider: MiniKit,
  } as Partial<WidgetConfig>;

  return <BandoWidget integrator={integrator} config={config} />;
};
```
