<script lang="ts">
    export let score = 0;
</script>

<div
    class="px-4 md:px-0 py-3 md:py-10"
    id="score-board"
    aria-label="Score board"
>
    <span class="text-xl md:text-2xl">
        <span class="text-white/90">Score: </span>
        <span class="text-yellow-400 font-extrabold">{score}</span>
    </span>
</div>
