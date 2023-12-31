<script>
  import Input from "../atoms/Input.svelte";
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  export let data;
  const currentEmail = pb.authStore.model?.email;

  let loading = false;
  let errorResponse = null;
  let isSuccess = false;

  async function saveNewEmailHandler() {
    loading = true;
    errorResponse = null;
    try {
      const newEmail = await pb
        .collection("users")
        .requestEmailChange(data.email);
      if (newEmail) {
        isSuccess = true;
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }
</script>

<Input
  id="newEmail"
  bind:value={data.email}
  label="Email"
  disabled={loading || isSuccess}
  error={errorResponse}
/>
{#if isSuccess}
  <label for="email" style="color: green;"
    >Check your new email inbox, we have sent you the confirmation link!</label
  >
{/if}
{#if currentEmail !== data.email && !isSuccess}
  <button on:click={saveNewEmailHandler} disabled={loading}
    >Save New Email</button
  >
{/if}
