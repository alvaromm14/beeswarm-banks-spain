<script>
    export let xScale;
    export let height;

    $: ticks = [2009, 2015, 2020, 2025]
</script>

<g class="axis x">
    {#each ticks as tick}
        <g class="tick" transform="translate({xScale(tick)}, 0)">
            <text x="0" y={height} text-anchor="middle" dominant-baseline="hanging">{tick}</text>
            <line x1="0" x2="0" y1="0" y2={height - 4} stroke="hsla(215, 15%, 91%, 1)"></line>
        </g>
    {/each}
</g>