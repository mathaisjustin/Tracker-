<script lang="ts">
  export let type: 'button' | 'submit' = 'button';
  export let variant: 'primary' | 'secondary' = 'primary';
</script>

<button
  {type}
  class={`rounded-xl px-4 py-2 text-sm font-semibold transition ${
    variant === 'primary'
      ? 'bg-slate-900 text-white hover:bg-slate-800'
      : 'bg-slate-200 text-slate-900 hover:bg-slate-300'
  }`}
>
  <slot />
</button>
