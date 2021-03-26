<template>
<div>
	<ul v-if="isAuthenticated">
		<li v-for="repo in repos" :key="repo.id">
			<!-- <img :src=" require('../assets/${repo.owner.avatar_url}') "> -->
			<!-- <img :src=" require('../assets/' + repo.owner.avatar_url) "> -->
			<h2>{{ repo.owner.login }} / {{ repo.name }}</h2>
			<div>{{ repo.stargazers_count }}</div>
		</li>
	</ul>
    <div v-else>
		<h1>Error!</h1>
		<p>
			Please log in to see your profile
		</p>
    </div>
</div>  
</template>

<script>
import axios from 'axios'

export default {
	name: 'user',
	data () {
		return {
			repos: null,
			interval: null	
		}
	},
    props: ['isAuthenticated', 'userInfo'],

	created () {
		this.refreshData()
	},
	beforeDestroy () {
		clearInterval(this.interval)
	},
	methods: {
		refreshData () {
			// console.log('dd')
			axios.get("https://api.github.com/users/" + this.userInfo.username +"/repos")
				.then((response) => {
					this.repos = response.data
					setTimeout(this.refreshData, 10000)
				})
				.catch(error => {
					console.log(error)
				})
		}
	}
}
</script>

<style>

</style>