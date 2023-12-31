<script>
  import Input from "../atoms/Input.svelte";
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  export let data;

  let loading = false;
  let errorResponse = null;

  async function saveNewEmailHandler() {
    loading = true;
    errorResponse = null;
    try {
      const newUsername = await pb.collection("users").update(data.id, {
        username: data.username,
        name: data.name,
      });
      if (newUsername) {
        window.location.reload();
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }
</script>

<Input
  id="name"
  bind:value={data.name}
  label="Name"
  disabled={loading}
  error={errorResponse}
/>
<Input
  id="username"
  bind:value={data.username}
  label="Username"
  disabled={loading}
  error={errorResponse}
/>
<button on:click={saveNewEmailHandler} disabled={loading}>Save</button>
