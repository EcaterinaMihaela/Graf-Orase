<script>
  import { onMount } from "svelte";
  import { Network } from "vis-network";

  let container;

  onMount(() => {
    // Nodurile orașelor
    const nodes = [
      { id: 1, label: "București" },
      { id: 2, label: "Craiova" },
      { id: 3, label: "Timișoara" },
      { id: 4, label: "Arad" }
    ];

    // Muchiile cu distanță
    const edges = [
      { from: 1, to: 2, label: "230 km", font: { align: "top" } },
      { from: 2, to: 3, label: "320 km", font: { align: "top" } },
      { from: 3, to: 4, label: "270 km", font: { align: "top" } }
    ];

    const data = { nodes, edges };

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

    // Interactivitate: click pe nod
    network.on("click", (params) => {
      if (params.nodes.length > 0) {
        const node = nodes.find(n => n.id === params.nodes[0]);
        alert(`Oraș selectat: ${node.label}`);
      }
    });
  });
</script>

<!-- Container pentru graf -->
<div bind:this={container} class="w-full h-[500px] rounded-lg shadow-lg bg-slate-100 p-4"></div>