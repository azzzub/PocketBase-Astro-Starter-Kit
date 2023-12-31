<script>
  import PocketBase from "pocketbase";
  import { onMount } from "svelte";
  import EmailVerification from "./profile/EmailVerification.svelte";
  import ChangePassword from "./profile/ChangePassword.svelte";
  import ChangeEmail from "./profile/ChangeEmail.svelte";
  import ChangeInfo from "./profile/ChangeInfo.svelte";
  import ChangeAvatar from "./profile/ChangeAvatar.svelte";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  let data = {
    id: null,
    avatar: "",
    created: "",
    email: "",
    name: "",
    updated: "",
    username: "",
    verified: false,
  };

  onMount(() => {
    if (!pb.authStore.isValid) {
      window.location.replace("/login?redirect=/profile");
    }

    data = pb.authStore.model;
  });
</script>

<main>
  <h1>My Profile</h1>
  <h3>Profile Detail</h3>
  <ChangeAvatar {data} />
  <ChangeInfo {data} />
  <h3>Email Info</h3>
  <ChangeEmail {data} />
  <EmailVerification {data} />
  <ChangePassword userId={data.id} />
  <h3>Info</h3>
  <span>Account created: <strong>{data?.created}</strong></span>
  <span>Last updated: <strong>{data?.updated}</strong></span>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    width: 50%;
    gap: 12px;
    margin-bottom: 16px;
  }
</style>
