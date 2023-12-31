<script>
  import PocketBase from "pocketbase";
  const pb = new PocketBase(import.meta.env.PUBLIC_POCKETBASE_URL);

  export let data;
  let files;

  let loading = false;
  let errorResponse = null;

  async function changeAvatarHandler() {
    loading = true;
    try {
      const formData = new FormData();
      formData.append("avatar", files[0]);
      const createdRecord = await pb
        .collection("users")
        .update(data.id, formData);
      if (createdRecord) {
        data = { ...data, avatar: createdRecord.avatar };
      }
    } catch (error) {
      errorResponse = error.response;
    } finally {
      loading = false;
    }
  }

  function selectFile() {
    document.getElementById("avatar-input").click();
  }
</script>

<div class="avatar-ctr">
  <img
    src={pb.files.getUrl(data, data.avatar) ||
      "https://upload.wikimedia.org/wikipedia/commons/2/2c/Default_pfp.svg"}
    alt="Avatar"
    class="avatar"
  />
  <input
    id="avatar-input"
    type="file"
    bind:files
    hidden
    on:change={changeAvatarHandler}
  />
  <button on:click={selectFile}>Change Avatar</button>
</div>

<style>
  .avatar-ctr {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: 16px;
  }

  .avatar {
    vertical-align: middle;
    width: 50px;
    height: 50px;
    border-radius: 50%;
  }
</style>
