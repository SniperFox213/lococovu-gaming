<script>
	// Importing modules
	import { onMount } from "svelte";
	import { fade } from "svelte/transition";

	import { goto, stores } from "@sapper/app";
	const { page } = stores();

	import Cookie from "cookie-universal";
	const cookies = Cookie();

	import profile from "../stores/profile.js";
	import accounts from "../stores/accounts.js";

	let loaded = false;

	onMount(() => {
		if ($profile.id == null) {
			// Let's now check if we have
			// any logged in user.
			if (cookies.get('token')) {
				// And now let's try to load this user
				// into our temporary datastore
				profile.loadProfile(cookies.get('token'))
				.then(() => {
					loaded = true;
				}).catch((error) => {
					if (error.error == "authorizePincode") {
						if ($page.path.includes('authorize')) {
							loaded = true;
						} else {
							goto(`https://lococovu.me/authorize/pincode?type=confirmation&query=${encodeURIComponent('?type=updateSecurityCode')}&token=${cookies.get('token')}&return=${encodeURIComponent('https://gaming.lococovu.me/authorize')}`);
						};
					} else {
						loaded = true;
					};
				});
			} else {
				loaded = true;
			};
		} else {
			loaded = true;
		};

		// Let's check if we have any other accounts
		// (and let's load their information if they exists)
		if (cookies.get('tokens')) {
			let token  = cookies.get('token');
			let tokens = cookies.get('tokens').split(',');

			// Performing some actions (i dunno how to name them)
			if (tokens == null) {
				if (cookies.get('token')) {
					tokens = [cookies.get('token')];
				};
			} else {
				if (!tokens.includes(token)) {
					tokens.push(token);
				};
			};

			accounts.loadTokens(tokens == null ? [] : tokens);
		} else {
			if (cookies.get('token')) {
				accounts.loadTokens([cookies.get('token')]);
			};
		}
	});
</script>

<svelte:head>
	<link rel="stylesheet" href="./animations.css">
</svelte:head>

{ #if !loaded}
	<div style="z-index: 999;" out:fade class="absolute bg-container w-full h-screen flex justify-center items-center">
		<!-- Logotype -->
		<div style="animation: pulse 1.5s infinite ease-in-out;" class="w-full md:w-2/3 lg:w-1/3 flex justify-center items-center">
			<img id="logotype" class="w-1/12" src="./logotype/small-white.svg" alt="Lococovu Logotype">
		</div>
	</div>
{ :else }
	<main id="container" class="w-full min-h-screen bg-container">
		<slot />
	</main>
{ /if }