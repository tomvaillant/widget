<script>
    import './styles.css';
    import { onMount } from 'svelte';
    import Papa from 'papaparse';
  
    let electoralData = {};
    const TOTAL_VOTES = 539;
  
    const CSV_URL = 'https://raw.githubusercontent.com/tomvaillant/us-wahl-data/refs/heads/main/widget_simplified.csv';
  
    onMount(async () => {
      const response = await fetch(CSV_URL);
      const csvText = await response.text();
      
      Papa.parse(csvText, {
        header: true,
        dynamicTyping: true,
        complete: (results) => {
          electoralData = results.data[0];
          console.log('Electoral Data:', electoralData);
        }
      });
    });
  
    function getWidth(votes) {
      return votes ? `${(votes / TOTAL_VOTES) * 100}%` : '0%';
    }
  
    $: trumpVotes = (electoralData.republican || 0) + (electoralData.lean_trump || 0) + (electoralData.battleground_trump || 0);
    $: harrisVotes = (electoralData.democrat || 0) + (electoralData.lean_harris || 0) + (electoralData.battleground_harris || 0);
  </script>
  
  <main>
    <div class="main-wrapper">
      <img src="harris.png" alt="Kamala Harris" class="candidate-image" />
      <div class="graph-container">
        <div class="vote-totals">
          <span class="harris-votes">{harrisVotes}</span>
          <span class="trump-votes">{trumpVotes}</span>
        </div>
        <div class="bars">
          <div class="democrat" style="width: {getWidth(electoralData.democrat)}"></div>
          <div class="lean_harris" style="width: {getWidth(electoralData.lean_harris)}"></div>
          <div class="battleground_harris" style="width: {getWidth(electoralData.battleground_harris)}"></div>
          <div class="battleground_trump" style="width: {getWidth(electoralData.battleground_trump)}"></div>
          <div class="lean_trump" style="width: {getWidth(electoralData.lean_trump)}"></div>
          <div class="republican" style="width: {getWidth(electoralData.republican)}"></div>
        </div>
        <div class="candidate-names">
          <span>Kamala Harris</span>
          <span>Donald Trump</span>
        </div>
      </div>
      <img src="trump.png" alt="Donald Trump" class="candidate-image" />
    </div>
    
    <div class="legend-wrapper">
      <div class="legend">
        <span class="lead-3">3+ point lead</span>
        <span class="lead-1">1-3 point lead</span>
      </div>
    </div>
  </main>