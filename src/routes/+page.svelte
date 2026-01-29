<script lang="ts">
	import logo from '$lib/assets/logo.png';
	import { resolveAddress } from '$lib/packages/cryptalias/index';

	const coins = [
		{ ticker: 'xmr', name: 'Monero', color: '#FF6600', color2: '#FF944D' },
		{ ticker: 'btc', name: 'Bitcoin', color: '#F7931A', color2: '#FFB347' },
		{ ticker: 'eth', name: 'Ethereum', color: '#627EEA', color2: '#8FA6FF' },
		{ ticker: 'usdt', name: 'Tether', color: '#26A17B', color2: '#45C59B' },
		{ ticker: 'bnb', name: 'BNB', color: '#F3BA2F', color2: '#FFD976' },
		{ ticker: 'sol', name: 'Solana', color: '#00FFA3', color2: '#7CFFB9' },
		{ ticker: 'xrp', name: 'XRP', color: '#23292F', color2: '#5A6A77' },
		{ ticker: 'usdc', name: 'USD Coin', color: '#2775CA', color2: '#5DA0E6' },
		{ ticker: 'ada', name: 'Cardano', color: '#3CC8C8', color2: '#7DE3E3' },
		{ ticker: 'doge', name: 'Dogecoin', color: '#C2A633', color2: '#E0C96B' },
		{ ticker: 'trx', name: 'TRON', color: '#FF060A', color2: '#FF5C61' },
		{ ticker: 'ton', name: 'Toncoin', color: '#0098EA', color2: '#4FC3FF' },
		{ ticker: 'avax', name: 'Avalanche', color: '#E84142', color2: '#FF7A7C' },
		{ ticker: 'dot', name: 'Polkadot', color: '#E6007A', color2: '#FF4FA5' },
		{ ticker: 'link', name: 'Chainlink', color: '#2A5ADA', color2: '#6B8CFF' },
		{ ticker: 'matic', name: 'Polygon', color: '#8247E5', color2: '#B38CFF' },
		{ ticker: 'ltc', name: 'Litecoin', color: '#345D9D', color2: '#6F95D1' },
		{ ticker: 'bch', name: 'Bitcoin Cash', color: '#8DC351', color2: '#B6E07D' },
		{ ticker: 'xlm', name: 'Stellar', color: '#08B5E5', color2: '#66D8FF' },
		{ ticker: 'atom', name: 'Cosmos', color: '#6F8CFF', color2: '#A5B7FF' }
	] as const;

	const fallbackButton = { color: '#20D4F9', color2: '#5BE6FF' };

	let selected = $state('xmr');
	let customTicker = $state('');
	let aliasInput = $state('');

	let resolutionSuccess = $state(false);
	let resolutionError = $state(false);
	let resolvedAddress = $state('');
	let errorMessage = $state('');
	let alertToken = $state('');
	let resolvePromise = $state<Promise<string> | null>(null);
	let isResolving = $state(false);

	let selectedColors = $derived(coins.find((coin) => coin.ticker === selected) ?? fallbackButton);

	const makeToken = () => {
		const buf = new Uint32Array(2);
		crypto.getRandomValues(buf);
		return `${buf[0].toString(36)}${buf[1].toString(36)}`;
	};

	const resolve = async () => {
		resolutionSuccess = false;
		resolutionError = false;
		errorMessage = '';
		resolvedAddress = '';
		isResolving = true;
		resolvePromise = resolveAddress(selected == 'other' ? customTicker : selected, aliasInput)
			.then((address) => {
				resolvedAddress = address;
				alertToken = makeToken();
				resolutionSuccess = true;
				isResolving = false;
				return address;
			})
			.catch((err) => {
				errorMessage = err instanceof Error ? err.message : 'Failed to resolve address.';
				resolutionError = true;
				isResolving = false;
				throw err;
			});
	};
</script>

