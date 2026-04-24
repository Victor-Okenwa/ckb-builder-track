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

__2__

I fixed a doouble awaited Immediately Invoked functions into one `Promise` do that both executes same time.

```tsx
  useEffect(function () {
    if (!signer) {
      return;
    }
    (async function () {
      const [address, capacity] = await Promise.all([
        signer.getRecommendedAddress(),
        signer.getBalance()
      ]
      );

      setAddress(address)
      setBalance(ccc.fixedPointToString(capacity));
    })();
  }, [signer]);
```

__3__

Made the blanace to have a currency format making numbers more readable by adding commas. Aksoi added props for the control of the number of addres characters the developer wants to see.

```tsx
export function WalletConnectInfoBalance({ className = "",
  decimalPlaces,
  withCurrency = true
}: {
  decimalPlaces?: DecimalPlaces;
  withCurrency?: boolean;
  className?: ClassValue
}) {
  const { wallet, balance } = useWalletConnect();

  if (!wallet) return null;

  const formatter = new Intl.NumberFormat('en-US', {
    maximumFractionDigits: decimalPlaces, // Controls decimal places
    roundingMode: 'trunc',    // Forces it to cut off instead of rounding up
  });

  return (
    <h3 className={cn("font-semibold text-sm", className)}>{decimalPlaces !== undefined ? formatter.format(Number(balance)) : balance}
      {withCurrency &&
        <span>
          {" "}
          CKB
        </span>}
    </h3>
  )
}

export function WalletConnectInfoAddress({ className = "",
  frontChars = 10,
  endChars = 6,
}: {
  className?: ClassValue;
  frontChars?: number,
  endChars?: number,
}) {
  const { wallet, address } = useWalletConnect();

  if (!wallet) return null;

  return (
    <span className={cn("text-xs font-normal", className)}>
      {truncateAddress(address, frontChars, endChars)}
    </span>
  )
}
```
---

> Full preview

```tsx
import { ccc } from "@ckb-ccc/connector-react";
import { ClassValue } from "clsx";
import React, { createContext, useContext, useEffect, useState } from "react";
import { truncateAddress } from "../utils/stringUtils";
import { cn } from "@/lib/utils";

interface WalletConnectContextProps {
  balance: string;
  setBalance: React.Dispatch<React.SetStateAction<string>>;
  address: string;
  setAddress: React.Dispatch<React.SetStateAction<string>>;
  open: () => unknown
  wallet: ccc.Wallet | undefined
}

const initialWalletConnectState: WalletConnectContextProps = {
  balance: "",
  setBalance: () => { },
  address: "",
  setAddress: () => { },
  open: () => { },
  wallet: undefined
};


//  Context for store
const WalletConnectContext = createContext<WalletConnectContextProps>(initialWalletConnectState);

function useWalletConnect() {
  const context = useContext(WalletConnectContext);
  if (!context) throw new Error("Wallet connect components must be used within <WalletConnect />");
  return context;
}

export function WalletConnect({ children }: { children: React.ReactNode }) {
  const { open, wallet } = ccc.useCcc();
  const [balance, setBalance] = useState<string>("");
  const [address, setAddress] = useState<string>("");
  const signer = ccc.useSigner();

  useEffect(function () {
    if (!signer) {
      return;
    }
    (async function () {
      const [address, capacity] = await Promise.all([
        signer.getRecommendedAddress(),
        signer.getBalance()
      ]
      );

      setAddress(address)
      setBalance(ccc.fixedPointToString(capacity));
    })();
  }, [signer]);

  const values = {
    balance,
    setBalance,
    address,
    setAddress,
    open,
    wallet
  }

  return (
    <WalletConnectContext.Provider value={values}>
      {children}
    </WalletConnectContext.Provider>
  )
}

export function WalletConnectButton({
  className = ""
}: {
  className?: ClassValue
}) {
  const { open, wallet } = useWalletConnect();

  if (wallet) return null;

  return (
    <button className={cn("cursor-pointer rounded-full border border-solid border-transparent transition-colors flex items-center justify-center gap-2 bg-black dark:bg-white text-white dark:text-black hover:opacity-90  text-sm sm:text-base font-bold  px-5 py-3", className)}
      onClick={open}
    >
      Connect Wallet
    </button>
  )
}

export function WalletConnectInfoContainer({ children, className = ""
}: {
  children: React.ReactNode;
  className?: ClassValue
}) {
  const { open, wallet } = useWalletConnect();

  if (!wallet) return null;
  return (
    <button className={cn("cursor-pointer rounded-full border border-solid border-transparent transition-colors bg-black dark:bg-white text-white dark:text-black hover:opacity-90 text-sm sm:text-base font-bold px-5 py-3", className)}
      onClick={open}>
      {children}
    </button>
  )
}

export function WalletConnectInfoImage({ className = ""
}: {
  className?: ClassValue
}) {
  const { wallet } = useWalletConnect();

  if (!wallet) return null;

  return (
    <img src={wallet.icon} alt="avatar" className={cn("w-6 h-6", className)} />
  )
}

type DecimalPlaces = 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20;

export function WalletConnectInfoBalance({ className = "",
  decimalPlaces,
  withCurrency = true
}: {
  decimalPlaces?: DecimalPlaces;
  withCurrency?: boolean;
  className?: ClassValue
}) {
  const { wallet, balance } = useWalletConnect();

  if (!wallet) return null;

  const formatter = new Intl.NumberFormat('en-US', {
    maximumFractionDigits: decimalPlaces, // Controls decimal places
    roundingMode: 'trunc',    // Forces it to cut off instead of rounding up
  });

  return (
    <h3 className={cn("font-semibold text-sm", className)}>{decimalPlaces !== undefined ? formatter.format(Number(balance)) : balance}
      {withCurrency &&
        <span>
          {" "}
          CKB
        </span>}
    </h3>
  )
}

export function WalletConnectInfoAddress({ className = "",
  frontChars = 10,
  endChars = 6,
}: {
  className?: ClassValue;
  frontChars?: number,
  endChars?: number,
}) {
  const { wallet, address } = useWalletConnect();

  if (!wallet) return null;

  return (
    <span className={cn("text-xs font-normal", className)}>
      {truncateAddress(address, frontChars, endChars)}
    </span>
  )
}
```

