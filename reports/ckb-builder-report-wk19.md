# Builder Track Weekly Report — Week 18

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 1st May, 2026

## Verification and Deeletion of Communties on Mint Gate.

During this week I tasked my self with the goal of achieving onchain verification as a single source of truth to derermin the owner of a community especially before deletion.

Although I stored data on my supabased database but it can be mutated and changed but onchain it is immuntable.

The data I stored onchain when a user creates a community is the:
1. Community Id
2. creator address

```ts
{
    id: "68f118dc-5a21-4eda-a058-11ecaf8e9b34",
    creatorAddress: "ckt1qrfrwcdnvssswdwpn3s9v8fp87emat306ctjwsm3nmlkjg8qyza2cqgqqxt7d37lhpzaju02xcajrpgzh0ue8qawqyw9rq00"
}
```

I creator an endpoint to verify ownership by fetching the `Transaction Hash` stored on the database. The endpoint goes to `/verify/community-ownership?{params}`. It takes two parameters:
1. Community Id.
2. User Address.

It first of all fetches the database for the `txHas` then uses CKB `getLiveCell` function to get the output data from then convets it from hext to UTF8 readble format.

__Verify ownershi emdpoint__
```tsx
import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";
import { cccClient } from "@/ccc-client";
import { hexToUtf8 } from "@/lib/ckb/hash";

export async function GET(req: Request) {
    const { searchParams } = new URL(req.url);
    const communityId = (searchParams.get("community_id") ?? "").trim();
    const userAddress = (searchParams.get("user_address") ?? "").trim();

    if (!communityId || !userAddress) {
        return NextResponse.json({ error: "Invalid community id or user address" }, { status: 400 });
    }

    // Get all memberships for the user, paged
    const { data: community, error: fetchError } = await supabaseAdmin
        .from("communities")
        .select("tx_hash")
        .eq("id", communityId)
        .maybeSingle();

    const txHash = community?.tx_hash;
    if (!txHash) return NextResponse.json({ error: "Community not found", verified: false }, { status: 404 });
    if (fetchError) return NextResponse.json({ error: fetchError.message, verified: false }, { status: 500 });

    const cell = await cccClient.getCellLive({ txHash, index: "0x0" }, true)
    const data = hexToUtf8(cell!.outputData)

    // Convert to Javaqscript Object
    const dataToObject = JSON.parse(data)
    if (dataToObject.creatorAddress === userAddress) {
        return NextResponse.json({ txHash, verified: true });
    } else {
        return NextResponse.json({ txHash, verified: false });
    }
}
```

__Convert data from Hex to UTF8 Fucntion__

```ts
export function hexToUtf8(hexString: string): string {
    const decoder = new TextDecoder("utf-8");
    const uint8Array = new Uint8Array(
        hexString.match(/[\da-f]{2}/gi)!.map((h) => parseInt(h, 16))
    );
    return decoder.decode(uint8Array);
}
```

With the above I figured out how to verify user owner before a sensitive action like deletion is done. 

I decided to create an endpoint for deletion which also verifies ownership before a deletion. 

The endpoint works like this:

1. Receive parameters `communityId` and `userAddress` form client.
2. Gets the community `Transaction hash`.
3. Gets the live cells of the `Transaction Hash`.
4. Converts from _Hex_ to _UTF8_.
5. Verifiy ownership.
6. If user is verified as true owner perform deletion on members table by removing and deleting all members details.
7. Safely delete the community and all its records from the database.

__Delete Community endpoint__ 
```ts
import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";
import { cccClient } from "@/ccc-client";
import { hexToUtf8 } from "@/lib/ckb/hash";

export async function DELETE(req: Request) {
    try {
        const { searchParams } = new URL(req.url);
        const communityId = (searchParams.get("community_id") ?? "").trim();
        const userAddress = (searchParams.get("user_address") ?? "").trim();

        if (!communityId || !userAddress) {
            return NextResponse.json({ error: "Invalid community id or user address" }, { status: 400 });
        }

        // Verify ownership by checking blockchain
        const { data: community, error: fetchError } = await supabaseAdmin
            .from("communities")
            .select("tx_hash")
            .eq("id", communityId)
            .maybeSingle();

        if (fetchError) {
            return NextResponse.json({ error: fetchError.message }, { status: 500 });
        }

        const txHash = community?.tx_hash;
        if (!txHash) {
            return NextResponse.json({ error: "Community not found" }, { status: 404 });
        }

        const cell = await cccClient.getCellLive({ txHash, index: "0x0" }, true);
        if (!cell) {
            return NextResponse.json({ error: "Failed to verify ownership" }, { status: 500 });
        }

        const data = hexToUtf8(cell.outputData);
        const dataToObject = JSON.parse(data);

        if (dataToObject.creatorAddress !== userAddress) {
            return NextResponse.json({ error: "You are not the owner of this community" }, { status: 403 });
        }

        // Delete all members first
        const { error: deleteMembersError } = await supabaseAdmin
            .from("members")
            .delete()
            .eq("community_id", communityId);

        if (deleteMembersError) {
            console.error("delete members:", deleteMembersError);
            return NextResponse.json({ error: "Failed to delete community members" }, { status: 500 });
        }

        // Delete the community
        const { error: deleteCommunityError } = await supabaseAdmin
            .from("communities")
            .delete()
            .eq("id", communityId);

        if (deleteCommunityError) {
            console.error("delete community:", deleteCommunityError);
            return NextResponse.json({ error: "Failed to delete community" }, { status: 500 });
        }

        return NextResponse.json({ message: "Community deleted successfully" });

    } catch (error) {
        console.error("delete community error:", error);
        return NextResponse.json({ error: "Internal server error" }, { status: 500 });
    }
}
```

__Communty card Delete button__

```tsx

```

