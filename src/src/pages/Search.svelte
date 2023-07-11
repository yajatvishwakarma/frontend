<script>
  import { onMount } from "svelte";
  import ModalWrapper from "../components/ModalWrapper.svelte";
  import Nav from "../components/Nav.svelte";
  import ProjectCard from "../components/ProjectCard.svelte";
  import axios from "axios";
  import { getRandomLoadingMessage } from "../store/loading";
  export let params;
  let BASEURL = import.meta.env.VITE_BASEURL;
  let data = {
    projects: [],
  };
  let loading = false;
  onMount(async () => {
    loading = true;
    let response;
    response = await axios.post(BASEURL + "/search", { query: params.id });
    loading = false;
    data.projects = response.data.projects;
  });
</script>

<ModalWrapper>
  <div class="p-3 md:p-5 lg:p-10">
    <Nav />
    <div class="text-xl mt-6">
      Searching for: <span class="font-semibold">{params.id} </span>
    </div>
    <section class="grid gap-5 grid-cols-1 md:grid-cols-3 lg:grid-cols-4 mt-10">
      {#if loading}
        <div
          class="flex col-span-4 flex-col gap-3 h-[80vh] justify-center items-center"
        >
          <span class="loading loading-spinner loading-md" />
          <div class="max-w-xs text-sm text-center">
            {getRandomLoadingMessage()}
          </div>
        </div>
      {:else}
        {#each data.projects as project, index}
          <ProjectCard
            description={project.shortDescription}
            title={project.title}
            id={project._id}
            isReviewed={project.isReviewed}
            index={index + 1}
          />
        {/each}
      {/if}
    </section>
  </div>
</ModalWrapper>
