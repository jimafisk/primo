<script lang="ts">
	import find from 'lodash/find'
  import { Router, Link, Route } from "svelte-routing";
	import { ax, updateDataToCurrentVersion } from './utils'
	import { onMount, createEventDispatcher, setContext } from 'svelte'
	import Page from './screens/Page/Page.svelte'
  import Modal from './@modal'

	const dispatch = createEventDispatcher()

	import {domainInfo, site, symbols, tailwind, pageData, allSites} from './@stores/data'
	import {content,pageId} from './@stores/data/page'
  import {modal,editorViewDev, userRole} from './@stores/app'

	export let data
	export let functions
	export let sites = []
	export let showDashboardLink = false
	export let role = 'developer'

	setContext('functions', functions)
	setContext('showDashboardLink', showDashboardLink)

	$: setContext('sites', sites)
	$: $editorViewDev = (role === 'developer') ? true : false
	$: $userRole = role

	$: dispatch('save', $site)

	$: allSites.set(sites)

	$: site.update(s => ({
		...s,
		...data
	}))

	$: {
		const currentPage = find($site.pages, ['id', $pageId || 'index'])
		pageData.update(s => ({
			...s, 
			...currentPage
		}))

		tailwind.setInitial()
	}

</script>


<Router>
	<Route path="/:pageId" let:params>
		<Page pageId={params.pageId} on:build on:signOut />
	</Route>
	<Route>
		<Page pageId={'index'} on:build on:signOut />
	</Route>
</Router>

<Modal />

<style type="scss" global>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
</style>