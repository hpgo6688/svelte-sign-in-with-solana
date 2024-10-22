

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
    <button on:click={sendTransaction}>Send <Loading {loading}/></button>
</div>


<h3>History</h3>


<ul>
    <li><a href="https://solscan.io/tx/5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei?cluster=devnet">devnet坏了 5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei</a></li>
    <li><a href="https://solscan.io/tx/3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx?cluster=testnet">testnet 3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx</a></li>
    <li><a href="https://solscan.io/tx/5WkwNvp5c8andzipGV1zXjyTsAcz6LqMQJ3kbjc16QNchj1LhRSFpe3L3w5noDyXWCeVFq3PGNoEfqz2koqG9Pk6?cluster=testnet">testnet 5WkwNvp5c8andzipGV1zXjyTsAcz6LqMQJ3kbjc16QNchj1LhRSFpe3L3w5noDyXWCeVFq3PGNoEfqz2koqG9Pk6</a></li>
    <li><a href="https://solscan.io/tx/3mLxPYhLbXqaCfYhQoi3VSjt7Rr2SrpyTZGzGoFjPkY2m4gXUqQcXj3XWN4tVus5guJM7sBgsY7dTJGkWnBuzeMc?cluster=testnet">testnet 3mLxPYhLbXqaCfYhQoi3VSjt7Rr2SrpyTZGzGoFjPkY2m4gXUqQcXj3XWN4tVus5guJM7sBgsY7dTJGkWnBuzeMc</a></li>
</ul>

<!-- 5dP24SzRk12xJ7uftJpA7AA8xskZt8Q1zgyhtvYyZcqSzhaTwf4ey26ps3f2v7bhWuXKm7WkMKXbq9wDCZZLtMei -->

<!-- https://solscan.io/tx/3dGDxnVZesjG5ZXxgzNfEk584bYHVn6VQN5oNtLn224BoW2EMYwsHrS1jx7NbW8vdpkGrH5FQ2aYGeikM23V3umx?cluster=testnet -->
<script>
    import { Transaction, SystemProgram, clusterApiUrl, Connection, PublicKey, LAMPORTS_PER_SOL } from '@solana/web3.js';
    import Loading from './loading.svelte';
    const cluster = "testnet";
    const endpoint = clusterApiUrl(cluster);
    const connection = new Connection(endpoint, 'confirmed');


    export let wallet;
    export let connected;
    export let recipientAddress = 'FRvWiiyKoDFD4EF4k92AQkio61kuKZHhkgqzaMeSj9Ab';
    export let lamportsAmount = 0.1;
    
    let loading = false

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
            loading = true
            const signature = await wallet.sendTransaction(transaction, connection);
            console.log('Transaction sent:', signature);
            console.log(`https://solscan.io/tx/${signature}?cluster=${cluster}`)
            loading = false
        } catch (error) {
            loading = false
            console.error('Transaction failed:', error);
        }
    };
</script>

