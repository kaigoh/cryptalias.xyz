<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.png';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<title>Cryptalias.xyz</title>
</svelte:head>
{@render children()}

<footer class="flex items-center justify-center text-sm text-muted">
	<a
		class="inline-flex items-center gap-2 rounded-pill border border-border bg-surface/70 px-4 py-2 transition hover:border-primary/70 hover:text-primary"
		href="https://github.com/kaigoh/cryptalias"
		rel="noopener noreferrer"
		target="_blank"
	>
		<svg aria-hidden="true" class="h-4 w-4" viewBox="0 0 24 24" fill="currentColor">
			<path
				d="M12 2C6.48 2 2 6.58 2 12.26c0 4.54 2.87 8.39 6.84 9.75.5.1.68-.22.68-.49 0-.24-.01-.88-.01-1.73-2.78.62-3.37-1.38-3.37-1.38-.45-1.19-1.11-1.5-1.11-1.5-.91-.64.07-.63.07-.63 1 .07 1.53 1.06 1.53 1.06.9 1.56 2.36 1.11 2.94.85.09-.66.35-1.11.63-1.36-2.22-.26-4.56-1.14-4.56-5.07 0-1.12.39-2.03 1.03-2.75-.1-.26-.45-1.31.1-2.73 0 0 .85-.28 2.8 1.05.81-.23 1.68-.35 2.55-.35.87 0 1.74.12 2.55.35 1.95-1.33 2.8-1.05 2.8-1.05.55 1.42.2 2.47.1 2.73.64.72 1.03 1.63 1.03 2.75 0 3.94-2.34 4.81-4.57 5.06.36.32.68.95.68 1.92 0 1.38-.01 2.5-.01 2.84 0 .27.18.59.69.49 3.96-1.36 6.83-5.21 6.83-9.75C22 6.58 17.52 2 12 2z"
			/>
		</svg>
		<span>GitHub</span>
	</a>
</footer>
