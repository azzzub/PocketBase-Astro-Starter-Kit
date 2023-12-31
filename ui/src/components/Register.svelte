<script>
  import PocketBase from "pocketbase";
  import Input from "./atoms/Input.svelte";
  import { onMount } from "svelte";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  let loading = false;
  let errorResponse = null;

  let data = {
    username: "",
    email: "",
    emailVisibility: true,
    password: "",
    passwordConfirm: "",
    name: "",
  };

  async function registerHandler() {
    loading = true;
    errorResponse = null;
    try {
      const newRecord = await pb.collection("users").create(data);
      if (newRecord) {
        const login = await pb
          .collection("users")
          .authWithPassword(newRecord.email, data.password);
        if (login) {
          window.location.replace("/");
        }
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

<form on:submit|preventDefault={registerHandler}>
  <Input
    id="name"
    type="text"
    placeholder="Your name (optional)"
    bind:value={data.name}
    error={errorResponse}
  />
  <Input
    id="username"
    type="text"
    placeholder="Your username (optional)"
    bind:value={data.username}
    error={errorResponse}
  />
  <Input
    id="email"
    type="email"
    placeholder="Your email*"
    required
    bind:value={data.email}
    error={errorResponse}
  />
  <Input
    id="password"
    type="password"
    placeholder="Your password*"
    required
    bind:value={data.password}
    error={errorResponse}
  />
  <Input
    id="passwordConfirm"
    type="password"
    placeholder="Type your password again!*"
    required
    bind:value={data.passwordConfirm}
    error={errorResponse}
  />
  <button type="submit" disabled={loading}>Register</button>
  <a href="/login">I have an account!</a>
</form>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 50%;
  }
</style>
