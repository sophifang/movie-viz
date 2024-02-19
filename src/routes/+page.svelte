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
  import Radio from './_components/Radio.svelte';
  import Menu from './_components/Menu.svelte';
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
  const logScale = d3.scaleLog().domain([500000, 900000000])
  const logFormat10 = logScale.tickFormat(10)
  let radioValue;
	
	const options = [{
		value: 'rating',
		label: 'Rating',
	}, {
		value: 'gross',
		label: 'Gross ($)',
	}]
</script>

<main>
  <h1>Does a Movie's Certificate Affect Its Performance?</h1>
  <h3>An analysis of the top 200 highest-rated movies on IMDb from 1921-2020</h3>
  <p><i>Please view this visualization in desktop full screen for the best experience.</i></p>
</main>
<section id="query-section">
	<Menu {certificates} bind:selectedCert />
  <Radio {options} legend='Select a performance metric' bind:userSelected={radioValue}/>
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
  #query-section {
		width: 80%;
		display: flex;
		justify-content: left;
		align-items: center;
    margin-left: 150px;
	}
  .certification {
    width: 80%;
    text-align: left;
    margin-left: 150px;
    padding-bottom: 100px;
  }
</style>
{#if radioValue === 'rating'}
<div class='chart-container'>
  <p><b>Rating</b></p>
  <br>
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
        graph ='rating'
      />
    </Svg>
  </LayerCake>
</div>
{/if}

{#if radioValue === 'gross'}
<div class='chart-container'>
  <p><b>Gross ($)</b><br><i>Note: Movies with missing gross values have been excluded from the visualization below.</i></p>
  <br>
  <LayerCake
    padding={{bottom: 15}}
    x={'gross'}
    data={data.filter(function(d){return (d.gross > 0)})}
    let:width
  >

    <Svg>
      <AxisX
      formatTick = {d => d/1000000+"M"}
      />
      <Beeswarm
        r={width < 400 ? r / 1.25 : r}
        strokeWidth={1}
        xStrength={0.95}
        yStrength={0.075}
        getTitle={d => d[titleKey]}
        selected = {selectedCert}
        graph='gross'
      />
    </Svg>
  </LayerCake>
</div>
{/if}
<div class = "certification">
  <p><b>What Do We Mean By Certificate?</b><br>A movie's certificate reflects its suitability for certain audiences. See below for specific certificates and their meanings</p>
<p><b>Explanation of U.S. Movie Certification Symbols</b></p>
<ul>
  <li><strong>A:</strong> Adults only.</li>
  <li><strong>G:</strong> General audiences – All ages admitted.</li>
  <li><strong>PG:</strong> Parental guidance suggested – Some material may not be suitable for children.</li>
  <li><strong>PG-13:</strong> Parents strongly cautioned – Some material may be inappropriate for children under 13.</li>
  <li><strong>R:</strong> Restricted – Under 17 requires accompanying parent or adult guardian.</li>
  <li><strong>TV-14:</strong> This program contains material that most parents would find unsuitable for children under 14 years of age.</li>
  <li><strong>TV-MA:</strong> This program is specifically designed to be viewed by adults and therefore may be unsuitable for children under 17.</li>
  <li><strong>U:</strong> Unrestricted - Suitable for children aged 4 and older. </li>
  <li><strong>UA:</strong> Unrestricted - Parental discretion advisory for children under 12 years.</li>
</ul>
</div>