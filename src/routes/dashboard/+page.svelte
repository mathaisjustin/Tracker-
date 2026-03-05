<script lang="ts">
  import StatCard from '$lib/components/dashboard/StatCard.svelte';
  import TrendChart from '$lib/components/dashboard/TrendChart.svelte';
  import CalendarHeatmap from '$lib/components/dashboard/CalendarHeatmap.svelte';
</script>

<div class="space-y-4">
  <header class="rounded-2xl border border-slate-200 bg-white p-4 shadow-sm">
    <h1 class="text-xl font-bold">Personal Dashboard</h1>
    <p class="text-sm text-slate-600">Track smoking consumption and smoke-free progress.</p>
  </header>

  <section class="grid gap-4 md:grid-cols-2 xl:grid-cols-4">
    <StatCard label="Daily Cigarette Count" value="5" />
    <StatCard label="Monthly Consumption" value="132" />
    <StatCard label="Money Spent" value="₹2,450" />
    <StatCard label="Smoke-Free Streak" value="3 days" />
  </section>

  <TrendChart />
  <CalendarHeatmap />
</div>
