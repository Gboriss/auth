<template>
<div class="container">
	<ul v-if="isAuthenticated">
		<li v-for="repo in repos" :key="repo.id">
			<h2>{{ repo.owner.login }} / {{ repo.name }}</h2>
			<div><span>&#9733;</span>{{ repo.stargazers_count }}</div>
		</li>
	</ul>
    <div class="submit-error" v-else>
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

<style lang="scss" scoped>
.container {
    display: flex;
    flex-direction: column;
	max-width: 1140px;
	height: 100%;
	margin:  auto;
}

.submit-error {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 0 auto;
}

ul {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin: 0 auto;
    li {
        margin-bottom: 20px;
        div {
            display: flex;
            align-items: center;
        }
    }
}

span {
    color: rgb(255, 217, 0);
    font-size: 25px;
    margin-right: 10px;
}

</style>