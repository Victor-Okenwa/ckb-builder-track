# Builder Track Weekly Report — Week 19

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 7th May, 2026

## Retract Members, restructure app sidebar and add Revoke membership endpoints.

Users can become members of a community but what if they do not want to be part of that community again?
This is where the retract membership endpoint comes in. Users can now retract they membership to a community by using a simpe `Retract membership` form.

### Below are snippets of used


```typescript
// /retract-membership endpoint

import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";

export async function DELETE(req: Request) {
    try {
        const { searchParams } = new URL(req.url);
        const communityId = (searchParams.get("community_id") ?? "").trim();
        const userAddress = (searchParams.get("user_address") ?? "").trim();

        if (!communityId || !userAddress) {
            return NextResponse.json({ error: "Missing required parameters: community_id, user_address" }, { status: 400 });
        }

        // Check if the membership exists
        const { data: membership, error: membershipError } = await supabaseAdmin
            .from("members")
            .select("id")
            .eq("community_id", communityId)
            .eq("user_address", userAddress)
            .maybeSingle();

        if (membershipError) {
            return NextResponse.json({ error: membershipError.message }, { status: 500 });
        }

        if (!membership) {
            return NextResponse.json({ error: "You are not a member of this community" }, { status: 404 });
        }

        // Delete the membership
        const { error: deleteError } = await supabaseAdmin
            .from("members")
            .delete()
            .eq("community_id", communityId)
            .eq("user_address", userAddress);

        if (deleteError) {
            console.error("retract membership:", deleteError);
            return NextResponse.json({ error: "Failed to retract membership" }, { status: 500 });
        }

        return NextResponse.json({ message: "Membership retracted successfully" });

    } catch (error) {
        console.error("retract membership error:", error);
        return NextResponse.json({ error: "Internal server error" }, { status: 500 });
    }
}
```
---
```tsx
// Retract membership button on community-card

export function CommunityCardRetractMembershipButton({
    isMember,
    communityId,
    communityName,
    userAddress,
    className,
    ...props
}: {
    isMember: boolean,
    communityId: string,
    communityName: string,
    userAddress: string,
    className?: ClassValue
} & HTMLAttributes<HTMLButtonElement>) {
    const [isOpen, setIsOpen] = useState(false)
    const [isLoading, setIsLoading] = useState(false)
    const [buttonLabel, setButtonLabel] = useState("Retract Membership")

    const handleRetract = async () => {
        setIsLoading(true)
        setButtonLabel("Retracting...")

        try {
            const params = new URLSearchParams({
                community_id: communityId,
                user_address: userAddress,
            })

            const res = await fetch(
                `/api/community/retract-membership?${params.toString()}`,
                { method: "DELETE" }
            )
            const data = await res.json()

            if (res.ok) {
                toast.success(`Membership retracted from ${communityName}`)
                setButtonLabel("Retracted")
                setIsOpen(false)
                location.reload();
            } else {
                toast.error(data?.error || "Failed to retract membership")
                setButtonLabel("Try Again")
            }
        } catch (err: unknown) {
            console.error(err)
            toast.error("Failed to retract membership")
            setButtonLabel("Try Again")
        } finally {
            setIsLoading(false)
        }
    }

    if (!isMember) {
        return null
    }

    return (
        <AlertDialog open={isOpen} onOpenChange={setIsOpen}>
            <AlertDialogTrigger asChild>
                <Button variant="destructive" size="sm" className={cn(className)} {...props}>
                    Retract Membership
                </Button>
            </AlertDialogTrigger>
            <AlertDialogContent>
                <AlertDialogHeader>
                    <AlertDialogTitle>
                        Retract membership from {communityName}
                    </AlertDialogTitle>
                    <AlertDialogDescription>
                        Are you sure you want to leave <b>{communityName}</b>? You will lose access to its members-only content and privileges.
                    </AlertDialogDescription>
                </AlertDialogHeader>
                <div className="flex justify-end gap-2 mt-4">
                    <AlertDialogCancel>Cancel</AlertDialogCancel>
                    <Button
                        variant="destructive"
                        onClick={handleRetract}
                        disabled={isLoading}
                    >
                        {isLoading && <Spinner />}
                        {buttonLabel}
                    </Button>
                </div>
            </AlertDialogContent>
        </AlertDialog>
    )
}
```

### Restructure Mint Gate App

I noticed that the mint gate interface is not user friendly especialy when it comes to navigation. 

I solve this issue by moving the sidebar to be present on the home page so far as the user has connected wallet. 

This is how I implemented this:

