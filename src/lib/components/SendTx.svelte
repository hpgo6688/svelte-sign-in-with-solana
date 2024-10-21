

 <div>
    <h2>Send Transaction</h2>
    <label>
        RecipientAddress:
        <input type="text" bind:value={recipientAddress} placeholder="please input" />
    </label>
    <label>
        send amount (lamports):
        <input type="number" bind:value={lamportsAmount} />
    </label>
    <button on:click={sendTransaction}>Send</button>
</div>


<h3>History</h3>


<ul>
    <li><a href="https://solscan.io/tx/5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei?cluster=devnet">devnet坏了 5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei</a></li>
    <li><a href="https://solscan.io/tx/3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx?cluster=testnet">testnet 3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx</a></li>
</ul>

<!-- 5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei -->

<!-- https://solscan.io/tx/3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx?cluster=testnet -->
<script>
    import { Transaction, SystemProgram, clusterApiUrl, Connection, PublicKey, LAMPORTS_PER_SOL } from '@solana/web3.js';
    const endpoint = clusterApiUrl("testnet");
    const connection = new Connection(endpoint, 'confirmed');


    export let wallet;
    export let connected;
    export let recipientAddress = 'FRvWiiyKoDFD4EF4k92AQkio61kuKZHhkgqzaMeSj9Ab';
    export let lamportsAmount = 0.1;

    const sendTransaction = async () => {
        if (!connected || !wallet) {
            console.error('Wallet not connected');
            return;
        }

        let toPubkey;
        try {
            toPubkey = new PublicKey(recipientAddress);
        } catch (error) {
            console.error('Invalid recipient address:', error);
            return;
        }

        console.log('lamportsAmount',lamportsAmount, typeof lamportsAmount)
        const transaction = new Transaction().add(
            SystemProgram.transfer({
                fromPubkey: wallet.publicKey,
                toPubkey: toPubkey,
                lamports: lamportsAmount * LAMPORTS_PER_SOL,
            })
        );

        try {
            const signature = await wallet.sendTransaction(transaction, connection);
            console.log('Transaction sent:', signature);
        } catch (error) {
            console.error('Transaction failed:', error);
        }
    };
</script>

