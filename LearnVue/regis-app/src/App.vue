<template>
    <div id="app">
        <v-app>
            <v-app-bar app color="teal accent-4" dark>
                <v-icon large color="purple">mdi-pig</v-icon>
                <div style="font-size: 1.5em">Register Account</div>
                <v-spacer></v-spacer>
            </v-app-bar>

            <v-main>
                <SignUp />
            </v-main>
        </v-app>
    </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld';
import SignUp from './components/SignUp'

export default {
    name: 'App',

    components: {
        //HelloWorld,
        SignUp,
    },

    data: () => ({}),
}
</script>