```tsx
// (public)/layout.tsx

"use client";

import { Navigation } from "@/components/navigation";
import { useApp } from "@/components/providers/app-provider";
import {
    SidebarProvider,
    Sidebar,
    SidebarContent,
    SidebarGroup,
    SidebarGroupLabel,
    SidebarGroupContent,
    SidebarMenu,
    SidebarMenuItem,
    SidebarMenuButton,
    useSidebar,
} from "@/components/ui/sidebar";
import { cn } from "@/lib/utils";
import Link from "next/link";
import { WalletConnect } from "@/components/ConnectWallet";
import { WalletConnectInfoAddress, WalletConnectInfoBalance, WalletConnectInfoContainer, WalletConnectInfoImage } from "@/components/ConnectWallet";

const sidebarItems = [
    { title: "Home", url: "/" },
    { title: "My Communities", url: "/my-communities" },
    { title: "My Memberships", url: "/my-memberships" },
    { title: "Discover Communities", url: "/communities" },
    { title: "Create Community", url: "/create-community" },
];

function AppSidebar() {
    const { open } = useSidebar();

    return (
        <Sidebar className="border-r border-border overflow-hidden" collapsible="icon">
            <div className="px-4 py-5 border-b border-border">
                <Link href="/" className={cn("text-sm font-semibold tracking-widest uppercase", {
                    "truncate-text text-[9px]": !open,
                })}>Mint Gate</Link>
            </div>
            <SidebarContent>
                <SidebarGroup>
                    <SidebarGroupLabel className="text-xs text-muted-foreground">Navigation</SidebarGroupLabel>
                    <SidebarGroupContent>
                        <SidebarMenu>
                            {sidebarItems.map((item) => (
                                <SidebarMenuItem key={item.title}>
                                    <SidebarMenuButton asChild>
                                        <Link
                                            href={item.url}
                                            className="text-sm text-muted-foreground hover:text-foreground hover:bg-secondary/50 transition-colors"
                                        >
                                            <span>{item.title}</span>
                                        </Link>
                                    </SidebarMenuButton>
                                </SidebarMenuItem>
                            ))}
                        </SidebarMenu>
                    </SidebarGroupContent>
                </SidebarGroup>

                <SidebarGroup className={cn("mt-auto border-t border-border pt-4", {
                    "hidden": !open,
                })}>
                    <SidebarGroupLabel className="text-xs text-muted-foreground">Wallet</SidebarGroupLabel>
                    <SidebarGroupContent>
                        <WalletConnect>
                            <WalletConnectInfoContainer className="flex gap-1 items-center bg-muted py-2 px-4">
                                <WalletConnectInfoImage />

                                <div className="flex flex-col">
                                    <WalletConnectInfoBalance decimalPlaces={2} className="text-sm" />
                                    <WalletConnectInfoAddress frontChars={5} endChars={5} />
                                </div>
                            </WalletConnectInfoContainer>
                        </WalletConnect>
                    </SidebarGroupContent>
                </SidebarGroup>
            </SidebarContent>
        </Sidebar>
    );
}

export default function PublicLayout({ children }: { children: React.ReactNode }) {
    const { isConnected } = useApp();

    return (
        <div className="min-h-screen">
            <SidebarProvider>
                {isConnected && (
                    <AppSidebar />
                )}

                <section className="w-full">
                    <Navigation isConnected={isConnected} />

                    {children}
                </section>
            </SidebarProvider>
        </div>
    );
}
```

### Revoke Membership endpoint.

I decided to give a community creators more power by having the right to revoke memberships. This is specially important if a creator found that a user has violated community guidelines.

__Here is the implementation__

```typescript
// revoke-membership

import { supabaseAdmin } from "@/lib/superbase/server";
import { NextResponse } from "next/server";
import { cccClient } from "@/ccc-client";
import { hexToUtf8 } from "@/lib/ckb/hash";

export async function DELETE(req: Request) {
    try {
        const { searchParams } = new URL(req.url);
        const communityId = (searchParams.get("community_id") ?? "").trim();
        const userAddress = (searchParams.get("user_address") ?? "").trim();
        const requesterAddress = (searchParams.get("requester_address") ?? "").trim();

        if (!communityId || !userAddress || !requesterAddress) {
            return NextResponse.json({ error: "Missing required parameters: community_id, user_address, requester_address" }, { status: 400 });
        }

        // Check if the requester is either the member themselves or the community creator
        let isAuthorized = false;

        // Check if requester is the member themselves (self-revoke)
        if (requesterAddress === userAddress) {
            isAuthorized = true;
        } else {
            // Check if requester is the community creator
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

            if (dataToObject.creatorAddress === requesterAddress) {
                isAuthorized = true;
            }
        }

        if (!isAuthorized) {
            return NextResponse.json({ error: "You are not authorized to revoke this membership" }, { status: 403 });
        }

        // Check if the membership exists
        const { data: membership, error: membershipError } = await supabaseAdmin
            .from("members")
            .select("id")
            .eq("community_id", communityId)
            .eq("user_address", userAddress)
            .maybeSingle();

        if (membershipError) {
            return NextResponse.json({ error: membershipError.message }, { status: 500 });
        }

        if (!membership) {
            return NextResponse.json({ error: "Membership not found" }, { status: 404 });
        }

        // Delete the membership
        const { error: deleteError } = await supabaseAdmin
            .from("members")
            .delete()
            .eq("community_id", communityId)
            .eq("user_address", userAddress);

        if (deleteError) {
            console.error("revoke membership:", deleteError);
            return NextResponse.json({ error: "Failed to revoke membership" }, { status: 500 });
        }

        return NextResponse.json({ message: "Membership revoked successfully" });

    } catch (error) {
        console.error("revoke membership error:", error);
        return NextResponse.json({ error: "Internal server error" }, { status: 500 });
    }
}

```