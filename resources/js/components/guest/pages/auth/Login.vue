<template>
    <div>
        <Header></Header>
        <template>
            <div class="min-h-screen flex justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
                <div class="max-w-md w-full space-y-8">
                    <div class="sm:mx-auto sm:w-full sm:max-w-md">
                        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
                            Sign in to your account
                        </h2>
                        <p class="mt-2 text-center text-sm text-gray-600 max-w">
                            Or
                            <router-link
                                :to="{name: 'auth-register'}"
                                class="font-medium text-indigo-600 hover:text-indigo-500"
                            >
                                register here
                            </router-link>.
                        </p>
                    </div>

                    <div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md">
                        <div class="bg-white py-8 px-4 shadow sm:rounded-lg sm:px-10">
                            <Errors :errors="errors"></Errors>
                            <form class="space-y-6" @submit.prevent="authenticate" novalidate>
                                <div>
                                    <label for="email" class="block text-sm font-medium text-gray-700">
                                        Email address
                                    </label>
                                    <div class="mt-1">
                                        <input
                                            id="email"
                                            name="email"
                                            type="email"
                                            autocomplete="email"
                                            placeholder="Email address"
                                            required="required"
                                            v-model="email"
                                            class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"

                                        >
                                    </div>
                                </div>

                                <div>
                                    <label for="password" class="block text-sm font-medium text-gray-700">
                                        Password
                                    </label>
                                    <div class="mt-1">
                                        <input
                                            id="password"
                                            name="password"
                                            type="password"
                                            autocomplete="current-password"
                                            placeholder="Password"
                                            required="required"
                                            v-model="password"
                                            class="appearance-none block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
                                        >

                                    </div>
                                </div>

                                <div class="flex items-center justify-between">
                                    <div class="flex items-center">
                                        <input id="remember_me" name="remember_me" type="checkbox" class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded">
                                        <label for="remember_me" class="ml-2 block text-sm text-gray-900">
                                            Remember me
                                        </label>
                                    </div>

                                    <div class="text-sm">
                                        <a href="#" class="font-medium text-indigo-600 hover:text-indigo-500">
                                            Forgot your password?
                                        </a>
                                    </div>
                                </div>

                                <div>
                                    <button
                                        type="submit"
                                        class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
                                    >Sign in</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </template>
    </div>
</template>

<script>
import Header from "../../layout/Header";
import Login from "../../../../graphql/Login.gql";
import {gqlErrors} from "../../../../utils";
import Errors from "../../../board/Errors";

export default {
    components: {
        Header,
        Errors
    },
    data() {
        return {
            email: null,
            password: null,
            errors: []
        }
    },
    methods: {
        async authenticate() {
            this.errors = [];
            try {
                const response = await this.$apollo.mutate({
                    mutation: Login,
                    variables: {
                        email: this.email,
                        password: this.password
                    }
                });
                const user = response.data?.login;

                if (user) {
                    this.$store.dispatch("setLoggedIn",true);
                    this.$store.commit("setUser", user);
                    this.$router.push({ name: "dashboard" });
                }
            } catch (err) {
                this.errors = gqlErrors(err)
            }
        }
    }
}
</script>