### Fetching User's owned and member communities
Users can now fetch and see the the communities they own or are a member of. 

I created a `my communities` and `my memberships` routes to handle these.

Also I created a backend endpoint to handle each of this

### Some code snippets.

> get users owned communities
```ts
import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";

export async function GET(req: Request) {
    const { searchParams } = new URL(req.url);
    const page = Math.max(1, Number(searchParams.get("page")) || 1);
    const limit = Math.max(1, Math.min(100, Number(searchParams.get("limit")) || 10));
    const userAddress = (searchParams.get("user_address") ?? "").trim();

    const from = (page - 1) * limit;
    const to = from + limit - 1;

    // Get all memberships for the user, paged
    const { data: communities, error } = await supabaseAdmin
        .from("communities")
        .select("*")
        .range(from, to)
        .eq("creator_address", userAddress);

    if (error) return NextResponse.json({ error: error.message }, { status: 500 });
    if (!communities || communities.length === 0) return NextResponse.json({ communities: [] }, { status: 200 });

    const ids = communities.map((c) => String(c.id));

    const membersCountByCommunity = new Map<string, number>();
    const membershipIds = new Set<string>();

    if (ids.length > 0) {
        const [allMembersResult, userMembershipResult] = await Promise.all([
            supabaseAdmin.from("members").select("community_id").in("community_id", ids),
            userAddress
                ? supabaseAdmin
                    .from("members")
                    .select("community_id")
                    .eq("user_address", userAddress)
                    .in("community_id", ids)
                : Promise.resolve({ data: null as { community_id: string }[] | null, error: null }),
        ]);

        if (allMembersResult.error) {
            console.error("getAll members counts:", allMembersResult.error);
            return NextResponse.json({ error: allMembersResult.error.message }, { status: 500 });
        }

        for (const m of allMembersResult.data ?? []) {
            const cid = String(m.community_id);
            membersCountByCommunity.set(cid, (membersCountByCommunity.get(cid) ?? 0) + 1);
        }

        console.log(allMembersResult)

        if (userAddress) {
            if (userMembershipResult.error) {
                console.error("getAll membership:", userMembershipResult.error);
                return NextResponse.json({ error: userMembershipResult.error.message }, { status: 500 });
            }
            for (const m of userMembershipResult.data ?? []) {
                membershipIds.add(String(m.community_id));
            }
        }
    }

    const payload = communities.map((row) => {
        const id = String(row.id);
        return {
            communityID: id,
            name: row.name ?? "",
            description: row.description ?? "",
            mintPrice: Number(row.mint_price ?? 0),
            creatorAddress: row.creator_address ?? "",
            isCreator: true,
            isMember: false,
            membersCount: membersCountByCommunity.get(id) ?? 0,
            txHash: row.tx_hash
        };
    });

    return NextResponse.json({ communities: payload });
}
```
> get users memberships and corresponding communities
```ts
import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";
import { CommunityListItem } from "../get-all/route";

export async function GET(req: Request) {
    const { searchParams } = new URL(req.url);
    const page = Math.max(1, Number(searchParams.get("page")) || 1);
    const limit = Math.max(1, Math.min(100, Number(searchParams.get("limit")) || 10));
    const userAddress = (searchParams.get("user_address") ?? "").trim();

    const from = (page - 1) * limit;
    const to = from + limit - 1;

    // Get all memberships for the user, paged
    const { data: memberships, error: membersError } = await supabaseAdmin
        .from("members")
        .select("*")
        .range(from, to)
        .eq("user_address", userAddress);

    if (membersError) return NextResponse.json({ error: membersError.message }, { status: 500 });
    if (!memberships || memberships.length === 0) return NextResponse.json({ communities: [] }, { status: 200 });

    // Get the community IDs to fetch details for
    const communityIds = memberships.map((m) => m.community_id);

    // If no memberships, return empty list
    if (!communityIds.length) {
        return NextResponse.json({ communities: [] }, { status: 200 });
    }

    // Fetch all communities in one query using `in`
    const { data: communityRows, error: communitiesError } = await supabaseAdmin
        .from("communities")
        .select("*")
        .in("id", communityIds);

    if (communitiesError) return NextResponse.json({ error: communitiesError.message }, { status: 500 });

    // Optionally: Make sure the order matches the order of communityIds (to preserve pagination relevance)
    let communities = [];
    if (communityRows) {
        // Could filter/map if needed to ensure order
        const communityMap = Object.fromEntries(communityRows.map((row) => [row.id, row]));
        communities = communityIds.map((cid) => communityMap[cid]).filter(Boolean);
    }


    const payload: CommunityListItem[] = communities.map((row) => {
        const id = String(row.id);
        return {
            communityID: id,
            name: row.name ?? "",
            description: row.description ?? "",
            mintPrice: Number(row.mint_price ?? 0),
            creatorAddress: row.creator_address ?? "",
            isCreator: row.creator_address == userAddress,
            isMember: true,
        };
    });

    return NextResponse.json({ communities: payload });
}
```
