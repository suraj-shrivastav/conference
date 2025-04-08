<script>
	// @ts-nocheck

	import { onMount } from 'svelte';

	let schedule = [];
	let error = null;
	let loading = true;
	let expandedIndex = null;
	let isMobile = false;

	let countdown = 'Loading...';
	let h1Text = '🚀 TechNext 2025 Schedule';
	let subtitle = 'Plan your conference experience.';
	let displayedText = '';
	let displayedSubtitle = '';
	let index = 0;
	let subIndex = 0;

	function typeWriter() {
		if (index < h1Text.length) {
			displayedText += h1Text.charAt(index);
			index++;
			setTimeout(typeWriter, 100);
		} else if (subIndex < subtitle.length) {
			displayedSubtitle += subtitle.charAt(subIndex);
			subIndex++;
			setTimeout(typeWriter, 50);
		}
	}

	function updateCountdown() {
		const eventDate = new Date('2025-03-15T09:00:00').getTime();
		setInterval(() => {
			const now = new Date().getTime();
			const difference = eventDate - now;
			const days = Math.floor(difference / (1000 * 60 * 60 * 24));
			const hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
			const minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
			countdown = `${days}d : ${hours}h : ${minutes}m`;
		}, 1000);
	}

	onMount(async () => {
		typeWriter();
		updateCountdown();

		try {
			const res = await fetch('/data/schedule.json');
			if (!res.ok) {
				throw new Error(`Failed to fetch schedule: ${res.status} ${res.statusText}`);
			}
			const data = await res.json();
			schedule = data;
			console.log('schedule:', schedule);
		} catch (err) {
			error = err.message;
			console.error('Error loading schedule:', err);
		} finally {
			loading = false;
			checkScreenWidth();
			window.addEventListener('resize', checkScreenWidth);
		}
	});

	function checkScreenWidth() {
		isMobile = window.innerWidth < 768;
		if (!isMobile) {
			expandedIndex = null;
		}
	}

	function toggleExpand(index) {
		if (isMobile) {
			expandedIndex = expandedIndex === index ? null : index;
		}
	}
</script>

<section class="schedule-hero">
	<div class="gradient-bg"></div>
	<div class="schedule-content">
		<div class="schedule-text-content">
			<h1>{displayedText}</h1>
			<h2>{displayedSubtitle}</h2>
			<div class="countdown-container">
				<div class="countdown-box">
					<span class="countdown-text">Time Remaining</span>
					<span class="countdown-timer">{countdown}</span>
				</div>
			</div>
		</div>
	</div>
</section>

<section class="schedule">
	<h1>Conference Schedule</h1>

	{#if loading}
		<p class="loading">Loading schedule...</p>
	{:else if error}
		<p class="error">Error: {error}</p>
	{:else if schedule && schedule.length > 0}
		<div class="schedule-table">
			{#each schedule as session, index}
				<div
					class="session"
					class:expanded={isMobile && expandedIndex === index}
					on:click={() => toggleExpand(index)}
				>
					<div class="session-header">
						<span class="topic">{session.topic}</span>
						{#if isMobile}
							<span class="expand-icon">{expandedIndex === index ? '▲' : '▼'}</span>
						{/if}
					</div>
					{#if !isMobile || expandedIndex === index}
						<div class="session-details">
							<p><strong>Date:</strong> {session.date}</p>
							<p><strong>Time:</strong> {session.time}</p>
							<p><strong>Speaker:</strong> {session.speaker}</p>
						</div>
					{/if}
				</div>
			{/each}
		</div>
	{:else}
		<p class="no-schedule">No conference schedule found.</p>
	{/if}
</section>

<style>
	.schedule-hero {
		min-height: 50vh;
		position: relative;
		overflow: hidden;
		background: #0a0a0a;
		color: white;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.gradient-bg {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: linear-gradient(45deg, #000428, #004e92);
		opacity: 0.8;
	}

	.schedule-content {
		position: relative;
		text-align: center;
	}

	.schedule-text-content h1 {
		font-size: 3rem;
		font-weight: 800;
		margin: 0;
		background: linear-gradient(120deg, #00ff87 0%, #60efff 100%);
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
	}

	.schedule-text-content h2 {
		font-size: 1.5rem;
		color: #60efff;
		margin: 1rem 0;
		font-weight: 400;
	}

	.countdown-container {
		margin: 2rem 0;
	}

	.countdown-box {
		background: rgba(255, 255, 255, 0.1);
		padding: 1rem 2rem;
		border-radius: 12px;
		backdrop-filter: blur(10px);
		display: inline-flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	.countdown-text {
		font-size: 0.9rem;
		color: #60efff;
	}

	.countdown-timer {
		font-size: 2rem;
		font-weight: 700;
		color: white;
	}

	.schedule {
		text-align: center;
		padding: 3rem 2rem;
		background: linear-gradient(45deg, #000428, #004e92); /* Matching Hero Gradient */
		color: white; /* Matching Hero Text Color */
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	}

	.schedule h1 {
		margin-bottom: 2rem;
		color: white; /* Matching Hero Text Color */
	}

	.schedule-table {
		max-width: 800px;
		margin: 0 auto;
	}

	.session {
		background: rgba(255, 255, 255, 0.1); /* Slightly transparent white for session boxes */
		border-radius: 12px;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
		margin-bottom: 1rem;
		overflow: hidden;
		transition: box-shadow 0.3s ease;
		color: white;
	}

	.session:hover {
		box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
	}

	.session-header {
		padding: 1.5rem;
		display: flex;
		justify-content: space-between;
		align-items: center;
		cursor: pointer;
		background: rgba(255, 255, 255, 0.05); /* Slightly darker transparent white */
		border-bottom: 1px solid rgba(255, 255, 255, 0.1); /* Light white
        border-bottom: 1px solid rgba(255, 255, 255, 0.1); /* Light white border */
	}

	.topic {
		font-weight: 600;
		color: white;
	}

	.expand-icon {
		font-size: 1.2rem;
		color: #60efff;
	}

	.session-details {
		padding: 1.5rem;
		text-align: left;
		background: rgba(255, 255, 255, 0.05);
		border-top: 1px solid rgba(255, 255, 255, 0.1);
	}

	.session-details p {
		margin-bottom: 0.5rem;
		color: #a0aec0; /* Lighter text color */
	}

	.session-details strong {
		color: white;
	}

	/* Mobile styles */
	@media (max-width: 767px) {
		.session.expanded {
			background-color: rgba(
				255,
				255,
				255,
				0.15
			); /* Slightly brighter background on mobile expansion */
		}
	}
</style>
