<script>
  import PocketBase from "pocketbase";

  export let data;

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  let loading = false;
  let isSuccess = false;
  let errorResponse = null;

  async function requestVerificationHandler() {
    loading = true;
    errorResponse = null;
    try {
      const requestVerification = await pb
        .collection("users")
        .requestVerification(data.email);
      if (requestVerification) {
        isSuccess = true;
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }
</script>

<strong>{data.verified ? "Verified" : "Not Verified"}</strong>
{#if !data.verified}
  <button on:click={requestVerificationHandler} disabled={loading || isSuccess}
    >Verify My Email</button
  >
  {#if errorResponse}
    <span style="color: red;">{errorResponse}</span>
  {/if}
  {#if isSuccess}
    <span style="color: green;">
      Check your inbox to verify your account!
    </span>
  {:else}
    <span>
      You will receive the verification link on your registered email!
    </span>
  {/if}
{/if}
