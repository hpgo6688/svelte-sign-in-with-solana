<!-- src/YourComponent.svelte -->
<script>
    import { onMount } from 'svelte';
    import { walletStore } from '@svelte-on-solana/wallet-adapter-core';
    import { Transaction, SystemProgram, clusterApiUrl, Connection, PublicKey } from '@solana/web3.js';


    let recipientAddress = 'FRvWiiyKoDFD4EF4k92AQkio61kuKZHhkgqzaMeSj9Ab'; // 目标地址
    let lamportsAmount = 1000000000; // 默认发送的 lamports 数量

    const endpoint = clusterApiUrl('devnet');
    const connection = new Connection(endpoint, 'confirmed');

    const sendTransaction = async () => {
        if (!walletStore.sendTransaction) {
            console.error('Wallet not connected');
            return;
        }

        // 将字符串转换为 PublicKey
        let toPubkey;
        try {
            toPubkey = new PublicKey(recipientAddress);
        } catch (error) {
            console.error('Invalid recipient address:', error);
            return;
        }

        const transaction = new Transaction().add(
            SystemProgram.transfer({
                fromPubkey: walletStore.publicKey,
                toPubkey, // 使用输入的目标地址
                lamports: lamportsAmount, // 使用输入的 lamports 数量
            })
        );

        try {
            const signature = await walletStore.sendTransaction(transaction, connection);
            console.log('Transaction sent:', signature);
        } catch (error) {
            console.error('Transaction failed:', error);
        }
    };
</script>

<div>
    <h2>发送交易</h2>
    <label>
        接收地址:
        <input type="text" bind:value={recipientAddress} placeholder="输入目标地址" />
    </label>
    <label>
        发送数量 (lamports):
        <input type="number" bind:value={lamportsAmount} />
    </label>
    <button on:click={sendTransaction}>发送交易</button>
</div>
