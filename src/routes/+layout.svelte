<h1>Different way to Sign-in with solana</h1>
<nav>
    <ul>
        <li><button on:click={() => goto('/adapter-direct')}>Go to Connection Solana Wallet by Direct</button></li>
        <li><button on:click={() => goto('/adapter-ui')}>Go to Connection Solana Wallet by wallet-adapter-ui</button></li>
        <!-- <li><button on:click={() => goto('/send-tx')}>Go to Transaction</button></li> -->
    </ul>
</nav>

<script>
    import { goto } from '$app/navigation';
</script>



<slot></slot>