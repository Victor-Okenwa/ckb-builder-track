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