<template>
    <v-menu
            ref="menu"
            v-model="menu"
            :close-on-content-click="false"
            :return-value.sync="value"
            transition="scale-transition"
            offset-y
            min-width="290px"
    >
        <template v-slot:activator="{ on, attrs }">
            <v-text-field
                    v-model="value"
                    :label="label"
                    readonly
                    v-bind="attrs"
                    v-on="on"
            ></v-text-field>
        </template>
        <v-date-picker v-model="value" no-title scrollable>
            <v-spacer></v-spacer>
            <v-btn text color="primary" @click="menu = false">Cancel</v-btn>
            <v-btn text color="primary" @click="$refs.menu.save(value)">OK</v-btn>
        </v-date-picker>
    </v-menu>
</template>

<script>
    export default {
        props: {
            label: String,
            value: Date,
        },
        data: () => ({
            menu: false,
            modal: false,
            menu2: false,
        }),
    }
</script>

<style scoped>

</style>