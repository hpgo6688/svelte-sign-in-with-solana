<script lang="ts">
    import { onMount } from 'svelte';
    import { Connection, PublicKey, clusterApiUrl } from '@solana/web3.js';
    import Loading from './loading.svelte';

    let publicKeyString = 'BM863J1ewz5ST5arUmL3LM7oP68z3LGNNsp67L5p1gZj'; // Public key input
    let balance: number | undefined = undefined; // Store balance
    let errorMessage = ''; // Store error message
    let loading = false

    const getBalance = async () => {
        try {
            // Create connection to Testnet
            const connection = new Connection(clusterApiUrl('testnet'), 'confirmed');
            const publicKey = new PublicKey(publicKeyString);
            loading = true
            // Get balance
            balance = await connection.getBalance(publicKey);
            balance /= 1e9; // Convert to SOL
            errorMessage = ''; // Clear error message
            loading = false
        } catch (error) {
            loading = false
            balance = undefined; // Clear balance
            errorMessage = 'Unable to fetch balance: ' + (error as unknown as Error)?.message;
        }
    };
</script>

<style>
    /* Basic styles */
    .container {
        padding: 1rem;
    }
    input {
        margin-bottom: 1rem;
    }
</style>

<div class="container">
    <h1>Get Solana Account Balance</h1>
    <input
        type="text"
        placeholder="Enter your public key"
        bind:value={publicKeyString}
    />
    <button on:click={getBalance}>Get Balance <Loading {loading}/></button>

    {#if balance !== undefined}
        <p>Account {publicKeyString} balance: {balance} SOL</p>
    {/if}

    {#if errorMessage}
        <p style="color: red;">{errorMessage}</p>
    {/if}
</div>
