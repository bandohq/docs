---
description: Wallet Management for the Bando Widget.
---

# Wallet Management

Configure your widget to enable wallet management for Bando, allowing users to buy products and pay for services using tokens. You can connect the widget to different blockchain environments like EVM (Ethereum Virtual Machine), Solana, UTXO-based chains, and more. Below are examples of how to integrate wallet management within your project.

## EVM Wallet Connection

To handle wallet connections for EVM (Ethereum Virtual Machine) chains in Bando, the widget leverages the [Wagmi](https://wagmi.sh/) library for managing wallets and chain switching, and it also fully supports integration with Wagmi-based tools such as [RainbowKit](https://www.rainbowkit.com/). This ensures seamless wallet interactions across various EVM networks.

If your dApp already uses Wagmi or a Wagmi-based library, the widget automatically detects when it is wrapped in [WagmiProvider](https://wagmi.sh/react/api/WagmiProvider), allowing it to continue using the existing wallet management setup without needing further configuration.

The following example demonstrates how to set up basic wallet management using Wagmi in Bando.

```jsx
import { BandoWidget } from "@bando/widget";
import { createClient } from "viem";
import { WagmiProvider, createConfig, http } from "wagmi";
import { mainnet, arbitrum, optimism, scroll } from "wagmi/chains";
import { injected } from "wagmi/connectors";

const wagmiConfig = createConfig({
  // Make sure to provide the full list of chains
  // you would like to support in the Widget
  // and keep them in sync, so all functionality
  // like switching chains can work correctly.
  chains: [mainnet, arbitrum, optimism, scroll],
  connectors: [injected()],
  client({ chain }) {
    return createClient({ chain, transport: http() });
  },
});

export const WidgetPage = () => {
  return (
    <WagmiProvider config={wagmiConfig} reconnectOnMount>
      <BandoWidget integrator="wagmi-example" />
    </WagmiProvider>
  );
};
```

### Sync Chains with Wagmi

To ensure all functionality, such as chain switching, works correctly, it’s important to keep the Bando widget's chain configuration synchronized with your app’s setup. This will ensure that the widget can handle all chains properly.

The way tho achieve this is by using the following hook:

**Dynamic Chain Syncing**: You can fetch all supported chains from the Bando API and update the chain settings dynamically. The widget includes hooks that simplify this approach.

Here's an example of how to dynamically sync chains using these provided hooks.

{% tabs %}

{% tab title="WalletProvider.tsx" %}

```jsx
import { useSyncWagmiConfig } from '@lifi/wallet-management';
import { useAvailableChains } from '@bando/widget';
import { injected } from '@wagmi/connectors';
import { useRef, type FC, type PropsWithChildren } from 'react';
import { createClient, http } from 'viem';
import { mainnet } from 'viem/chains';
import type { Config } from 'wagmi';
import { createConfig, WagmiProvider } from 'wagmi';

const connectors = [injected()];

export const WalletProvider: FC<PropsWithChildren> = ({ children }) => {
  const { chains } = useAvailableChains();
  const wagmi = useRef<Config>();

  if (!wagmi.current) {
    wagmi.current = createConfig({
      chains: [mainnet],
      client({ chain }) {
        return createClient({ chain, transport: http() });
      },
      ssr: true,
    });
  }

  useSyncWagmiConfig(wagmi.current, connectors, chains);

  return (
    <WagmiProvider config={wagmi.current} reconnectOnMount={false}>
      {children}
    </WagmiProvider>
  );
};
```

{% endtab %}

{% tab title="WidgetPage.tsx" %}

```jsx
import { BandoWidget } from "@bando/widget";
import { WalletProvider } from "../providers/WalletProvider";

export const WidgetPage = () => {
  return (
    <WalletProvider>
      <BandoWidget integrator="wagmi-example" />
    </WalletProvider>
  );
};
```

{% endtab %}

{% endtabs %}

## SVM Wallet Connection

To manage wallet connections to SVM (Solana Virtual Machine) chains, the Bando widget integrates with the [Solana Wallet Standard library](https://github.com/anza-xyz/wallet-standard). This ensures smooth interaction between your dApp and Solana-based wallets.

If you are already using the Solana Wallet Standard library in your dApp, and the widget detects that it is wrapped in the [ConnectionProvider and WalletProvider](https://solana.com/developers/cookbook/wallets/connect-wallet-react), it will automatically reuse your existing wallet management setup without requiring any additional configuration.

The example below demonstrates how to set up basic wallet management for SVM using these providers.

{% tabs %}

{% tab title="SolanaWalletProvider.tsx" %}

```jsx
import type { Adapter } from "@solana/wallet-adapter-base";
import { WalletAdapterNetwork } from "@solana/wallet-adapter-base";
import {
  ConnectionProvider,
  WalletProvider,
} from "@solana/wallet-adapter-react";
import { clusterApiUrl } from "@solana/web3.js";
import type { FC, PropsWithChildren } from "react";

const endpoint = clusterApiUrl(WalletAdapterNetwork.Mainnet);
/**
 * Wallets that implement either of these standards will be available automatically.
 *
 *   - Solana Mobile Stack Mobile Wallet Adapter Protocol
 *     (https://github.com/solana-mobile/mobile-wallet-adapter)
 *   - Solana Wallet Standard
 *     (https://github.com/solana-labs/wallet-standard)
 *
 * If you wish to support a wallet that supports neither of those standards,
 * instantiate its legacy wallet adapter here. Common legacy adapters can be found
 * in the npm package `@solana/wallet-adapter-wallets`.
 */
const wallets: Adapter[] = [];

export const SolanaWalletProvider: FC<PropsWithChildren> = ({ children }) => {
  return (
    <ConnectionProvider endpoint={endpoint}>
      <WalletProvider wallets={wallets} autoConnect>
        {children}
      </WalletProvider>
    </ConnectionProvider>
  );
};
```

{% endtab %}

{% tab title="WidgetPage.tsx" %}

```jsx
import { BandoWidget } from "@bando/widget";
import { SolanaWalletProvider } from "../providers/SolanaWalletProvider";

export const WidgetPage = () => {
  return (
    <SolanaWalletProvider>
      <BandoWidget integrator="solana-example" />
    </SolanaWalletProvider>
  );
};
```

{% endtab %}

{% endtabs %}

## Configuration

To configure external wallet management in Bando, you can use additional settings for WalletConnect and Coinbase Wallet. These configurations allow for smoother integration with these wallet providers, making it easy to manage user interactions with external wallets.

Here's the interface setup:

```typescript
interface WidgetWalletConfig {
  onConnect(): void;
  walletConnect?: WalletConnectParameters;
  coinbase?: CoinbaseWalletParameters;
}

interface WidgetConfig {
  // Other configuration properties...
  walletConfig?: WidgetWalletConfig;
}
```

### Connect Wallet Button

For internal wallet management, when users click the **Connect wallet** button, it opens the internal wallet menu. In cases where external wallet management is used, the `onConnect` option is provided to allow developers to specify a callback function. This function is executed when the **Connect wallet** button is clicked.

This approach gives developers flexibility in handling both internal and external wallet connections in a seamless manner within the Bando widget.

<!-- TODO: print example -->

Here’s a similar configuration adapted to Bando’s widget that uses the `useConnectModal` hook provided by RainbowKit to open the wallet modal when the **Connect wallet** button is clicked:

{% tabs %}

{% tab title="WidgetPage.tsx" %}

```tsx
import { BandoWidget } from "@bando/widget";
import { useConnectModal } from "@rainbow-me/rainbowkit";
import { WalletProvider } from "../providers/WalletProvider";

export const WidgetPage = () => {
  const { openConnectModal } = useConnectModal();

  return (
    <WalletProvider>
      <BandoWidget
        integrator="bando-example"
        config={{
          walletConfig: {
            onConnect() {
              openConnectModal?.();
            },
          },
        }}
      />
    </WalletProvider>
  );
};
```

{% endtab %}

{% tab title="WalletProvider.tsx" %}

```tsx
import { formatChain, useAvailableChains } from "@bando/widget";
import { RainbowKitProvider, getDefaultConfig } from "@rainbow-me/rainbowkit";
import { useMemo, type FC, type PropsWithChildren } from "react";
import type { Chain } from "viem";
import { WagmiProvider } from "wagmi";
import { mainnet } from "wagmi/chains";

export const WalletProvider: FC<PropsWithChildren> = ({ children }) => {
  const { chains } = useAvailableChains();

  const wagmiConfig = useMemo(() => {
    const _chains: [Chain, ...Chain[]] = chains?.length
      ? (chains.map(formatChain) as [Chain, ...Chain[]])
      : [mainnet];

    // Wagmi currently doesn't support updating the config after its creation,
    // so in order to keep the dynamic chains list updated, we need to
    // re-create a config every time the chains list changes.
    const wagmiConfig = getDefaultConfig({
      appName: "Bando Widget Example",
      chains: _chains,
      projectId: "Your WalletConnect ProjectId",
      ssr: !chains?.length,
    });

    return wagmiConfig;
  }, [chains]);

  return (
    <WagmiProvider
      config={wagmiConfig}
      reconnectOnMount={Boolean(chains?.length)}
    >
      <RainbowKitProvider>{children}</RainbowKitProvider>
    </WagmiProvider>
  );
};
```

{% endtab %}

{% endtabs %}

In this example, the `useConnectModal` hook opens the RainbowKit wallet connection modal when the **Connect wallet** button is clicked, allowing for seamless integration within the widget.

### WallecConnect Configuration

To configure WalletConnect and Coinbase Wallet with Bando's widget, you can specify additional parameters such as `projectId` for WalletConnect or `appName` for Coinbase Wallet. These settings allow the widget to handle wallet connections seamlessly through these providers.

Here's an example of how to configure the widget:

```typescript
import { BandoWidget, WidgetConfig } from "@bando/widget";

const widgetConfig: WidgetConfig = {
  walletConfig: {
    walletConnect: {
      projectId: "Your Wallet Connect Project Id",
    },
    coinbase: {
      appName: "Your App Name",
    },
  },
};

export const WidgetPage = () => {
  return (
    <BandoWidget integrator="Your dApp/company name" config={widgetConfig} />
  );
};
```

This example sets up the widget to connect to WalletConnect by using a specific `projectId` and configures the `appName` for Coinbase Wallet, allowing users to interact with either wallet provider through the Bando widget.
