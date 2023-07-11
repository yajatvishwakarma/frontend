<script>
  import toast from "svelte-french-toast";
  import store from "../store/store";
  import Papa from "papaparse";
  import axios from "axios";
  let BASEURL = import.meta.env.VITE_BASEURL;
  let file = null;
  async function sendToServer(data) {
    const response = await axios.post(BASEURL + "/create-project", data);
    if (response.data.message === "Project created")
      toast.success("Project created");
    else toast.error("Something went wrong");
  }
  let loading = false;
  async function submit() {
    Papa.parse(file[0], {
      header: true,
      complete: function (results) {
        loading = true;
        results.errors.forEach((error) => {
          results.data.splice(error.row, 1);
        });
        let fin = results.data.map((item) => {
          return {
            title: item["Title"],
            shortDescription: item["Short Description"],
            longDescription: item["Long Description"],
            githubLink: item["Github Link"],
            demoLink: item["Demo Link"],
          };
        });
        fin.forEach((item) => {
          if (
            item.title.length < 10 ||
            item.title.length > 50 ||
            item.shortDescription.length < 50 ||
            item.shortDescription.length > 150 ||
            item.longDescription.length < 100 ||
            item.longDescription.length > 2500
          ) {
            toast.error("Skipping invalid project");
            return;
          } else {
            sendToServer(item);
          }
        });
        loading = false;
      },
    });
  }
</script>

<input
  type="checkbox"
  bind:checked={$store.isAddCSVModalOpen}
  class="modal-toggle"
/>
<div class="modal">
  <div class="modal-box max-w-3xl">
    <button
      on:click={() => ($store.isAddCSVModalOpen = false)}
      class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">✕</button
    >
    <h3 class="font-bold text-lg">Add Multiple Hackathon Submissions</h3>
    <!-- <div class="py-4 flex flex-col gap-3">
      <input
        type="file"
        bind:files={file}
        class="file-input file-input-bordered w-full"
      />
      <div class="label-text-alt">Upload a CSV file</div>
    </div>
    <div class="flex">
      <button on:click={() => (file = null)} class="btn btn-ghost">Clear</button
      >
      <button
        on:click={submit}
        disabled={loading}
        class="btn btn-circle ml-auto"
      >
        {#if loading}
          <div class="loading loading-spinner" />
        {:else}
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"
            />
          </svg>
        {/if}
      </button>
    </div> -->
    <div>
      This feature has been disabled to preserve resources, Please view the
      video submission to see how to add multiple projects.
      <button
        class="btn btn-sm"
        on:click={() => (window.location.href = "https://youtu.be/8UYPhdfQ1-w")}
        >Youtube video</button
      >
    </div>
  </div>
</div>
