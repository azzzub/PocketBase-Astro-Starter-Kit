<script>
  import Input from "../atoms/Input.svelte";
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  export let userId;

  let data = {
    oldPassword: "",
    password: "",
    passwordConfirm: "",
  };

  let loading = false;
  let errorResponse = null;

  async function changePasswordHandler() {
    loading = true;
    errorResponse = null;
    try {
      const record = await pb.collection("users").update(userId, data);
      if (record) {
        pb.authStore.clear();
        window.location.reload();
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }
</script>

<form class="ctr" on:submit|preventDefault={changePasswordHandler}>
  <h3>Change Password</h3>
  <Input
    id="oldPassword"
    type="password"
    bind:value={data.oldPassword}
    required
    placeholder="Type your current password"
    label="Current Password"
    error={errorResponse}
  />

  <Input
    id="password"
    type="password"
    bind:value={data.password}
    required
    placeholder="Type your new password"
    label="New Password"
    error={errorResponse}
  />

  <Input
    id="passwordConfirm"
    type="password"
    bind:value={data.passwordConfirm}
    required
    placeholder="Type again your new password"
    label="Confirm New Password"
    error={errorResponse}
  />

  <button type="submit" disabled={loading}>Change Password</button>
</form>

<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin-bottom: 16px;
  }
</style>
