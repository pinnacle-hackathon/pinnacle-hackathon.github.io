<script lang="ts">
	import { onMount } from "svelte";
	import CarouselInner from "./CarouselInner.svelte";
	import type { Hackathon } from "../core/schema/hackathon.schema";

	//fetch API root
	import { stores } from '@sapper/app';
	const { session } = stores();
	const { API_ROOT } = $session;

	let futureHackathons: Hackathon[] = [];
	let hackathons: Hackathon[] = [];

	onMount(() => {
		fetch(API_ROOT+"/hackathons")
			.then(res => res.json())
			.then(res => hackathons = res.results.filter((h: Hackathon) => h.isVisible !== false))
			.then(() => hackathons = hackathons.sort((a, b) =>
				{ return a.internal_title.localeCompare(b.internal_title); }))
			.then(() => {
				futureHackathons = hackathons.filter(h => new Date(h.startDate) > new Date());
				hackathons = hackathons.filter(h => new Date(h.startDate) < new Date())
			})
			.catch(ex => console.log("GET hackathons failed: "+ex));
	});
</script>

<section class="container-wide component-section-large light-bg" id="carousel">
	<div class="container inner flex-column">
		<div class="header-group">
			<h2>Partnered Events</h2>
			<p>The winners of our partnered hackathons qualify for our premiere event.</p>
		</div>
		<CarouselInner futureHackathons="{futureHackathons}" hackathons="{hackathons}" />
		<p>Check out our <a href="/hackathons">full list</a> of partnered events.</p>
	</div>
</section>

<style lang="scss">
	#carousel {
		.inner {
			max-width: 90%;
		}

		h2 {
			text-align: center;
		}

		p {
			text-align: center;
			font-size: 1.3rem;
		}
	}
</style>
