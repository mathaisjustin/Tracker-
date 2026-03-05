<script lang="ts">
  import Card from '$lib/components/ui/Card.svelte';
  export let label: string;
  export let value: string;
</script>

<Card>
  <p class="text-xs text-slate-500">{label}</p>
  <p class="mt-2 text-2xl font-bold text-slate-900">{value}</p>
</Card>
