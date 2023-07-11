<script>
  import toast from "svelte-french-toast";
  import store from "../store/store";
  import axios from "axios";
  let BASEURL = import.meta.env.VITE_BASEURL;
  let loading = false;
  let data = {
    title: "",
    shortDescription: "",
    longDescription: "",
    githubLink: "",
    demoLink: "",
  };
  let count = {
    title: 0,
    shortDescription: 0,
    longDescription: 0,
    githubLink: 0,
    demoLink: 0,
  };
  $: {
    count.title = data.title.length;
    count.shortDescription = data.shortDescription.length;
    count.longDescription = data.longDescription.length;
  }
  async function submit() {
    if (count.title < 10 || count.title > 50) {
      toast.error("Title should be between 10 and 50 characters long");
      return;
    }
    if (count.shortDescription < 50 || count.shortDescription > 150) {
      toast.error(
        "Short description should be between 50 and 150 characters long"
      );
      return;
    }
    if (count.longDescription < 100 || count.longDescription > 2500) {
      toast.error(
        "Long description should be between 100 and 2500 characters long"
      );
      return;
    }
    if (loading) return;
    loading = true;
    const response = await axios.post(BASEURL + "/create-project", data);
    loading = false;
    if (response.data.message === "Project created")
      toast.success("Project created");
    else toast.error("Something went wrong");
    data = {
      title: "",
      shortDescription: "",
      longDescription: "",
      githubLink: "",
      demoLink: "",
    };
    $store.isAddModalOpen = false;
  }
</script>

<input
  type="checkbox"
  bind:checked={$store.isAddModalOpen}
  class="modal-toggle"
/>
<div class="modal">
  <div class="modal-box max-w-3xl">
    <button
      on:click={() => ($store.isAddModalOpen = false)}
      class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">✕</button
    >
    <h3 class="font-bold text-lg">Add a Hackathon Submission</h3>
    <div class="py-4 flex flex-col gap-3">
      <div class="flex flex-col gap-2">
        <div class="flex justify-between items-center">
          <div class="label-text-alt font-bold opacity-50 uppercase">
            Project Title
          </div>
          <div>{count.title}/50</div>
        </div>
        <input
          bind:value={data.title}
          type="text"
          class="input-bordered input w-full"
          placeholder="Title of the project"
        />
      </div>
      <div class="flex flex-col gap-2">
        <div class="flex justify-between items-center">
          <div class="label-text-alt font-bold opacity-50 uppercase">
            Project Short Description
          </div>
          <div>{count.shortDescription}/150</div>
        </div>
        <textarea
          bind:value={data.shortDescription}
          class="textarea-bordered textarea w-full"
          placeholder="Briefly explain what your project does in 150 words or less"
        />
      </div>
      <div class="flex flex-col gap-2">
        <div class="flex justify-between items-center">
          <div class="label-text-alt font-bold opacity-50 uppercase">
            Project Long Description
          </div>
          <div>{count.longDescription}/2500</div>
        </div>
        <textarea
          bind:value={data.longDescription}
          class="textarea-bordered textarea w-full"
          placeholder="Explain your project in detail"
        />
      </div>
      <div class="flex flex-col gap-2">
        <div class="label-text-alt font-bold opacity-50 uppercase">Links</div>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
          <div class="join">
            <div
              class="bg-slate-100 px-4 join-item flex items-center justify-center"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                fill="black"
                viewBox="0 0 24 24"
                stroke-width="1.5"
                class="w-6 h-6 text-black"
                ><path
                  d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"
                /></svg
              >
            </div>
            <input
              type="text"
              bind:value={data.githubLink}
              class="join-item input input-bordered w-full"
              placeholder="Github Link"
            />
          </div>
          <div class="join">
            <div
              class="bg-slate-100 px-4 join-item flex items-center justify-center"
            >
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
                  d="M17.25 6.75L22.5 12l-5.25 5.25m-10.5 0L1.5 12l5.25-5.25m7.5-3l-4.5 16.5"
                />
              </svg>
            </div>
            <input
              type="text"
              bind:value={data.demoLink}
              class="join-item input input-bordered w-full"
              placeholder="Project Link"
            />
          </div>
        </div>
      </div>
    </div>
    <div class="flex">
      <button
        class="btn btn-ghost"
        on:click={() => {
          data.title = "";
          data.shortDescription = "";
          data.longDescription = "";
          data.githubLink = "";
          data.demoLink = "";
        }}>Clear</button
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
    </div>
  </div>
</div>
