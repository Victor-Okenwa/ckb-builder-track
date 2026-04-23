# Builder Track Weekly Report — Week 17

__Name:__ Victor Okenwa.
__Week Ending:__ Friday April 24th, 2026

## Refactor Wallet Component, Fetch memberships and users' owned communities.

In this week, I refactored the wallet component. I made it developer friendly and easy to use.

### 3 Main Highlights of what I did

__1__

I made it a `compound component` so developers can choose what and how to use.

```tsx
<WalletConnect>
    <WalletConnectInfoContainer className="p-0 bg-transparent">
        <WalletConnectInfoImage />
        <div className="flex flex-col gap-1">
            <WalletConnectInfoBalance decimalPlaces={2} />
            <WalletConnectInfoAddress frontChars={15} endChars={4} />
        <div>
    </WalletConnectInfoContainer>
</WalletConnect>
```