<main>
	<div class="relative flex flex-col items-center justify-center gap-10 px-6 py-16">
		<div class="bg-grid pointer-events-none absolute inset-0 opacity-40"></div>
		<img
			src={logo}
			alt="Cryptalias logo"
			class="relative w-[min(360px,70vw)] drop-shadow-[0_20px_60px_rgba(0,0,0,0.6)]"
		/>

		{#if resolvePromise}
			<div class="glass relative w-full max-w-2xl space-y-6 rounded-card px-6 py-6 sm:px-8">
				{#await resolvePromise}
					<div
						class="relative rounded-xl bg-primary/10 px-4 py-3 text-primary shadow-sm"
						role="status"
					>
						<span class="absolute left-0 top-3 h-[calc(100%-1.5rem)] w-1 rounded-full bg-primary"></span>
						<p class="font-display text-xs tracking-[0.2em] text-primary/90 uppercase">
							Resolving address
						</p>
						<p class="mt-2 text-sm text-text">Please wait…</p>
					</div>
				{:then address}
					<div
						id={`${alertToken}`}
						class={`relative rounded-xl bg-success/10 px-4 py-3 text-success shadow-sm ${alertToken}`}
						role="status"
					>
						<span class="absolute left-0 top-3 h-[calc(100%-1.5rem)] w-1 rounded-full bg-success"></span>
						<p class="font-display text-xs tracking-[0.2em] text-success/90 uppercase">
							Resolved address
						</p>
						<p class="mt-2 font-mono text-sm break-all text-text">{address}</p>
					</div>
				{:catch err}
					<div
						class="relative rounded-xl bg-danger/10 px-4 py-3 text-danger shadow-sm"
						role="alert"
					>
						<span class="absolute left-0 top-3 h-[calc(100%-1.5rem)] w-1 rounded-full bg-danger"></span>
						<p class="font-display text-xs tracking-[0.2em] text-danger/90 uppercase">
							Resolution error
						</p>
						<p class="mt-2 text-sm text-text">
							{err instanceof Error ? err.message : 'Failed to resolve address.'}
						</p>
					</div>
				{/await}
			</div>
		{/if}

		<form
			class="glass relative w-full max-w-2xl space-y-6 rounded-card px-6 py-6 sm:px-8"
			autocomplete="off"
		>
			<div class="space-y-2">
				<label class="block text-sm font-medium text-muted" for="crypto"> Crypto </label>
				<select
					id="crypto"
					name="crypto"
					class="w-full rounded-xl border border-border bg-surface-2/95 px-4 py-3 text-text shadow-sm focus:border-primary focus:ring-2 focus:ring-primary/40"
					bind:value={selected}
					required
				>
					{#each coins as coin}
						<option value={coin.ticker}>{coin.name}</option>
					{/each}
					<option value="other">Other (custom ticker)</option>
				</select>
			</div>

			{#if selected === 'other'}
				<div class="space-y-2">
					<label class="block text-sm font-medium text-muted" for="custom-ticker">
						Custom ticker
					</label>
					<input
						id="custom-ticker"
						name="customTicker"
						class="w-full rounded-xl border border-border bg-surface/80 px-4 py-3 text-text shadow-sm focus:border-primary focus:ring-2 focus:ring-primary/40"
						type="text"
						placeholder="e.g. xyz"
						required={selected === 'other'}
						pattern="^[-A-Za-z0-9.]+$"
						title="Use letters, numbers, dots, or dashes only."
						autocomplete="off"
						inputmode="text"
						bind:value={customTicker}
					/>
				</div>
			{/if}

			<div class="space-y-2">
				<label class="block text-sm font-medium text-muted" for="alias"> Alias </label>
				<input
					id="alias"
					name="alias"
					class="w-full rounded-xl border border-border bg-surface/80 px-4 py-3 text-text shadow-sm focus:border-primary focus:ring-2 focus:ring-primary/40"
					type="text"
					placeholder="donations$cryptalias.xyz"
					required
					pattern="^([A-Za-z0-9.\-]+)(?:\+([A-Za-z0-9.\-]+))?\$([A-Za-z0-9.\-]+)$"
					title="Format: name[+tag]$domain (letters, numbers, dots, dashes)."
					autocomplete="off"
					inputmode="text"
					bind:value={aliasInput}
				/>
			</div>

			<div class="flex justify-center">
				<button
					class="rounded-pill px-8 py-3 text-sm font-semibold tracking-[0.18em] text-white uppercase shadow-lg transition hover:translate-y-px focus:ring-2 focus:ring-offset-0 focus:outline-none"
					style={`background: linear-gradient(135deg, ${selectedColors.color} 0%, ${selectedColors.color2} 100%); box-shadow: 0 16px 40px ${selectedColors.color}55;`}
					type="button"
					onclick={resolve}
					disabled={isResolving}
				>
					{#if isResolving}
						<span class="inline-flex items-center gap-2">
							<span
								class="h-4 w-4 animate-spin rounded-full border-2 border-white/40 border-t-white"
								aria-hidden="true"
							></span>
							Resolving
						</span>
					{:else}
						Resolve
					{/if}
				</button>
			</div>
		</form>
	</div>
</main>
