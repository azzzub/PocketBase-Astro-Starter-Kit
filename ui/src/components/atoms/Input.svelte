<script>
  export let value;
  export let id;
  export let error = null;
  export let label = null;
</script>

{#if label}
  <label for={id}>{label}</label>
{/if}
<input {id} bind:value {...$$restProps} />
{#if error?.code === 400}
  {#if error?.data?.[id]}
    <label class="error-msg" for={id}>{error?.data?.[id]?.message}</label>
  {/if}
{/if}

<style>
  .error-msg {
    color: red;
    font-size: smaller;
  }
</style>
