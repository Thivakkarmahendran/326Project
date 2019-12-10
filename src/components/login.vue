<template>
    <div id="login">
        <h1>Login</h1>
        <input type="text" name="username" v-model="input.username" placeholder="Username" required/>
        <input type="password" name="password" v-model="input.password" placeholder="Password" required/>
        <button type="button" v-on:click="login()">Login</button>
        <p v-if="invalid">Username/Password is incorrect.</p>
        <p v-if="!filledout">Username/Password is required.</p>
    </div>
</template>

<script>
    export default {
        name: 'Login',
        data() {
            return {
                invalid: false,
                filledout: true,
                input: {
                    username: "",
                    password: ""
                }
            }
        },
        methods: {
            login() {

                this.filledout = true;
                this.invalid = false;

                let password = this.input.password;
                let hashPassword = this.hashCode(password);

                if(this.input.username != "" && this.input.password != "") {
                    if(this.input.username == this.$parent.mockAccount.username && hashPassword == this.$parent.mockAccount.password) {
                        this.$emit("authenticated", true);
                        this.$router.replace({ name: "secure" });
                    } else {
                        console.log("The username and / or password is incorrect");
                        this.invalid = true;
                    }
                } else {
                    console.log("A username and password must be present");
                    this.filledout = false;
                }
            },
            hashCode(s){
                return s.split("").reduce(function(a,b){a=((a<<5)-a)+b.charCodeAt(0);return a&a},0);
            }
        }
    }
</script>

<style scoped>
    #login {
        width: 500px;
        border: 1px solid #CCCCCC;
        margin: 200px auto auto;
        padding: 20px;
        background: #1abc9c;
        color: white;
        font-size: 12px;
    }
    p {
        color: red;
        font-size: small;
    }
</style>
