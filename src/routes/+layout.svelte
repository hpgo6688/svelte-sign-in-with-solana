<h1>Different way to Sign-in with solana</h1>
<nav>
    <ul>
        <li><button on:click={() => goto('/dev')}>Go to Connection Solana Wallet by Direct</button></li>
        <li><button on:click={() => goto('/org')}>Go to Connection Solana Wallet by wallet-adapter-ui</button></li>
    </ul>
</nav>

<script>
    import { goto } from '$app/navigation';
</script>



<slot></slot>