<script>
	import Navbar from '$lib/components/+navbar.svelte';
	import Hero from './hero/+page.svelte';
	import Speaker from './speakers/+page.svelte';
	import Schedule from './schedule/+page.svelte';
	import Sponsors from './sponsors/+page.svelte';
	import About from './about/+page.svelte';
	import Contact from './contact/+page.svelte';
</script>

<!-- <Navbar /> -->
<Hero />
<Speaker />
<Schedule />
<Sponsors />
<About />
<Contact />

<slot />
