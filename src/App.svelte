<script>
  import { onMount } from "svelte";
  import { Network } from "vis-network";

  let container;
  let search = "";
  let filterCounty = "";
  let network;
  let suggestions = [];
  let showSuggestions = false;

  const cities = [
    { id: 1, label: "București", population: "1.83M", county: "București", region: "Muntenia", description: "Capitala României" },
    { id: 2, label: "Craiova", population: "300k", county: "Dolj", region: "Oltenia", description: "Oraș universitar și industrial" },
    { id: 3, label: "Timișoara", population: "319k", county: "Timiș", region: "Banat", description: "Oraș cultural și economic important" },
    { id: 4, label: "Arad", population: "159k", county: "Arad", region: "Crișana", description: "Oraș de pe Mureș" },
    { id: 5, label: "Cluj-Napoca", population: "324k", county: "Cluj", region: "Transilvania", description: "Capitala Transilvaniei" },
    { id: 6, label: "Iași", population: "290k", county: "Iași", region: "Moldova", description: "Oraș universitar și cultural" },
    { id: 7, label: "Constanța", population: "283k", county: "Constanța", region: "Dobrogea", description: "Port la Marea Neagră" },
    { id: 8, label: "Brașov", population: "253k", county: "Brașov", region: "Transilvania", description: "Oraș turistic" },
    { id: 9, label: "Sibiu", population: "134k", county: "Sibiu", region: "Transilvania", description: "Capitală culturală" },
    { id: 10, label: "Oradea", population: "183k", county: "Bihor", region: "Crișana", description: "Oraș art nouveau" },
    { id: 11, label: "Ploiești", population: "180k", county: "Prahova", region: "Muntenia", description: "Centru industrial" },
    { id: 12, label: "Galați", population: "217k", county: "Galați", region: "Moldova", description: "Oraș port la Dunăre" },
    { id: 13, label: "Pitești", population: "155k", county: "Argeș", region: "Muntenia", description: "Centru auto" },
    { id: 14, label: "Bacău", population: "144k", county: "Bacău", region: "Moldova", description: "Oraș din Moldova" }
  ];

  const edges = [
    { from: 1, to: 2, label: "230 km" },
    { from: 2, to: 3, label: "320 km" },
    { from: 3, to: 4, label: "270 km" },
    { from: 1, to: 7, label: "225 km" },
    { from: 1, to: 6, label: "400 km" },
    { from: 4, to: 5, label: "350 km" },
    { from: 5, to: 6, label: "300 km" },

    { from: 1, to: 8, label: "170 km" },  // București – Brașov
    { from: 1, to: 11, label: "60 km" },  // București – Ploiești
    { from: 1, to: 13, label: "120 km" }, // București – Pitești
    { from: 1, to: 12, label: "250 km" }, // București – Galați

    { from: 5, to: 9, label: "150 km" },  // Cluj – Sibiu
    { from: 5, to: 10, label: "155 km" }, // Cluj – Oradea

    { from: 8, to: 9, label: "140 km" },  // Brașov – Sibiu
    { from: 8, to: 11, label: "100 km" }, // Brașov – Ploiești

    { from: 6, to: 14, label: "130 km" }, // Iași – Bacău
    { from: 6, to: 12, label: "230 km" }, // Iași – Galați

    { from: 3, to: 10, label: "170 km" }, // Timișoara – Oradea
    { from: 2, to: 13, label: "115 km" }  // Craiova – Pitești
  ];

