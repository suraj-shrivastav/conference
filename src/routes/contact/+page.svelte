<script>
	let name = '';
	let email = '';
	let message = '';

	function handleSubmit() {
		// Here you would typically handle form submission, e.g., send an email
		console.log('Form submitted:', { name, email, message });
		// Reset form fields
		name = '';
		email = '';
		message = '';
		alert('Thank you for your message!'); // Replace with better feedback
	}

	const socialLinks = {
		linkedin: 'https://www.linkedin.com/your-page', // Replace with your links
		twitter: 'https://twitter.com/your-page',
		github: 'https://github.com/your-page',
		youtube: 'https://www.youtube.com/your-channel'
	};
</script>

<section class="contact">
	<h1>Contact Us</h1>
	<div class="contact-container">
		<div class="contact-form">
			<form on:submit|preventDefault={handleSubmit}>
				<div class="form-group">
					<label for="name">Name</label>
					<input type="text" id="name" bind:value={name} required />
				</div>
				<div class="form-group">
					<label for="email">Email</label>
					<input type="email" id="email" bind:value={email} required />
				</div>
				<div class="form-group">
					<label for="message">Message</label>
					<textarea id="message" bind:value={message} required></textarea>
				</div>
				<button type="submit">Send Message</button>
			</form>
		</div>

		<div class="social-links">
			<h2>Connect with Us</h2>
			<div class="social-icons">
				{#if socialLinks.linkedin}
					<a
						href={socialLinks.linkedin}
						target="_blank"
						rel="noopener noreferrer"
						aria-label="LinkedIn"
					>
						<i class="fab fa-linkedin"></i>
					</a>
				{/if}
				{#if socialLinks.twitter}
					<a
						href={socialLinks.twitter}
						target="_blank"
						rel="noopener noreferrer"
						aria-label="Twitter"
					>
						<i class="fab fa-twitter"></i>
					</a>
				{/if}
				{#if socialLinks.github}
					<a
						href={socialLinks.github}
						target="_blank"
						rel="noopener noreferrer"
						aria-label="GitHub"
					>
						<i class="fab fa-github"></i>
					</a>
				{/if}
				{#if socialLinks.youtube}
					<a
						href={socialLinks.youtube}
						target="_blank"
						rel="noopener noreferrer"
						aria-label="YouTube"
					>
						<i class="fab fa-youtube"></i>
					</a>
				{/if}
			</div>
		</div>
	</div>
</section>

<style>
	.contact {
		padding: 4rem 2rem;
		margin: 0 auto;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
		color: white; /* Matching Hero Text Color */
		line-height: 1.6;
		background: linear-gradient(45deg, #000428, #004e92); /* Matching Hero Gradient */
	}

	.contact h1 {
		text-align: center;
		margin-bottom: 2rem;
		color: #60efff; /* Lighter color */
	}

	.contact-container {
		display: grid;
		grid-template-columns: 1fr;
		gap: 2rem;
        width: 100%;
	}

	.contact-form form {
		display: flex;
		flex-direction: column;
	}

	.form-group {
		display: flex;
		flex-direction: column;
		margin-bottom: 1rem;
	}

	.form-group label {
		margin-bottom: 0.5rem;
		font-weight: 600;
		color: #a0aec0;
	}

	.form-group input,
	.form-group textarea {
		padding: 0.75rem;
		border: 1px solid rgba(255, 255, 255, 0.2);
		border-radius: 6px;
		font-size: 1rem;
		background: rgba(255, 255, 255, 0.1);
		color: white;
	}

	.form-group textarea {
		resize: vertical;
	}

	.contact-form button {
		padding: 1rem 1.5rem;
		background-color: #60efff; /* Lighter color */
		color: #000428;
		border: none;
		border-radius: 6px;
		cursor: pointer;
		font-size: 1rem;
		transition: background-color 0.3s ease;
	}

	.contact-form button:hover {
		background-color: #00b8d4; /* Slightly different hover color */
	}

	.social-links {
		text-align: center;
	}

	.social-links h2 {
		margin-bottom: 1rem;
		color: #a0aec0;
	}

	.social-icons {
		display: flex;
		justify-content: center;
		gap: 1rem;
	}

	.social-icons a {
		color: #60efff; /* Lighter color */
		font-size: 1.5rem;
		transition: color 0.3s ease;
	}

	.social-icons a:hover {
		color: #00b8d4; /* Slightly different hover color */
	}

	@media (max-width: 768px) {
		.contact-container {
			grid-template-columns: 1fr;
		}
	}
</style>
