<script>
	import { onMount } from 'svelte';

	let aboutContent = {
		title: 'About TechNext 2025',
		sections: [
			{
				heading: 'Our Vision',
				content:
					"TechNext 2025 is more than just a conference; it's a hub for innovation, collaboration, and forward-thinking. We envision a future where technology empowers individuals and transforms industries. Our goal is to bring together the brightest minds to explore emerging trends, share groundbreaking ideas, and forge lasting connections."
			},
			{
				heading: 'What We Offer',
				content:
					"At TechNext 2025, you can expect an immersive experience filled with insightful keynotes, hands-on workshops, and engaging panel discussions. Our carefully curated agenda covers a wide range of topics, including artificial intelligence, blockchain, cybersecurity, sustainable tech, and more. You'll have the opportunity to learn from industry experts, network with peers, and discover cutting-edge technologies."
			},
			{
				heading: 'Our Commitment',
				content:
					'We are committed to creating an inclusive and accessible environment for all attendees. We strive to provide a platform where diverse perspectives are valued and celebrated. Our conference is designed to foster a sense of community and promote meaningful dialogue. We are also dedicated to sustainability and are implementing eco-friendly practices throughout the event.'
			},
			{
				heading: 'Join Us',
				content:
					"Join us at TechNext 2025 and be part of the movement shaping the future of technology. Whether you're a seasoned professional, a budding entrepreneur, or a passionate enthusiast, you'll find inspiration and opportunities to grow. We look forward to welcoming you to San Francisco!"
			}
		]
	};
</script>

<section class="about">
	<h1>{aboutContent.title}</h1>
	{#each aboutContent.sections as section}
		<div class="section">
			<h2>{section.heading}</h2>
			<p>{section.content}</p>
		</div>
	{/each}
</section>

<style>
	.about {
		padding: 4rem 2rem;
		margin: 0 auto;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
		color: white; /* Matching Hero Text Color */
		line-height: 1.6;
		background: linear-gradient(45deg, #000428, #004e92); /* Matching Hero Gradient */
	}

	.about h1 {
		text-align: center;
		margin-bottom: 2rem;
		color: #60efff; /* Lighter color */
	}

	.section {
		margin-bottom: 2.5rem;
	}

	.section h2 {
		margin-bottom: 1rem;
		color: #a0aec0; /* Lighter color */
	}

	.section p {
		color: #a0aec0; /* Lighter color */
	}

	.loading,
	.error {
		text-align: center;
		margin-top: 2rem;
		color: #d32f2f;
		font-weight: 600;
	}
</style>
