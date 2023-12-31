<script>
  import PocketBase from "pocketbase";

  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  function logOutHandler() {
    pb.authStore.clear();
    window.location.reload();
  }
</script>

<header>
  {#if pb.authStore.isValid}
    <nav>
      {#if window.location.pathname === "/profile"}
        <a href="/">
          <button>Home</button>
        </a>
      {:else}
        <a href="/profile">
          <button>Profile</button>
        </a>
      {/if}
      <button on:click={logOutHandler}>Log Out</button>
    </nav>
  {:else}
    <nav>
      <a href="/login">
        <button>Login</button>
      </a>
      <a href="/register">
        <button>Register</button>
      </a>
    </nav>
  {/if}
</header>

<style>
  header {
    display: flex;
    flex-direction: row;
    width: 100%;
    justify-content: right;
  }
</style>
