<template>
    <div id="app">
        <!--    <img alt="Vue logo" src="./assets/logo.png">-->
       <!-- <login/> -->
<!--        <div id="nav">-->
<!--            <router-link to="/login" v-on:click.native="logout()" replace>Logout</router-link>-->
<!--        </div>-->
        <router-view @authenticated="setAuthenticated" />
    </div>
</template>

<script>
    //import login from './components/login.vue'

    export default {
        name: 'app',
        data() {
            return {
                authenticated: false,
                mockAccount: {
                    username: "cs326",
                    password: "password"
                }
            }
        },
        components: {
            //login
        },
        mounted() {
            if(!this.authenticated) {
                this.$router.replace({ name: "login" });
            }
        },
        methods: {
            setAuthenticated(status) {
                this.authenticated = status;
            },
            logout() {
                this.authenticated = false;
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        /*text-align: center;*/
        color: #2c3e50;
        /*margin-top: 0;*/
    }
    body {
        margin: 0;
        padding: 0;
    }
</style>
