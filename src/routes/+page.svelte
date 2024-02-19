<script>
  import * as d3 from 'd3';
  import {onMount} from 'svelte';
  let data = [];
  let dataTransformed = [];
  onMount(async function() {
        const res = await fetch(
            'movies_top200.csv',
        );
        const csv = await res.text();
      data = d3.csvParse(csv, d3.autoType);

      getCertificates();
      console.log(data);
	});
  import { LayerCake, Svg, Html } from 'layercake';
  import Menu from './_components/Menu.svelte';
  import Key from './_components/Key.html.svelte';
  import AxisX from './_components/AxisX.svelte';
  import Beeswarm from './_components/BeeswarmForce.svelte';
  const xKey = 'rating';
  const titleKey = 'title';
  const imageUrl = 'image';
  const r = 15;
  let certificates = []; // menu built from bookData
	let selectedCert = ""; //  menu selection	
	
	const getCertificates = () => {
		for (let movieObj of data) {
			if (!certificates.includes(movieObj.certificate)) {
				certificates = [...certificates, movieObj.certificate]
			}
		}
		certificates = certificates.sort();
    console.log(certificates);
	}	
</script>

<main>
  <h1>Does a Movie's Certificate Affect Its Rating?</h1>
  <h3>An analysis of the top 200 highest-rated movies on IMDb from 1921-2020</h3>
  <p><i>Please view visualization in full screen for best experience.</i></p>
  <p><b>What do we mean by certificate?</b><br>A movie's certificate reflects its suitability for certain audiences based on its content. See below for specific certificates and their meanings</p>
</main>
<section id="query-section">
	<Menu {certificates} bind:selectedCert />
</section>	
<style>
  main {
    text-align: center;
  }
  .chart-container {
    width: 80%;
    height: 650px;
    margin: 0 auto;
    text-align: center;
    padding-bottom: 100px;
  }
</style>

<div class='chart-container'>
  <LayerCake
    padding={{bottom: 15}}
    x={xKey}
    data={data}
    let:width
  >

    <Svg>
      <AxisX/>
      <Beeswarm
        r={width < 400 ? r / 1.25 : r}
        strokeWidth={1}
        xStrength={0.95}
        yStrength={0.075}
        getTitle={d => d[titleKey]}
        selected = {selectedCert}
      />
    </Svg>

    <Html pointerEvents={false}>
      <Key shape='circle' />
    </Html>

  </LayerCake>
  <p>Rating</p>
</div>