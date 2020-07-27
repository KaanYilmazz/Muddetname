<template>
    <v-app>
        <v-app-bar app color="primary" dark>
            <div class="d-flex align-center">
                <h2>ZİRVE BİLGİSAYAR</h2>
            </div>

            <v-spacer></v-spacer>

            <v-btn
                    href="https://github.com/vuetifyjs/vuetify/releases/latest"
                    target="_blank"
                    text
            >
            </v-btn>
        </v-app-bar>

        <v-main>
            <Home></Home>
        </v-main>
    </v-app>
</template>

<script>


    import Home from "./views/Home";

    export default {
        name: "App",

        components: {
            Home

        },

        data: () => ({
            //
        })
    };
</script>
