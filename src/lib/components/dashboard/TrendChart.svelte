<script lang="ts">
  import { onMount } from 'svelte';
  import { Chart, type ChartConfiguration, LineController, LineElement, PointElement, LinearScale, CategoryScale, Tooltip, Legend, BarController, BarElement } from 'chart.js';

  Chart.register(LineController, LineElement, PointElement, LinearScale, CategoryScale, Tooltip, Legend, BarController, BarElement);

  let lineCanvas: HTMLCanvasElement;
  let barCanvas: HTMLCanvasElement;

  onMount(() => {
    const lineConfig: ChartConfiguration<'line'> = {
      type: 'line',
      data: {
        labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        datasets: [
          {
            label: 'Cigarettes',
            data: [8, 7, 9, 6, 5, 4, 3],
            borderColor: '#0891b2',
            backgroundColor: 'rgba(8,145,178,0.2)',
            tension: 0.35
          }
        ]
      }
    };

    const barConfig: ChartConfiguration<'bar'> = {
      type: 'bar',
      data: {
        labels: ['Marlboro', 'Gold Flake', 'Classic'],
        datasets: [
          {
            label: 'Monthly by brand',
            data: [92, 40, 24],
            backgroundColor: ['#0f172a', '#0891b2', '#6366f1']
          }
        ]
      }
    };

    const line = new Chart(lineCanvas, lineConfig);
    const bar = new Chart(barCanvas, barConfig);

    return () => {
      line.destroy();
      bar.destroy();
    };
  });
</script>

<div class="grid gap-4 lg:grid-cols-2">
  <div class="rounded-2xl border border-slate-200 bg-white p-4 shadow-sm">
    <h3 class="mb-2 text-sm font-semibold">Weekly Trend</h3>
    <canvas bind:this={lineCanvas}></canvas>
  </div>
  <div class="rounded-2xl border border-slate-200 bg-white p-4 shadow-sm">
    <h3 class="mb-2 text-sm font-semibold">Brand Breakdown</h3>
    <canvas bind:this={barCanvas}></canvas>
  </div>
</div>
