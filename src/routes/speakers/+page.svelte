<script>
	import { onMount, tick } from 'svelte';
	import { fade, fly, scale } from 'svelte/transition';
	import { cubicOut } from 'svelte/easing';

	let speakers = [];
	let error = null;
	let loading = true;
	let expandedSpeaker = null;
	let isMobile = false;
	let activeFilter = 'all';
	let searchQuery = '';
	let animationDelay = 0;
	let isGridView = true;
	let appearAnimationComplete = false;

	onMount(async () => {
		try {
			const res = await fetch('/data/speakerData.json');
			if (!res.ok) {
				throw new Error(`Failed to fetch speakers: ${res.status} ${res.statusText}`);
			}
			speakers = await res.json();
			// Add random accent colors to each speaker
			const accentColors = ['#FF5E5B', '#D89CF6', '#45CAFF', '#FF9F1C', '#2DE2E6', '#F6AE2D', '#86BBD8'];
			speakers = speakers.map(speaker => ({
				...speaker,
				accentColor: accentColors[Math.floor(Math.random() * accentColors.length)]
			}));
			console.log('speakerdata:', speakers);
		} catch (err) {
			error = err.message;
			console.error('Error loading speakers:', err);
		} finally {
			loading = false;
			checkScreenWidth();
			window.addEventListener('resize', checkScreenWidth);
			
			// Delay setting animation complete to ensure elements have time to appear
			setTimeout(() => {
				appearAnimationComplete = true;
			}, speakers.length * 100 + 500);
		}
	});

	function checkScreenWidth() {
		isMobile = window.innerWidth < 768;
		if (!isMobile) {
			expandedSpeaker = null;
		}
	}

	function toggleExpand(speaker, event) {
		if (event) event.stopPropagation();
		
		if (isMobile || !isGridView) {
			expandedSpeaker = expandedSpeaker === speaker ? null : speaker;
		}
	}

	function filterSpeakers(filter) {
		activeFilter = filter;
	}

	function toggleView() {
		isGridView = !isGridView;
		expandedSpeaker = null;
	}

	const formatUrl = (url) => (url.startsWith('http') ? url : `https://${url}`);

	$: filteredSpeakers = speakers.filter(speaker => {
		if (searchQuery) {
			const query = searchQuery.toLowerCase();
			return (
				speaker.name.toLowerCase().includes(query) || 
				speaker.organization?.name.toLowerCase().includes(query) ||
				speaker.organization?.role.toLowerCase().includes(query)
			);
		}
		
		if (activeFilter === 'all') return true;
		// This would filter by category if your data has categories
		return speaker.category === activeFilter;
	});
	
	function getAnimationDelay(index) {
		return index * 100;
	}
</script>

