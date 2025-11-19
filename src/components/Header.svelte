<script>
    import { projectsWeb } from "../consts/metadata";
</script>

<header class="static md:fixed bg-transparent md:inset-0 w-full h-(--header-h) px-4 flex justify-between items-center z-9999">
    <a href={projectsWeb} 
        class="flex justify-center items-center px-4 py-2 text-white hover:bg-gray-900/80 bg-gray-900/60 rounded-xl hover:scale-105 transition-all"
        aria-label="My wev of personal projects">
        <i class="icon-[line-md--arrow-left]"></i>
    </a>

    <div class="flex flex-row gap-4 justify-center items-center max-[325px]:pr-0 pl-4 pr-4 py-2">
        <span class="text-white/80 text-sm max-[325px]:text-[12px]">
            Developed in
        </span>
        <div class="flex flex-row justify-center items-center gap-2">
            <i class="icon-[devicon--vitejs] text-xl md:text-2xl"></i>
            <span class="text-white/80 text-xl md:text-2xl">+</span>
            <i class="icon-[logos--svelte-icon] text-xl md:text-2xl"></i>
        </div>
    </div>
</header>