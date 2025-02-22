<script>
	import '../app.postcss';
	import { AppShell, AppBar, initializeStores, Toast, Modal } from '@skeletonlabs/skeleton';
	import { getSessionCookie } from '$lib/cookies/sessionCookie';
	initializeStores();

	import { onMount } from 'svelte';
	import { get } from 'svelte/store';

	// Check if current URL route is `/login` or `/register`
	let isLoginOrRegisterRoute = true;

	import { themePreference } from '$lib/store/themePreference';
	onMount(() => {
		document.body.setAttribute('data-theme', get(themePreference));

		const currentRoute = window.location.pathname;
		isLoginOrRegisterRoute = currentRoute === '/login' || currentRoute === '/register'
	});

	import Socials from '$lib/components/appBar/Socials.svelte';

	//#region Highlight JS
	import hljs from 'highlight.js/lib/core';
	import 'highlight.js/styles/github-dark.css';
	import { storeHighlightJs } from '@skeletonlabs/skeleton';
	import xml from 'highlight.js/lib/languages/xml'; // for HTML
	import css from 'highlight.js/lib/languages/css';
	import javascript from 'highlight.js/lib/languages/javascript';
	import typescript from 'highlight.js/lib/languages/typescript';

	hljs.registerLanguage('xml', xml); // for HTML
	hljs.registerLanguage('css', css);
	hljs.registerLanguage('javascript', javascript);
	hljs.registerLanguage('typescript', typescript);
	storeHighlightJs.set(hljs);
	//#endregion

	//#region Floating UI for Popups
	import { computePosition, autoUpdate, flip, shift, offset, arrow } from '@floating-ui/dom';
	import { storePopup } from '@skeletonlabs/skeleton';

	storePopup.set({ computePosition, autoUpdate, flip, shift, offset, arrow });
	//#endregion

	import ThemeSelector from '$lib/components/appBar/ThemeSelector.svelte';
	import AuthenticationSelector from '$lib/components/appBar/AuthenticationSelector.svelte';
	import UserInfoHeader from '$lib/components/appBar/UserInfoHeader.svelte';

	const sessionToken = getSessionCookie();

	import ChangePasswordModal from '$lib/components/modals/ChangePasswordModal.svelte';
	import ChangeEmailModal from '$lib/components/modals/ChangeEmailModal.svelte';
	const modalRegistry = {
		changePasswordModal: { ref: ChangePasswordModal },
		changeEmailModal: {ref : ChangeEmailModal }
	};
</script>

<Modal components={modalRegistry} />
<Toast />

<!-- App Shell -->
<AppShell>
	<svelte:fragment slot="header">
		<!-- App Bar -->
		<AppBar gridColumns="grid-cols-3" slotDefault="place-self-center" slotTrail="place-content-end">
			<svelte:fragment slot="lead">
				<a href="../" class="h3-link">
					<h3 class="h3 ml-auto">Muimi</h3>
				</a>
			</svelte:fragment>

			{#if !isLoginOrRegisterRoute}
				<!--A simple login/register button if not registered-->
				{#if sessionToken === undefined}
					<AuthenticationSelector />
				{/if}
			{/if}

			<svelte:fragment slot="trail">
				<div class="mr-auto">
					<Socials />
					<ThemeSelector />
				</div>
					{#if sessionToken !== undefined}
						<UserInfoHeader/>
					{/if}
			</svelte:fragment>
		</AppBar>
	</svelte:fragment>
	<!-- Page Route Content -->
	<slot />
</AppShell>
