<script>
  import data from "$data/data.js";
  import AxisX from "$components/AxisX.svelte";
  import Legend from "$components/Legend.svelte";
  import Tooltip from "$components/Tooltip.svelte";

  import { forceSimulation, forceX, forceY, forceCollide } from "d3-force";

  const RADIUS = 5;
  let nodes = [];
  let simulation = forceSimulation();

  $: simulation
    .nodes(data)
    .force(
      "x",
      forceX()
        .x((d) => xScale(d.año))
        .strength(0.8),
    )
    .force(
      "y",
      forceY()
        .y(innerHeight / 2)
        .strength(0.2),
    )
    .force(
      "collide",
      forceCollide().radius((d) => radiusScale(d.valor) + 1.5),
    )
    .alpha(0.6)
    .alphaDecay(0.04)
    .on("tick", () => {
      nodes = simulation.nodes();
    })
    .restart();

  let width = 400,
    height = 500;

  const margin = {
    top: 25,
    right: 100,
    bottom: 25,
    left: 100,
  };

  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  import { scaleLinear, scaleSqrt } from "d3-scale";

  $: xScale = scaleLinear().domain([2009, 2025]).range([0, innerWidth]);

  $: radiusScale = scaleSqrt()
    .domain([20, 100])
    .range(width < 568 ? [2, 6] : [4, 8]);

  let hovered;
  let hoveredBank;
  let hoveredRegion = null;

  import { fade } from "svelte/transition";

  export const regionColors = {
    Europa: "#559df2",
    "Estados Unidos": "#485fb3",
    Sudamérica: "#f5b956",
    Turquía: "#d0acf2",
  };

  export function getRegionColor(regionName) {
    return regionColors[regionName] || "#999";
  }
</script>

<h1>La expansión de la banca española tras la crisis financiera</h1>
<h2>
  Compras de firmas extranjeras <span style="color:grey;">(<span
    style="
    display: inline-block;
    margin-left: 2px;
    width: 9px;
    height: 9px;
    border: 1px solid grey;
    border-radius: 50%;
    vertical-align: middle;
  "
  ></span> &rarr; ventas)</span>
</h2>
<Legend colorScale={regionColors} bind:hoveredRegion {getRegionColor} />
<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-mouse-events-have-key-events -->
<div class="chart-container" bind:clientWidth={width}>
  <svg {width} {height}>
    <g
      class="inner-chart"
      transform="translate({margin.left}, {margin.top})"
      on:mouseleave={() => {
        hovered = null;
        hoveredBank = null;
      }}
    >
      <AxisX {xScale} height={innerHeight} />
      {#each nodes as node}
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <circle
          in:fade={{ delay: 400 }}
          cx={node.x}
          cy={node.y}
          r={radiusScale(node.valor)}
          fill={node.operación === "Venta"
            ? "white"
            : getRegionColor(node.region)}
          stroke={node.operación === "Venta" ? "#cfcfcf" : "#555"}
          stroke-width={hoveredBank || hoveredRegion
            ? hoveredBank === node ||
              hoveredRegion === node.region ||
              node.operación === "Venta"
              ? "1"
              : "0"
            : node.operación === "Venta"
              ? "1"
              : "0"}
          opacity={hoveredBank || hoveredRegion
            ? hoveredBank === node || hoveredRegion === node.region
              ? 1
              : 0.3
            : 1}
          on:mouseover={() => {
            hoveredBank = node;
            hovered = {
              ...node,
              x: node.x + margin.left,
              y: node.y + margin.top,
            };
          }}
          on:focus={() => {
            hovered = node;
          }}
          tabindex="0"
          on:click={(event) => {
            event.stopPropagation();
          }}
        />
      {/each}
    </g>
  </svg>
  {#if hovered}
    <Tooltip data={hovered} width={innerWidth}/>
  {/if}
</div>

<style>
  :global(.tick text, .axis-title) {
    font-size: 12px;
    font-weight: 400;
    fill: hsla(212, 10%, 53%, 1);
    user-select: none;
  }

  :global(.axos-title) {
    font-size: 14px;
  }

  h1 {
    font-size: 20px;
    margin-bottom: 3px;
    font-weight: 600;
    text-align: center;
  }

  h2 {
    font-size: 15px;
    margin-bottom: 6px;
    font-weight: 400;
    font-style: italic;
    text-align: center;
    position: relative;
  }

  circle {
    transition:
      stroke 300ms ease,
      opacity 300ms ease;
  }
</style>
