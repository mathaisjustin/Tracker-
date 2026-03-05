<script lang="ts">
  const days = Array.from({ length: 30 }, (_, i) => ({
    day: i + 1,
    level: Math.floor(Math.random() * 5)
  }));
  const shades = ['bg-slate-100', 'bg-cyan-100', 'bg-cyan-200', 'bg-cyan-300', 'bg-cyan-500'];
</script>

<div class="rounded-2xl border border-slate-200 bg-white p-4 shadow-sm">
  <h3 class="mb-3 text-sm font-semibold">Calendar Heatmap</h3>
  <div class="grid grid-cols-10 gap-2">
    {#each days as d}
      <div class={`flex h-8 items-center justify-center rounded text-xs ${shades[d.level]}`}>{d.day}</div>
    {/each}
  </div>
</div>
