<template>
<div id="app">
	<!-- This part only displays if the user is authenticated -->
	<div v-if="isAuthenticated">
		<h2>{{userInfo.username}}</h2>
		<button to="/profile">Profile</button>
		<button @click="logout">Signout</button>
	</div>
	<!-- The login option shows if the user is not authenticated -->
	<form @submit.prevent="" v-else class="search">
		<h1>Login</h1>
			<input 
				type="text" 
				required
				v-model.trim="text"
			/>
		<button type="submit" @click="authenticate()">Submit</button>
	</form>

	<!-- The content is in the router view -->
	<div class="router">
		<router-view :isAuthenticated="isAuthenticated" :userInfo="userInfo"/>
	</div>

</div>
</template>

<script>
import pathJoin from 'path.join';
import axios from 'axios';
import auth from './lib/auth';

export default {
	name: 'app',
	data() {
		return {
			text: '',
			isAuthenticated: false,
			userInfo: {
				username: null,
			},
		};
	},
	methods: {

		authenticate() {
			const self = this;
			auth.login(() => {
				self.getUserInfo();
			});
		},

		getUserInfo() {
			const token = auth.getToken();
			const self = this;

			// TODO: CHANGE THIS TO YOUR SERVER
			// In this example, we are getting user info from github
			// If this fails, then our token is bad; we are NOT authenticated and
			// should be logged out

			axios.get(pathJoin('https://api.github.com', 'user'), {
				headers: {
					Authorization: `token ${token}`,
				},
			}).then((resp) => {
				self.isAuthenticated = true;

				// TODO: do stuff here, like setting user info variables
				self.userInfo.username = resp.data.login;
				self.userInfo.avatar = resp.data.avatar_url;
				this.text = ''
			}).catch(() => {
				self.logout();
			});
		},

		logout() {
			this.isAuthenticated = false;
			auth.logout();
		},
	},

	created() {
		this.getUserInfo();
	},
};
</script>

<style lang="scss">
@import './assets/fonts.css';
@import './assets/reset.css';

#app {
	display: flex;
	flex-direction: column;
	// width: 1280px;
	max-width: 1140px;
	// max-height: 100vh;
	height: 100%;
	margin:  auto;
}

* {
	transition: ( all 0.25s cubic-bezier(.53,.01,.35,1.5));

}

form {
	display: flex;
	flex-direction: column;
	width: 400px;
	height: 300px;
	padding: 30px 25px;
	margin: 40px auto;
	border-radius: 40px;
	box-sizing: border-box;
	box-shadow: 0 0 20px -5px rgba(0, 0, 0, .25);
	background-color: white;
	
	h1 {
		color: rgba(255,74,86,1);
		font-weight: 100;
		margin-left: 15px;
		margin-bottom: 35px;
		text-transform: uppercase;
	}
		
	button {
		margin-top: 35px;
		background-color: rgba(255,74,86,1);
		border: 1px solid rgba(255,74,86,1);
		line-height: 0;
		font-size: 17px;
		display: inline-block;
		box-sizing: border-box;
		padding: 20px 15px; 
		border-radius: 60px;
		color: #fff;
		font-weight: 100;
		
		&:hover, &:focus {
			color: white;
			background-color: rgb(231, 65, 76);
			transition: background-color .2s;
		}
	}

	input {		
		position: relative;
		font-size: 17px;
		padding: 10px 15px;
		background-color: none;
		box-sizing: border-box;

		appearance: none;
		border: 1px solid rgba(255,74,86,1);
		width: 100%;
		display: block;
		border-radius: 60px;
		font-weight: 100;
		z-index: 1;

		&::placeholder {
			font-weight: 300;
			color: rgba(175, 47, 47, 0.15);
		}

		&:focus {
			outline: none;
			background: #f2f2f2;
			color: rgb(24, 20, 20);
		}
				
	}
}

button {
	margin: 30px;
	padding: 30px;
	background-color: #c2c2c2;
}

.router {
    padding-top: 40px;
}

</style>
