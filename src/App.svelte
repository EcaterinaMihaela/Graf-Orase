<script>
  import { onMount } from "svelte";
  import { Network } from "vis-network";

  let container;

  onMount(() => {
    const nodes = [
      { id: 1, label: "București" },
      { id: 2, label: "Craiova" },
      { id: 3, label: "Timișoara" },
      { id: 4, label: "Arad" }
    ];

    const edges = [
      { from: 1, to: 2 },
      { from: 2, to: 3 },
      { from: 3, to: 4 }
    ];

    const data = { nodes, edges };
    const options = {
      nodes: { shape: "dot", size: 16, color: "#3b82f6", font: { color: "#fff" } },
      edges: { color: "#f87171", width: 2 },
      physics: false
    };

    new Network(container, data, options);
  });
</script>

<div bind:this={container} class="w-full h-[400px] mt-6"></div>