const regionColors = {
  Moldova: "#3A86FF",
  Transilvania: "#8338EC",
  Muntenia: "#FF006E",
  Oltenia: "#FB5607",
  Banat: "#2EC4B6",
  Crișana: "#FFBE0B",
  Dobrogea: "#06D6A0"
};

  function initNetwork(nodesToShow = cities) {

    const nodeIds = nodesToShow.map(n => n.id);

    const filteredEdges = edges.filter(e =>
      nodeIds.includes(e.from) && nodeIds.includes(e.to)
    );

    const data = {
      nodes: nodesToShow.map(c => ({
        ...c,
        color: regionColors[c.region] || "#999",
        title: `${c.label}
      Populație: ${c.population}
      Județ: ${c.county}
      Regiune: ${c.region}`
      })),
      edges: filteredEdges.map(e => ({
        ...e,
        title: `Distanță: ${e.label}`
      }))
    };


    const options = {
      nodes: {
        shape: "dot",
        size: 24,
        font: { color: "#1F0322", size: 16, bold: true }
      },
      edges: {
        color: "#9EBC9F",
        width: 3,
        font: { color: "#000", size: 14, background: "white" }
      },
      layout: { hierarchical: false },
      physics: true,
      interaction: { hover: true }
    };

    if (network) network.destroy(); // distruge vechiul network
    network = new Network(container, data, options);

    network.on("click", (params) => {
      if (params.nodes.length > 0) {
        const city = cities.find(c => c.id === params.nodes[0]);
        document.getElementById("info").innerHTML = `
          <h2 class="text-xl font-bold">${city.label}</h2>
          <p>Populație: ${city.population}</p>
          <p>Județ: ${city.county}</p>
          <p>${city.description}</p>
        `;
      }
    });
  }

  // Reinițializează network-ul la căutare sau filtrare
  function filterNetwork() {
    if (!search && !filterCounty) {
      initNetwork(cities);
      return;
    }

    const filtered = cities.filter(c =>
      c.label.toLowerCase().includes(search.toLowerCase()) &&
      c.county.toLowerCase().includes(filterCounty.toLowerCase())
    );

    initNetwork(filtered);
  }

  onMount(() => {
    initNetwork(); // initializează tot
  });



  //Fctie autocomplete pentru cautare- tip lista
  function updateSuggestions() {
    if (!search.trim()) {
      suggestions = [];
      showSuggestions = false;
      return;
    }

    suggestions = cities.filter(c =>
      c.label.toLowerCase().includes(search.toLowerCase())
    );

    showSuggestions = true;
  }

  function selectCity(city) {
    search = city.label;
    showSuggestions = false;

    // selectare în graf
    network.selectNodes([city.id]);
    network.focus(city.id, { scale: 1.5 });

    // afișare info
    document.getElementById("info").innerHTML = `
      <h2 class="text-xl font-bold">${city.label}</h2>
      <p>Populație: ${city.population}</p>
      <p>Județ: ${city.county}</p>
      <p>${city.description}</p>
    `;
  }
</script>




<!-- Partea de cautare oras si pe regiuni -->
<div class="mb-4 flex gap-4">
  <div class="relative">
    <input
      type="text"
      placeholder="Search city..."
      bind:value={search}
      on:input={() => { updateSuggestions(); filterNetwork(); }}
      class="p-2 border rounded w-64"
    />

    {#if showSuggestions}
      <div class="absolute bg-white border rounded shadow-md w-64 mt-1 max-h-48 overflow-y-auto z-10">
        {#each suggestions as city}
          <button
            type="button"
            class="p-2 hover:bg-gray-100 cursor-pointer w-full text-left"
            on:click={() => selectCity(city)}
          >
            {city.label}
          </button>
        {/each}
      </div>
    {/if}
  </div>
</div>

<!-- Culori regiuni -->
<div class="flex gap-6">

  <!-- Grafic -->
  <div class="w-3/4">
    <div bind:this={container}
         class="w-full h-[500px] rounded-lg shadow-lg bg-gradient-to-r from-purple-200 via-pink-100 to-blue-100 p-4">
    </div>
  </div>

  <!-- Sidebar dreapta -->
  <div class="w-1/4 space-y-4">

    <!-- Legendă -->
    <div class="p-4 bg-white rounded-lg shadow-md">
      <h3 class="text-lg font-bold mb-3">Legendă regiuni</h3>

      <div class="space-y-2 text-sm">
        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#3A86FF"></span>
          Moldova
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#8338EC"></span>
          Transilvania
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#FF006E"></span>
          Muntenia
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#FB5607"></span>
          Oltenia
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#FFBE0B"></span>
          Crișana
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#2EC4B6"></span>
          Banat
        </div>

        <div class="flex items-center gap-2">
          <span class="w-4 h-4 rounded-full" style="background:#06D6A0"></span>
          Dobrogea
        </div>
      </div>
    </div>

    <!-- Info oraș -->
    <div id="info" class="p-4 bg-white rounded-lg shadow-md"></div>

  </div>
</div>