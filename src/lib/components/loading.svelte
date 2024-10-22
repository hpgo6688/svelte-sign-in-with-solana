<script>
    export let loading = false; // 控制加载状态的属性
</script>

<style>
</style>

<span>
    {#if loading}
        loading...
    {/if}
</span>
