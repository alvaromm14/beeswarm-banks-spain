<script>
    import { onMount, tick } from "svelte";
    export let data;
    export let width;

    import { fly, fade } from "svelte/transition";

    let tooltipWidth;

    const xNudge = 5;
    const yNudge = 5;

    $: xPosition = data.x + tooltipWidth + xNudge > width
        ? data.x - tooltipWidth - xNudge
        : data.x + xNudge;

    $: yPosition = data.y + yNudge;

    function displayRegionName(region) {
        if (region === "Estados Unidos") return "EE.UU.";
        return region;
    }

</script>

<div
    class="tooltip"
    in:fly={{ y: 10, duration: 120, delay: 120 }}
    out:fade
    style="position: absolute;
        top: {yPosition}px;
        left: {xPosition}px;"
    bind:clientWidth={tooltipWidth}
>
    <div class="title-row">
        <h1>{data.adquisicion}</h1>
        <span
            class="region-pill"
            title={data.banco}
        >
            {displayRegionName(data.banco)}
        </span>
    </div>
    <h2>{data.region}</h2>
    <div class="info">
        <span class="score"
            >{data.valor.toLocaleString("es-ES", { useGrouping: true })}M€</span
        >
    </div>
</div>

<style>
  .tooltip {
    background-color: white;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    padding: 8px;
    border-radius: 4px;
    max-width: 220px;
    pointer-events: none;
    transition: top 50ms ease, left 50ms ease;
    border: 1px solid #ddd;
  }

    .title-row {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 2px;
    }

    h1 {
    font-size: 0.9rem;
    font-weight: bold;
    color: #222;
    margin: 0;
    font-family: 'Lato';
        padding-right: 8px;
        flex-shrink: 1;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }

    .region-pill {
    background-color: #f28b79;
    color: white;
    padding: 2px 8px;
    border-radius: 12px;
    font-size: 12px;
    font-weight: 600;
    white-space: nowrap;
    user-select: none;
    border: 1px solid rgba(0, 0, 0, 0.15);
    flex-shrink: 0;
    }

    h2 {
    font-size: 0.8rem;
        font-weight: 400;
        font-style: italic;
    }

    .info {
        display: flex;
        justify-content: space-between;
        column-gap: 8px;
        align-items: center;
    }

    .score {
    font-size: 0.8rem;
        font-style: italic;
    color: #444;
    margin: 4px 0 0 0;
    }
</style>
