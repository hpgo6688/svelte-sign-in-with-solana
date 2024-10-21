
<h1>Direct Connection Wallet </h1>
<div>
    {#if connected}
        <button on:click={disconnectWallet}>Disconnect Wallet</button>
    {:else}
        {#each wallets as wallet}
            <button on:click={() => connectWallet(wallet)}>Connect {wallet.name}</button>
        {/each}
    {/if}
</div>

<h1>Logs</h1>
<pre>
<code>
    {#each $logs as log}
        {log}
    {/each}
</code>
</pre>
<slot></slot>



 <script lang="ts">
    import { browser } from '$app/environment';
    import { onMount } from "svelte";
    import bs58 from "bs58";
    import { clusterApiUrl } from '@solana/web3.js';
    import { PhantomWalletAdapter, LedgerWalletAdapter, SolflareWalletAdapter } from '@solana/wallet-adapter-wallets';
    import { writable } from 'svelte/store';

    // 创建一个可写的日志存储
    const logs = writable([]);

    let wallet;
    let connected = false;
    let isVerifying = false;
    let verified = false;

    const wallets = [
        new PhantomWalletAdapter(),
        // new SolflareWalletAdapter(),
        // new LedgerWalletAdapter(),
    ];

    const network = clusterApiUrl("mainnet-beta");

    async function connectWallet(selectedWallet) {
        try {
            wallet = selectedWallet;
            await wallet.connect();
            connected = true;
            addLog(`\n\n\nWallet connected: ${wallet.publicKey.toString()}`);
            signMessage();
        } catch (error) {
            addLog(`\n\n\nError connecting wallet: ${error.message}`);
        }
    }

    async function disconnectWallet() {
        if (wallet) {
            await wallet.disconnect();
            connected = false;
            addLog('\n\n\nWallet disconnected');
        }
    }

    const addLog = (message) => {
        logs.update(currentLogs => [...currentLogs, message]);
    };

    const getMessage = async () => {
        const response = await fetch("/api/solana/message");
        const { data: message } = await response.json();
        addLog(`\n\n\nReceived message to sign: "${message}"`);
        return message;
    };

    const signMessage = async () => {
        try {
            isVerifying = true;

        const message = await getMessage();
        const encodedMessage = new TextEncoder().encode(message);

        if (!wallet.signMessage) {
            addLog('\n\n\nWallet not supported');
            isVerifying = false;
            return;
        }

        const signedMessage = await wallet.signMessage(encodedMessage);
        const base58Signature = bs58.encode(signedMessage);

        addLog(`\n\n\nPublic Key: ${wallet.publicKey.toBase58()}`);
        addLog(`\n\n\nSigned message: ${base58Signature}`);

        if (!signedMessage) {
            addLog('\n\n\nFailed to sign message');
            isVerifying = false;
            return;
        }

        addLog('\n\n\nVerifying signature...');

        const response = await fetch(`/api/solana/message/verify`, {
            method: "POST",
            body: JSON.stringify({
                message: message,
                signature: base58Signature,
                publicKey: wallet.publicKey.toBase58(),
            }),
        });

        if (response.status !== 200) {
            addLog('\n\n\nSignature verification failed');
            disconnectWallet();
            isVerifying = false;
            return;
        }

        addLog('\n\n\nSignature verification success ✅');
        isVerifying = false;
        verified = true;
        } catch (error) {
            addLog('\n\n\n'+ error.message);
        }
    };
    onMount(() => {
        if(browser) {
            // window.Buffer = Buffer;
            addLog('\n\n\nApp is ready');
        }
    });
</script>

