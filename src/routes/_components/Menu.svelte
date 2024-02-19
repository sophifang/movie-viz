<script>
    export let certificates;
    export let selectedCert;
  </script>

  <section class="menu-cont">
      <select class="menu" 
                      name="menu" 
                      id="menu" 
                      bind:value={selectedCert}>
          <option disabled selected value="">Select a certificate</option>
          <option value="all">All certificates</option>
          {#each certificates as certificate}
              <option value={certificate}>{certificate}</option>
          {/each}
      </select>
  </section>
  
  
  <style>     
      select {
          margin: 0;
          font-size: 1.18rem;
          border: 1px solid gray;
          border-radius: 5px;
      }
      
      .menu {
          width: 100%
      }
  </style>