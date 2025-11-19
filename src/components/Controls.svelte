<script lang="ts">
    import LeftButton from "./ui/LeftButton.svelte";
    import RightButton from "./ui/RightButton.svelte";
    import UpButton from "./ui/UpButton.svelte";
    import DownButton from "./ui/DownButton.svelte";
    
    export let controlOnClick: (dir: string) => void;
</script>

<div class="w-full flex md:hidden justify-center my-2 md:my-5 px-4 py-0 md:py-5 overflow-hidden" aria-label="Mobile controls">
    <div class="max-[325px]:grid-cols-2 grid grid-cols-3 gap-4 bg-black/30 p-4 rounded-3xl">
        <!-- Arriba -->
        <div class="max-[325px]:hidden"></div>
        <div class="max-[325px]:hidden">
            <UpButton controlOnClick={controlOnClick} />
        </div>
        <div class="max-[325px]:flex hidden">
            <LeftButton controlOnClick={controlOnClick} />
        </div>
        <div class="max-[325px]:hidden"></div>

        <!-- Izquierda / vacío / Derecha -->
        <div class="max-[325px]:hidden">
            <LeftButton controlOnClick={controlOnClick} />
        </div>
        <div class="max-[325px]:flex hidden">
            <RightButton controlOnClick={controlOnClick} />
        </div>

        <div class="max-[325px]:hidden"></div>

        <div class="max-[325px]:hidden">
            <RightButton controlOnClick={controlOnClick} />
        </div>
        <div class="max-[325px]:flex hidden">
            <DownButton controlOnClick={controlOnClick} />
        </div>

        <!-- Abajo -->
        <div class="max-[325px]:hidden"></div>
        
        <div class="max-[325px]:hidden">
            <DownButton controlOnClick={controlOnClick} />
        </div>
        <div class="max-[325px]:flex hidden">
            <UpButton controlOnClick={controlOnClick} />
        </div>

        <div class="max-[325px]:hidden"></div>
    </div>
</div>
