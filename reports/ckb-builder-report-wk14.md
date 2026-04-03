# Builder Track Weekly Report — Week 12

__Name:__ Victor Okenwa.
__Week Ending:__ Friday April 3rd, 2026

## Setting up Onchain storage.

I used CKB's feature that allows user to store data on a cell. Because of limitations I had to allow a user to store only the two most necessary data which are `community id` and `creator's address`. 

The reason I chose this two is because:
1. They are consistent accross community creation operations, the UUID of the Community and the Creators address will likely have the same number of characters.
2. They are the most needed because it not only identifies that a community exists, it also identifies the owner of the community.

```typescript
       const communityData = {
                id: communityId,
                creatorAddress
            };

            const dataHex = utf8ToHex(JSON.stringify(communityData));

            const capacityHex = ckbToShannonsHex(301);

            const unsignedTx = ccc.Transaction.from({
                outputs: [
                    {
                        capacity: capacityHex,
                        lock: addressObj.script,
                        type: undefined,
                    },
                ],
                outputsData: [dataHex],
            });
```

### Getting our transaction hash and persisting it on the database.

```typescript
    await unsignedTx.completeInputsByCapacity(signer);
    await unsignedTx.completeFeeBy(signer, 1000);

    const signedTx = await signer.signTransaction(unsignedTx);
    const txHash = await signer.sendTransaction(signedTx);


    const response = await fetch("/api/community/create", {
        method: "POST",
        body: JSON.stringify({
            id: communityId,
            name: values.name,
            description: values.description,
            creator_address: creatorAddress,
            guidelines: values.guidelines,
            hidden_link: values.hiddenLink,
            mint_price: values.mintPrice,
            tx_hash: txHash,
        }),
    });
```

To store the needed `community id` and `creator address` on chain we have to collect and approximated amount of __301 CKB__ which is approximately enough to store the necessary data.

### Below is the full code snippets for creating a community

```typescript
    const router = useRouter();
    const signer = ccc.useSigner();

    const form = useForm<FormValues>({
        resolver: zodResolver(formSchema),
        defaultValues: {
            name: "",
            description: "",
            guidelines: "",
            mintPrice: "0",
        },
    });

    async function handleSubmit(values: FormValues) {
        setIsSubmitting(true);
        const communityId = generateCommunityId();

        try {
            if (!signer) {
                toast.error("Connect wallet first");
                return;
            }


            const balance = await signer.getBalance();
            const creatorAddress = await signer?.getRecommendedAddress();
            const addressObj = await signer.getRecommendedAddressObj();

            if (!balance || !addressObj) {
                toast.error("Wallet not ready");
                return;
            }

            if (balance < ckbToShannons(301)) {
                toast.error("Insufficient balance (min 301 CKB)");
                return;
            }


            const communityData = {
                id: communityId,
                creatorAddress
            };

            const dataHex = utf8ToHex(JSON.stringify(communityData));

            const capacityHex = ckbToShannonsHex(301);

            const unsignedTx = ccc.Transaction.from({
                outputs: [
                    {
                        capacity: capacityHex,
                        lock: addressObj.script,
                        type: undefined,
                    },
                ],
                outputsData: [dataHex],
            });

            await unsignedTx.completeInputsByCapacity(signer);
            await unsignedTx.completeFeeBy(signer, 1000);

            const signedTx = await signer.signTransaction(unsignedTx);
            const txHash = await signer.sendTransaction(signedTx);

            console.log("Community deployed:", txHash);

            setIsDeploying(true);


            const response = await fetch("/api/community/create", {
                method: "POST",
                body: JSON.stringify({
                    id: communityId,
                    name: values.name,
                    description: values.description,
                    creator_address: creatorAddress,
                    guidelines: values.guidelines,
                    hidden_link: values.hiddenLink,
                    mint_price: values.mintPrice,
                    tx_hash: txHash,
                }),
            });

            if (!response.ok) {
                throw new Error(await response.text());
            }

            toast.success("Community created successfully 🚀");
            router.push(`/dashboard/community/${communityId}`);
        } catch (error: unknown) {
            console.error("handleSubmit error:", error);
            const msg = (error as Error)?.message ?? String(error);
            if (msg.toLowerCase().includes("capacity") || msg.toLowerCase().includes("insufficient")) {
                toast.error("Insufficient CKB balance. You need about 301 CKB plus fees.");
            } else {
                // show the raw message to help debugging
                toast.error(msg);
            }
        } finally {
            setIsDeploying(false);
            setIsSubmitting(false);
        }
    }
```


```typescript
/**
 * Convert CKB to shannons
 * @param ckb - CKB amount
 * @returns shannons
 */
export function ckbToShannons(ckb: number | string | bigint): bigint {
    return BigInt(ckb) * BigInt(SHANNONS_PER_CKB);
}

/**
 * Convert CKB to shannons hex
 * @param ckb - CKB amount
 * @returns shannons hex
 */
export function ckbToShannonsHex(ckb: number | string): string {
    const shannonsPerCkb = BigInt(SHANNONS_PER_CKB);
    const n = BigInt(Math.floor(Number(ckb))) * shannonsPerCkb;
    // return hex string with 0x prefix
    return "0x" + n.toString(16);
}
```

```typescript
export function utf8ToHex(utf8String: string): string {
    const encoder = new TextEncoder();
    const uint8Array = encoder.encode(utf8String);
    return (
        "0x" +
        Array.prototype.map
            .call(uint8Array, (byte: number) => {
                return ("0" + (byte & 0xff).toString(16)).slice(-2);
            })
            .join("")
    );
}
```

