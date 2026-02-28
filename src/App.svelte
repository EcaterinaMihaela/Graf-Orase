<script>
  import { onMount } from "svelte";
  import { Network } from "vis-network";

  let container;
  
    // Nodurile orașelor
    const cities = [
       { id: 1, label: "București", population: "1.83M", county: "București", description: "Capitala României" },
       { id: 2, label: "Craiova", population: "300k", county: "Dolj", description: "Oraș universitar și industrial" },
       { id: 3, label: "Timișoara", population: "319k", county: "Timiș", description: "Oraș cultural și economic important" },
       { id: 4, label: "Arad", population: "159k", county: "Arad", description: "Oraș de pe Mureș, important nod feroviar" },
       { id: 5, label: "Cluj-Napoca", population: "324k", county: "Cluj", description: "Capitala Transilvaniei" },
       { id: 6, label: "Iași", population: "290k", county: "Iași", description: "Oraș universitar și cultural" },
       { id: 7, label: "Constanța", population: "283k", county: "Constanța", description: "Port la Marea Neagră" }
     ];

    // Muchiile cu distanță
    const edges = [
      { from: 1, to: 2, label: "230 km", font: { align: "top" } },
      { from: 2, to: 3, label: "320 km", font: { align: "top" } },
      { from: 3, to: 4, label: "270 km", font: { align: "top" } },
      { from: 1, to: 7, label: "225 km", font: { align: "top" } }, // București - Constanța
      { from: 1, to: 6, label: "400 km", font: { align: "top" } }, // București - Iași
      { from: 4, to: 5, label: "350 km", font: { align: "top" } }, // Arad - Cluj
      { from: 5, to: 6, label: "300 km", font: { align: "top" } }  // Cluj - Iași
    ];

   // Setare titluri pentru hover automat
     edges.forEach(e => e.title = `Distanță: ${e.label}`);
     cities.forEach(c => c.title = `${c.label}\nPopulație: ${c.population}\nJudeț: ${c.county}`);

     onMount(() => {
       const data = { nodes: cities, edges };

    const options = {
      nodes: {
        shape: "dot",
        size: 24,
        color: "#D30C7B",
        font: { color: "#1F0322", size: 16, bold: true }
      },
      edges: {
        color: { color: "#9EBC9F" },
        width: 3,
        font: { color: "#000", size: 14, background: "white" }
      },
      layout: {
        hierarchical: false
      },
      physics: false,
      interaction: {
        hover: true
      }
    };

    const network = new Network(container, data, options);

    // Click pe nod se afis card cu informații
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
      });
    </script>

<!-- Container pentru graf -->
<div bind:this={container} class="w-full h-[500px] rounded-lg shadow-lg bg-gradient-to-r from-purple-200 via-pink-100 to-blue-100 p-4"></div>

<!-- Card info oraș -->
<div id="info" class="mt-4 p-4 bg-white rounded-lg shadow-md"></div>