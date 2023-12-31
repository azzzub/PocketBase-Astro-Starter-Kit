<script>
  import { onMount } from "svelte";
  import Input from "./atoms/Input.svelte";
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  let email = "";
  let errorResponse = null;
  let loading = false;
  let success = false;

  async function forgetPasswordHandler() {
    loading = true;
    errorResponse = null;
    try {
      const resetResponse = await pb
        .collection("users")
        .requestPasswordReset(email);
      if (resetResponse) {
        success = true;
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }

  onMount(() => {
    if (pb.authStore.isValid) {
      window.location.pathname = "/";
    }
  });
</script>

<form on:submit|preventDefault={forgetPasswordHandler}>
  {#if success}
    <label for="email" style="color: green;"
      >We have send you an email to reset your password, please check your
      inbox!</label
    >
  {:else}
    <label for="email">We will send you an email to reset your password!</label>
  {/if}
  <Input
    id="email"
    type="email"
    placeholder="Your email"
    required
    bind:value={email}
    error={errorResponse}
    disabled={success}
  />
  <button type="submit" disabled={loading || success}>Login</button>
  <a href="/">Back to home</a>
</form>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 50%;
  }
</style>
