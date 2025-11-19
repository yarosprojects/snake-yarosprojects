<script lang="ts">
  import { projectsWeb, creator, github_project_link } from "./consts/metadata";
  import Header from "./components/Header.svelte";
  import Snake from "./components/Snake.svelte";
  import Welcome from "./components/Welcome.svelte";
  import PlayIcon from "./components/ui/PlayIcon.svelte";

  let start = false;
</script>

<Header />

<main
  class="p-4 relative flex flex-col justify-center items-center w-full min-h-screen"
>
  {#if start}
    <Snake />
  {:else}
    <div
      class="flex flex-col w-full justify-center items-center gap-2"
      id="welcome-box"
    >
      <Welcome creator={creator} />

      <button
        id="start-button"
        on:click={() => (start = true)}
        class="outline-none border-none hover:scale-105 active:scale-80 transition-all group"
        aria-label="Start game button"
      >
        <div class="button-overlay"></div>
        <span class="text-white/80 transition-colors group-hover:text-white">
          Start
          <PlayIcon />
        </span>
      </button>
      <div class="w-full flex flex-col justify-center items-center py-4">
        <a
          href={github_project_link}
          class="px-4 py-2 rounded-lg bg-black/25 flex justify-center items-center"
          aria-label="Tetris project link"
          target="_blank"
          referrerpolicy="no-referrer"
          title="Github project link"
        >
          <i
            class="icon-[line-md--github] text-3xl text-white/80 hover:text-white transition-all font-extrabold"
          ></i>
        </a>
      </div>
    </div>
  {/if}
</main>

<style>
  #start-button {
    font-size: 17px;
    border-radius: 12px;
    background: linear-gradient(
      180deg,
      rgb(56, 56, 56) 0%,
      rgb(36, 36, 36) 66%,
      rgb(41, 41, 41) 100%
    );
    color: rgb(218, 218, 218);
    border: none;
    padding: 2px;
    font-weight: 700;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  #start-button span {
    border-radius: 10px;
    padding: 0.8em 1.3em;
    padding-right: 1.2em;
    text-shadow: 0px 0px 20px #4b4b4b;
    width: 100%;
    display: flex;
    align-items: center;
    gap: 12px;
    transition: all 0.3s;
    background-color: rgb(29, 29, 29);
    background-image: radial-gradient(
        at 95% 89%,
        rgb(15, 15, 15) 0px,
        transparent 50%
      ),
      radial-gradient(at 0% 100%, rgb(17, 17, 17) 0px, transparent 50%),
      radial-gradient(at 0% 0%, rgb(29, 29, 29) 0px, transparent 50%);
  }

  #start-button:hover span {
    background-color: rgb(26, 25, 25);
  }

  #start-button .button-overlay {
    position: absolute;
    inset: 0;
    pointer-events: none;
    background: repeating-conic-gradient(
        rgb(48, 47, 47) 0.0000001%,
        rgb(51, 51, 51) 0.000104%
      )
      60% 60%/600% 600%;
    filter: opacity(10%) contrast(105%);
    -webkit-filter: opacity(10%) contrast(105%);
  }
</style>
