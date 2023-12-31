<script>
  import { onMount } from "svelte";
  import Input from "./atoms/Input.svelte";
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  let usernameOrEmail = "";
  let password = "";
  let errorResponse = null;
  let loading = false;

  async function loginHandler() {
    loading = true;
    errorResponse = null;
    try {
      const authData = await pb
        .collection("users")
        .authWithPassword(usernameOrEmail, password);
      if (authData) {
        const pathname = window.location.search.replace("?", "").split("&");
        const redirect =
          pathname
            .filter((v) => v.includes("redirect"))?.[0]
            ?.split("=")?.[1] || "/";
        window.location.replace(redirect);
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

<form on:submit|preventDefault={loginHandler}>
  {#if errorResponse}
    <label for="usernameOrEmail">{errorResponse?.message}</label>
  {/if}
  <Input
    id="usernameOrEmail"
    type="text"
    placeholder="Your username or email"
    required
    bind:value={usernameOrEmail}
  />
  <Input
    id="password"
    type="password"
    placeholder="Your password"
    required
    bind:value={password}
  />
  <button type="submit" disabled={loading}>Login</button>
  <a href="/forget-password">Forget my password!</a>
  <a href="/register">I don't have any account!</a>
  <a href="/">Back to home!</a>
</form>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 50%;
  }

  label {
    color: red;
  }
</style>