<section class="speakers-section">
	<div class="bg-animation">
		<div class="blob blob1"></div>
		<div class="blob blob2"></div>
		<div class="blob blob3"></div>
	</div>
	
	<div class="content-container">
		<header in:fly={{ y: -50, duration: 800, delay: 100 }}>
			<div class="title-container">
				<h1>Meet Our <span class="gradient-text">Speakers</span></h1>
				<p class="subtitle">Industry leaders sharing insights at our event</p>
			</div>
			
			<div class="controls">
				<div class="search-container">
					<input 
						type="text" 
						bind:value={searchQuery} 
						placeholder="Search speakers..." 
						class="search-input"
					/>
					<span class="search-icon">🔍</span>
				</div>
				
				<div class="view-toggle">
					<button 
						class:active={isGridView} 
						on:click={toggleView} 
						aria-label="Grid view"
					>
						<span class="icon">▦</span>
					</button>
					<button 
						class:active={!isGridView} 
						on:click={toggleView} 
						aria-label="List view"
					>
						<span class="icon">☰</span>
					</button>
				</div>
			</div>
		</header>

		{#if loading}
			<div class="loading-container" in:fade={{ duration: 300 }}>
				<div class="spinner"></div>
				<p>Loading amazing speakers...</p>
			</div>
		{:else if error}
			<p class="error" in:fade={{ duration: 300 }}>Error: {error}</p>
		{:else if filteredSpeakers.length === 0}
			<div class="no-results" in:fade={{ duration: 300 }}>
				<p>No speakers found matching your search.</p>
				<button on:click={() => { searchQuery = ''; activeFilter = 'all'; }}>
					Clear filters
				</button>
			</div>
		{:else}
			<div class="speakers-container" class:grid-view={isGridView} class:list-view={!isGridView}>
				{#each filteredSpeakers as speaker, i (speaker.name)}
					<div 
						class="speaker-card"
						class:expanded={expandedSpeaker === speaker}
						style="--accent-color: {speaker.accentColor}; --animation-delay: {getAnimationDelay(i)}ms;"
						in:fly={{ y: 50, duration: 600, delay: getAnimationDelay(i), easing: cubicOut }}
						on:click={(e) => toggleExpand(speaker, e)}
					>
						<div class="card-inner">
							<div class="speaker-image-container">
								<div class="image-frame">
									<img src={speaker.image} alt={speaker.name} loading="lazy" />
									<div class="glow-effect"></div>
								</div>
							</div>
							
							<div class="speaker-details">
								<h2 class="speaker-name">{speaker.name}</h2>
								<p class="speaker-role">{speaker.organization?.role} at {speaker.organization?.name}</p>
								
								{#if !isMobile || expandedSpeaker === speaker || !isGridView}
									<div class="speaker-bio" class:visible={expandedSpeaker === speaker || !isGridView}>
										<div class="quote-container">
											<p class="quote">"{speaker.quote}"</p>
										</div>
										
										<div class="social-links">
											{#if speaker.social?.linkedin}
												<a 
													href={formatUrl(speaker.social.linkedin)} 
													target="_blank" 
													rel="noopener noreferrer"
													aria-label={`LinkedIn profile of ${speaker.name}`}
													class="social-link linkedin"
												>
													<span class="icon">in</span>
												</a>
											{/if}
											{#if speaker.social?.x}
												<a 
													href={formatUrl(speaker.social.x)} 
													target="_blank" 
													rel="noopener noreferrer"
													aria-label={`X (Twitter) profile of ${speaker.name}`}
													class="social-link twitter"
												>
													<span class="icon">𝕏</span>
												</a>
											{/if}
											{#if speaker.social?.github}
												<a 
													href={formatUrl(speaker.social.github)} 
													target="_blank" 
													rel="noopener noreferrer"
													aria-label={`GitHub profile of ${speaker.name}`}
													class="social-link github"
												>
													<span class="icon">⌥</span>
												</a>
											{/if}
											{#if speaker.social?.youtube}
												<a 
													href={formatUrl(speaker.social.youtube)} 
													target="_blank" 
													rel="noopener noreferrer" 
													aria-label={`YouTube channel of ${speaker.name}`}
													class="social-link youtube"
												>
													<span class="icon">▶</span>
												</a>
											{/if}
										</div>
										
										{#if isGridView && (isMobile || expandedSpeaker === speaker)}
											<button class="read-more-btn" on:click|stopPropagation={() => toggleExpand(speaker)}>
												{expandedSpeaker === speaker ? 'Show less' : 'Read more'}
											</button>
										{/if}
									</div>
								{:else}
									<button class="read-more-btn" on:click|stopPropagation={() => toggleExpand(speaker)}>
										Read more
									</button>
								{/if}
							</div>
						</div>
					</div>
				{/each}
			</div>
		{/if}
	</div>
</section>

<style>
	.speakers-section {
		position: relative;
		min-height: 100vh;
		overflow: hidden;
		padding: 6rem 2rem;
		background: linear-gradient(135deg, #050a2d 0%, #0a1a48 50%, #0d2554 100%);
		color: white;
		font-family: 'Montserrat', 'Segoe UI', Tahoma, sans-serif;
	}
	
	.bg-animation {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		overflow: hidden;
		z-index: 0;
		opacity: 0.4;
	}
	
	.blob {
		position: absolute;
		border-radius: 50%;
		filter: blur(80px);
	}
	
	.blob1 {
		width: 40vw;
		height: 40vw;
		background: radial-gradient(circle, rgba(64, 93, 230, 0.6) 0%, rgba(28, 58, 148, 0) 70%);
		top: -10vw;
		left: -10vw;
		animation: float 20s ease-in-out infinite alternate;
	}
	
	.blob2 {
		width: 45vw;
		height: 45vw;
		background: radial-gradient(circle, rgba(86, 204, 242, 0.5) 0%, rgba(45, 109, 194, 0) 70%);
		bottom: -15vw;
		right: -15vw;
		animation: float 25s ease-in-out infinite alternate-reverse;
	}
	
	.blob3 {
		width: 30vw;
		height: 30vw;
		background: radial-gradient(circle, rgba(111, 63, 251, 0.4) 0%, rgba(70, 41, 166, 0) 70%);
		top: 40%;
		left: 60%;
		animation: float 18s ease-in-out infinite alternate;
	}
	
	@keyframes float {
		0% {
			transform: translate(0, 0) scale(1);
		}
		50% {
			transform: translate(5%, 5%) scale(1.1);
		}
		100% {
			transform: translate(-5%, -5%) scale(0.9);
		}
	}
	
	.content-container {
		position: relative;
		z-index: 1;
		max-width: 1400px;
		margin: 0 auto;
	}
	
	header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 4rem;
		flex-wrap: wrap;
		gap: 2rem;
	}
	
	.title-container {
		text-align: left;
	}
	
	h1 {
		font-size: 3.5rem;
		font-weight: 800;
		margin-bottom: 0.5rem;
		letter-spacing: -1px;
	}
	
	.gradient-text {
		background: linear-gradient(to right, #60efff, #0061ff);
		-webkit-background-clip: text;
		background-clip: text;
		color: transparent;
	}
	
	.subtitle {
		font-size: 1.2rem;
		color: #a0aec0;
		margin-top: 0;
	}
	
	.controls {
		display: flex;
		gap: 1rem;
		align-items: center;
	}
	
	.search-container {
		position: relative;
	}
	
	.search-input {
		padding: 0.75rem 1rem 0.75rem 2.5rem;
		border-radius: 2rem;
		border: 2px solid rgba(255, 255, 255, 0.1);
		background: rgba(255, 255, 255, 0.05);
		color: white;
		font-size: 1rem;
		width: 250px;
		transition: all 0.3s ease;
	}
	
	.search-input:focus {
		outline: none;
		border-color: #60efff;
		background: rgba(255, 255, 255, 0.1);
		box-shadow: 0 0 15px rgba(96, 239, 255, 0.3);
	}
	
	.search-input::placeholder {
		color: rgba(255, 255, 255, 0.5);
	}
	
	.search-icon {
		position: absolute;
		left: 0.75rem;
		top: 50%;
		transform: translateY(-50%);
		color: rgba(255, 255, 255, 0.5);
		pointer-events: none;
	}
	
	.view-toggle {
		display: flex;
		background: rgba(255, 255, 255, 0.05);
		border-radius: 1rem;
		overflow: hidden;
		border: 2px solid rgba(255, 255, 255, 0.1);
	}
	
	.view-toggle button {
		background: transparent;
		border: none;
		color: rgba(255, 255, 255, 0.6);
		padding: 0.5rem 0.75rem;
		cursor: pointer;
		font-size: 1.2rem;
		transition: all 0.3s ease;
	}
	
	.view-toggle button.active {
		background: rgba(255, 255, 255, 0.1);
		color: #60efff;
	}
	
	.loading-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 400px;
	}
	
	.spinner {
		width: 50px;
		height: 50px;
		border: 4px solid rgba(255, 255, 255, 0.1);
		border-radius: 50%;
		border-top: 4px solid #60efff;
		animation: spin 1s linear infinite;
		margin-bottom: 1rem;
	}
	
	@keyframes spin {
		0% { transform: rotate(0deg); }
		100% { transform: rotate(360deg); }
	}
	
	.error, .no-results {
		text-align: center;
		padding: 3rem;
		background: rgba(255, 255, 255, 0.05);
		border-radius: 1rem;
		max-width: 600px;
		margin: 0 auto;
		border: 2px solid rgba(255, 0, 0, 0.2);
	}
	
	.no-results button {
		background: rgba(255, 255, 255, 0.1);
		border: none;
		color: white;
		padding: 0.75rem 1.5rem;
		border-radius: 2rem;
		margin-top: 1rem;
		cursor: pointer;
		font-weight: 600;
		transition: all 0.3s ease;
	}
	
	.no-results button:hover {
		background: rgba(255, 255, 255, 0.2);
		transform: translateY(-2px);
	}
	
	.speakers-container {
		display: grid;
		gap: 2.5rem;
		width: 100%;
	}
	
	.speakers-container.grid-view {
		grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
	}
	
	.speakers-container.list-view {
		grid-template-columns: 1fr;
		max-width: 900px;
		margin: 0 auto;
	}
	
	.speaker-card {
		position: relative;
		background: rgba(255, 255, 255, 0.03);
		border-radius: 1.5rem;
		box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
		overflow: hidden;
		transform-style: preserve-3d;
		transition: all 0.5s cubic-bezier(0.23, 1, 0.32, 1);
		cursor: pointer;
		animation-delay: var(--animation-delay);
	}
	
	.speaker-card::before {
		content: "";
		position: absolute;
		inset: 0;
		background: linear-gradient(to bottom right, 
			rgba(var(--accent-color), 0.2), 
			rgba(var(--accent-color), 0)
		);
		opacity: 0;
		transition: opacity 0.5s ease;
		z-index: 0;
	}
	
	.speaker-card::after {
		content: "";
		position: absolute;
		top: -50%;
		left: -50%;
		width: 200%;
		height: 200%;
		background: radial-gradient(
			circle at center,
			rgba(255, 255, 255, 0.15),
			rgba(255, 255, 255, 0) 50%
		);
		opacity: 0;
		transform: translateZ(-10px);
		transition: opacity 0.5s ease;
		pointer-events: none;
	}
	
	.speaker-card:hover {
		transform: translateY(-10px) scale(1.02);
		box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
	}
	
	.speaker-card:hover::before {
		opacity: 1;
	}
	
	.speaker-card:hover::after {
		opacity: 0.5;
	}
	
	.speaker-card.expanded {
		box-shadow: 0 25px 50px rgba(0, 0, 0, 0.4);
	}
	
	.speaker-card::before {
		content: "";
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		height: 5px;
		background: var(--accent-color, #60efff);
		border-radius: 5px 5px 0 0;
	}
	
	.card-inner {
		position: relative;
		z-index: 1;
		display: flex;
		flex-direction: column;
		padding: 2rem;
		height: 100%;
	}
	
	.list-view .card-inner {
		flex-direction: row;
		align-items: center;
		gap: 2rem;
	}
	
	.speaker-image-container {
		margin-bottom: 1.5rem;
		align-self: center;
	}
	
	.list-view .speaker-image-container {
		flex: 0 0 150px;
		margin-bottom: 0;
	}
	
	.image-frame {
		position: relative;
		width: 180px;
		height: 180px;
		border-radius: 50%;
		overflow: hidden;
		background: #121b38;
		border: 4px solid rgba(255, 255, 255, 0.1);
		transition: all 0.3s ease;
	}
	
	.list-view .image-frame {
		width: 130px;
		height: 130px;
	}
	
	.speaker-card:hover .image-frame {
		border-color: var(--accent-color, #60efff);
		transform: scale(1.05);
	}
	
	.image-frame img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		transition: all 0.5s ease;
	}
	
	.speaker-card:hover img {
		transform: scale(1.1);
	}
	
	.glow-effect {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: radial-gradient(
			circle at 50% 50%,
			var(--accent-color, rgba(96, 239, 255, 0.4)),
			transparent 70%
		);
		opacity: 0;
		mix-blend-mode: overlay;
		transition: opacity 0.5s ease;
	}
	
	.speaker-card:hover .glow-effect {
		opacity: 0.7;
	}
	
	.speaker-details {
		flex: 1;
		display: flex;
		flex-direction: column;
	}
	
	.speaker-name {
		font-size: 1.5rem;
		font-weight: 700;
		margin: 0 0 0.5rem;
		color: white;
	}
	
	.speaker-role {
		font-size: 1rem;
		color: rgba(255, 255, 255, 0.7);
		margin: 0 0 1rem;
	}
	
	.speaker-bio {
		opacity: 0;
		max-height: 0;
		overflow: hidden;
		transition: all 0.5s cubic-bezier(0.19, 1, 0.22, 1);
	}
	
	.speaker-bio.visible,
	.list-view .speaker-bio {
		opacity: 1;
		max-height: 500px;
		transition-delay: 0.1s;
	}
	
	.quote-container {
		position: relative;
		margin-bottom: 1.5rem;
		padding-left: 1rem;
	}
	
	.quote-container::before {
		content: '';
		position: absolute;
		left: 0;
		top: 0;
		bottom: 0;
		width: 3px;
		background: var(--accent-color, #60efff);
		border-radius: 3px;
	}
	
	.quote {
		font-style: italic;
		color: rgba(255, 255, 255, 0.8);
		line-height: 1.6;
		margin: 0;
	}
	
	.social-links {
		display: flex;
		gap: 0.75rem;
		margin-top: 1.5rem;
	}
	
	.social-link {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 36px;
		height: 36px;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.08);
		color: white;
		text-decoration: none;
		transition: all 0.3s ease;
		font-size: 0.9rem;
	}
	
	.social-link:hover {
		transform: translateY(-3px);
		box-shadow: 0 5px 10px rgba(0, 0, 0, 0.2);
	}
	
	.social-link.linkedin:hover {
		background: #0077B5;
	}
	
	.social-link.twitter:hover {
		background: #1DA1F2;
	}
	
	.social-link.github:hover {
		background: #333;
	}
	
	.social-link.youtube:hover {
		background: #FF0000;
	}
	
	.read-more-btn {
		align-self: center;
		margin-top: 1.5rem;
		background: rgba(255, 255, 255, 0.08);
		border: none;
		color: var(--accent-color, #60efff);
		padding: 0.5rem 1.25rem;
		border-radius: 2rem;
		cursor: pointer;
		font-weight: 600;
		transition: all 0.3s ease;
	}
	
	.read-more-btn:hover {
		background: rgba(255, 255, 255, 0.15);
		transform: translateY(-2px);
	}
	
	/* Mobile Styles */
	@media (max-width: 768px) {
		.speakers-section {
			padding: 4rem 1rem;
		}
		
		header {
			flex-direction: column;
			align-items: stretch;
			text-align: center;
			margin-bottom: 2rem;
		}
		
		.title-container {
			text-align: center;
		}
		
		h1 {
			font-size: 2.5rem;
		}
		
		.controls {
			flex-direction: column;
		}
		
		.search-input {
			width: 100%;
		}
		
		.speakers-container {
			grid-template-columns: 1fr;
		}
		
		.card-inner {
			padding: 1.5rem;
		}
		
		.image-frame {
			width: 120px;
			height: 120px;
		}
		
		.list-view .card-inner {
			flex-direction: column;
			gap: 1rem;
		}
		
		.list-view .speaker-image-container {
			flex: none;
			margin-bottom: 1rem;
		}
		
		.list-view .image-frame {
			width: 120px;
			height: 120px;
		}
	}
	
	/* Animation Keyframes */
	@keyframes fadeIn {
		from { opacity: 0; }
		to { opacity: 1; }
	}
	
	@keyframes slideIn {
		from { transform: translateY(50px); opacity: 0; }
		to { transform: translateY(0); opacity: 1; }
	}
</style>