<!-- File autogenerated by the Paraglide-SvelteKit CLI - Feel free to edit -->
<script>
	import { ParaglideJS } from "@inlang/paraglide-sveltekit";
	import { i18n } from "$lib/i18n";
	import { page } from "$app/stores";

  let { children } = $props();
	let currentPage = $derived($page.route);
</script>

<nav>
  <a href="/wrap">Wrap</a>
  <a href="/nowrap">No wrap</a>
</nav>

{#if currentPage.id === "/nowrap"}
	{@render children()}
{:else}
	<ParaglideJS {i18n}>
		{@render children()}
	</ParaglideJS>
{/if}
