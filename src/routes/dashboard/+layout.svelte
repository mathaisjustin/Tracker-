<script lang="ts">
  import Sidebar from '$lib/components/dashboard/Sidebar.svelte';
</script>

<div class="min-h-screen bg-slate-50 p-4 lg:p-6">
  <div class="mx-auto grid max-w-7xl gap-4 lg:grid-cols-[260px,1fr]">
    <Sidebar />
    <main><slot /></main>
  </div>
</div>
