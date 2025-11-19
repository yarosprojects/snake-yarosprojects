<script lang="ts">
    import { CONTROLS } from "../../consts/props";
    export let controlOnClick: (dir: string) => void;
</script>

<button
    aria-label="Right button"
    class="flex items-center justify-center p-3 rounded-xl shadow-md bg-black text-yellow-400 font-extrabold
                   hover:bg-gray-700 duration-200 active:scale-90 transition-all"
    on:click={() => controlOnClick(CONTROLS.right.id)}
>
    <i class="{CONTROLS.right.className} text-4xl md:text-xl"></i>
</button>
