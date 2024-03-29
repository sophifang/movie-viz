<!--
  @component
  Generates an SVG Beeswarm chart using a [d3-force simulation](https://github.com/d3/d3-force).
 -->
<script>
  import { getContext, afterUpdate } from 'svelte';
  import { forceSimulation, forceX, forceY, forceCollide } from 'd3-force';

  const { data, xGet, height} = getContext('LayerCake');
  // const nodes = $data.map((d) => ({ ...d }));
  // console.log(nodes)
  let nodes = [];

  /** @type {Number} [r=4] - The circle radius size in pixels. */
  export let r = 4;

  /** @type {Number} [strokeWidth=1] - The circle's stroke width in pixels. */
  export let strokeWidth = 1;

  /** @type {String} [stroke='#fff'] - The circle's stroke color. */
  export let stroke = '#000';

  /** @type {String} [selected=''] - The selected certification. */
  export let selected = '';

  /** @type {String} [graph='rating'] - The selected graph. */
  export let graph = 'rating';

  /** @type {Number} [xStrength=0.95] - The value passed into the `.strength` method on `forceX`. See [the documentation](https://github.com/d3/d3-force#x_strength). */
  export let xStrength = 0.95;

  /** @type {Number} [yStrength=0.075] - The value passed into the `.strength` method on `forceY`. See [the documentation](https://github.com/d3/d3-force#y_strength). */
  export let yStrength = 0.075;

  /** @type {Function} [getTitle]��An accessor function to get the field on the data element to display as a hover label using a `<title>` tag. */
  export let getTitle = undefined;
 
  afterUpdate(() => {
    nodes = $data.map((d) => ({ ...d }));
    console.log(nodes)
  });


  $: simulation = forceSimulation(nodes)
    .force('x', forceX().x(d => $xGet(d)).strength(xStrength))
    .force('y', forceY().y($height / 2).strength(yStrength))
    .force('collide', forceCollide(r))
    .stop();

  $: {
    for ( let i = 0,
      n = Math.ceil(Math.log(simulation.alphaMin()) / Math.log(1 - simulation.alphaDecay()));
      i < n;
      ++i ) {
      simulation.tick();
    }
  }
</script>

<g class='bee-group'>
    <filter id="saturate">
      <feColorMatrix type="saturate" values="0"/>
    </filter>
  {#each simulation.nodes() as node}
    <defs>
      <pattern id={`image-pattern-${node.index}`} x="0" y="0" height="100%" width="100%" patternContentUnits="objectBoundingBox">
        <image href="{node.image}" x="0" y="0" width="1" height="1" preserveAspectRatio="xMidYMid slice" />
      </pattern>
    </defs>
    
    {#if selected === "all" || node.certificate === selected || selected === ""}
        <circle
        fill={`url(#image-pattern-${node.index})`}
        stroke='{stroke}'
        stroke-width='{strokeWidth}'
        cx='{node.x}'
        cy='{node.y}'
        r='{r}'
      >
        {#if getTitle}
          {#if graph === "rating"}
          <title>{node.title} ({node.rating} rating)</title>
          {/if}
          {#if graph === "gross"}
          <title>{node.title} (${+(node.gross/1000000).toFixed(2)}M gross)</title>
          {/if}
        {/if}
      </circle>
    {:else}

          <circle
                fill={`url(#image-pattern-${node.index})`}
                stroke='#666'
                stroke-width='{strokeWidth}'
                cx='{node.x}'
                cy='{node.y}'
                r='{r}'
                filter = {'url(#saturate)'}
                opacity = 0.35
              >
                {#if getTitle}
                  {#if graph === "rating"}
                  <title>{node.title} ({node.rating} rating)</title>
                  {/if}
                  {#if graph === "gross"}
                  <title>{node.title} ({+(node.gross/1000000).toFixed(2)}M gross)</title>
                  {/if}
                {/if}
              </circle>
        {/if}
  {/each}
</g>