<script lang="ts">
  import Button from '$lib/components/ui/Button.svelte';
</script>

<div class="mx-auto flex min-h-screen max-w-md items-center p-6">
  <form class="w-full space-y-4 rounded-2xl border bg-white p-6 shadow-sm">
    <h1 class="text-xl font-bold">Login</h1>
    <input class="w-full rounded-xl border p-3" type="email" placeholder="Email" />
    <input class="w-full rounded-xl border p-3" type="password" placeholder="Password" />
    <Button type="submit">Sign in</Button>
  </form>
</div>
