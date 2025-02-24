<script>
	import { onMount } from 'svelte';

	let sponsor = [];
	let loading = true;
	let error = null;

	onMount(async () => {
		try {
			const res = await fetch('/data/sponsors.json');
			if (!res.ok) {
				if (res.status === 404) {
					throw new Error('Sponsors file not found.');
				}
				throw new Error(`Failed to fetch sponsors: ${res.status} ${res.statusText}`);
			}
			const data = await res.json();
			sponsor = data;
			console.log('sponsors:', sponsor);
		} catch (err) {
			error = err.message;
			console.error('Error loading sponsors:', err);
		} finally {
			loading = false;
		}
	});

	function checkScreenWidth() {
		if (typeof window !== 'undefined') {
			console.log('Window width:', window.innerWidth);
			// Add your logic based on screen width here
		}
	}

	onMount(() => {
		checkScreenWidth();
		if (typeof window !== 'undefined') {
			window.addEventListener('resize', checkScreenWidth);
		}
		return () => {
			if (typeof window !== 'undefined') {
				window.removeEventListener('resize', checkScreenWidth);
			}
		};
	});
</script>

<section class="sponsors">
	<h1>Our Sponsors</h1>
	{#if loading}
		<p>Loading sponsors...</p>
	{:else if error}
		<p>Error: {error}</p>
	{:else}
		<div class="sponsor-grid">
			{#each sponsor as s}
				<div class="sponsor-item">
					<a href={s.website} target="_blank" rel="noopener noreferrer">
						<img src={s.logo} alt={s.name} class="sponsor-logo" />
					</a>
				</div>
			{/each}
		</div>
	{/if}
</section>

<style>
	.sponsors {
		text-align: center;
		padding: 3rem 2rem;
		background: linear-gradient(45deg, #000428, #004e92);
		color: white;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	}

	.sponsors h1 {
		margin-bottom: 2rem;
		color: white;
	}

	.sponsor-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 20px;
		padding: 20px;
		justify-items: center;
	}

	.sponsor-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		text-align: center;
		padding: 15px;
		margin: auto;
		border-radius: 8px;
		transition:
			transform 0.3s ease,
			box-shadow 0.3s ease;
		background: transparent
	}

	.sponsor-logo {
		max-width: 150px;
		max-height: 80px;
		object-fit: contain;
		margin-bottom: 10px;
	}

	.sponsor-name {
		font-weight: bold;
		margin-bottom: 5px;
		color: #a0aec0;
	}

	.sponsor-link {
		color: #60efff;
		text-decoration: none;
	}

	.sponsor-link:hover {
		text-decoration: underline;
	}

	@media (max-width: 768px) {
		.sponsor-grid {
			grid-template-columns: repeat(4, 1fr); /* 4 columns in mobile view */
			gap: 10px; /* Reduced gap for mobile */
		}

		.sponsor-logo {
			max-width: 80px; /* Reduced logo size */
			max-height: 40px;
		}

		.sponsor-item {
			padding: 5px; /* reduced padding */
		}
	}
</style>
