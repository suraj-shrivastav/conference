<script>
	import { onMount } from 'svelte';

	let speakers = [];
	let error = null;
	let loading = true;
	let expandedSpeaker = null;
	let isMobile = false;

	onMount(async () => {
		try {
			const res = await fetch('/src/lib/data/speakerData.json');
			if (!res.ok) {
				throw new Error(`Failed to fetch speakers: ${res.status} ${res.statusText}`);
			}
			speakers = await res.json();
			console.log('speakerdata:', speakers);
		} catch (err) {
			error = err.message;
			console.error('Error loading speakers:', err);
		} finally {
			loading = false;
			checkScreenWidth();
			window.addEventListener('resize', checkScreenWidth);
		}
	});

	function checkScreenWidth() {
		isMobile = window.innerWidth < 768;
		if (!isMobile) {
			expandedSpeaker = null;
		}
	}

	function toggleExpand(speaker) {
		if (isMobile) {
			expandedSpeaker = expandedSpeaker === speaker ? null : speaker;
		}
	}

	const formatUrl = (url) => (url.startsWith('http') ? url : `https://${url}`);
</script>

<section class="speakers">
	<h1>Meet Our Speakers</h1>

	{#if loading}
		<p class="loading">Loading speakers...</p>
	{:else if error}
		<p class="error">Error: {error}</p>
	{:else if speakers.length === 0}
		<p class="no-speakers">No speakers found.</p>
	{:else}
		<div class="grid">
			{#each speakers as speaker (speaker.name)}
				<div
					class="card"
					class:expanded={isMobile && expandedSpeaker === speaker}
					on:click={() => toggleExpand(speaker)}
				>
					<img src={speaker.image} alt={speaker.name} />
					<h2>{speaker.name}</h2>
					<h4>{speaker.organization?.role}</h4>
					{#if !isMobile || expandedSpeaker === speaker}
						<p class="role"><b>{speaker.organization?.role}</b> at {speaker.organization?.name}</p>
						<p class="quote">"{speaker.quote}"</p>
						<div class="social-links">
							{#if speaker.social?.linkedin}
								<a
									href={formatUrl(speaker.social.linkedin)}
									target="_blank"
									aria-label={`LinkedIn profile of ${speaker.name}`}
									class="social-link">LinkedIn</a
								>
							{/if}
							{#if speaker.social?.x}
								<a
									href={formatUrl(speaker.social.x)}
									target="_blank"
									aria-label={`X (Twitter) profile of ${speaker.name}`}
									class="social-link">X (Twitter)</a
								>
							{/if}
							{#if speaker.social?.github}
								<a
									href={formatUrl(speaker.social.github)}
									target="_blank"
									aria-label={`GitHub profile of ${speaker.name}`}
									class="social-link">GitHub</a
								>
							{/if}
							{#if speaker.social?.youtube}
								<a
									href={formatUrl(speaker.social.youtube)}
									target="_blank"
									aria-label={`YouTube channel of ${speaker.name}`}
									class="social-link">YouTube</a
								>
							{/if}
						</div>
					{/if}
				</div>
			{/each}
		</div>
	{/if}
</section>

<style>
	.speakers {
		text-align: center;
		padding: 3rem 2rem;
		background: linear-gradient(45deg, #000428, #004e92); /* Matching Hero Gradient */
		color: white; /* Matching Hero Text Color */
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	}

	.speakers h1 {
		margin-bottom: 2rem;
		color: white; /* Matching Hero Text Color */
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 2rem;
		padding: 2rem;
		max-width: 1200px;
		margin: 0 auto;
	}

	.card {
		background: rgba(255, 255, 255, 0.1); /* Slightly transparent white for session boxes */
		border-radius: 12px;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
		margin-bottom: 1rem;
		transition:
			transform 0.3s ease,
			box-shadow 0.3s ease;
		cursor: pointer;
		color: white;
	}

	.card:hover {
		transform: translateY(-5px);
		box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
	}

	img {
		margin: 0 auto;
		border-radius: 50%;
		width: 180px;
		height: 180px;
		object-fit: cover;
		margin-bottom: 1rem;
	}

	.role {
		font-weight: 600;
		color: #a0aec0;
		margin-bottom: 0.5rem;
	}

	.quote {
		font-style: italic;
		color: #a0aec0;
		margin-bottom: 1rem;
	}

	.social-links {
		margin-top: 1rem;
	}

	.social-link {
		margin: 0 5px;
		text-decoration: none;
		color: #60efff;
	}

	.social-link:hover {
		text-decoration: underline;
	}

	.error,
	.no-speakers,
	.loading {
		color: #d32f2f;
		font-weight: 600;
		margin-top: 2rem;
	}

	/* Mobile styles */
	@media (max-width: 767px) {
		.card {
			padding: 1rem;
		}

		.card:not(.expanded) p,
		.card:not(.expanded) .social-links {
			display: none;
		}

		img {
			width: 100px;
			height: 100px;
		}

		.grid {
			grid-template-columns: 1fr;
		}
	}
</style>
