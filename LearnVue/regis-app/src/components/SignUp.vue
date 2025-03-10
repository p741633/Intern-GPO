<template>
    <v-container class="my-5">
        <v-layout column>
            <v-flex>
                <v-alert v-model="alert" border="left" close-text="Close Alert" type="error" dismissible>
                    <span v-html="errMessage"></span>
                </v-alert>

                <v-tabs v-model="tab" show-arrows background-color="blue accent-4" icons-and-text dark grow>
                    <v-tabs-slider color="blue darken-4"></v-tabs-slider>
                    <v-tab v-for="i in tabs" :key="i">
                        <v-icon large>{{ i.icon }}</v-icon>
                        <div class="caption py-1">{{ i.name }}</div>
                    </v-tab>

                    <v-tab-item>
                        <v-form ref="registerForm" v-model="valid" :lazy-validation="lazy">
                            <v-layout column>
                                <v-row>
                                    <v-col cols="12" sm="6" md="4">
                                        <v-flex>
                                            <v-text-field
                                                v-model="form.fname"
                                                :rules="[rules.required]"
                                                name="firstname"
                                                label="First name"
                                                type="firstname"
                                                maxlength="20"
                                                required
                                                ref="fname"
                                            ></v-text-field>
                                        </v-flex>
                                    </v-col>

                                    <v-col cols="12" sm="6" md="4">
                                        <v-flex>
                                            <v-text-field
                                                v-model="form.lname"
                                                :rules="[rules.required]"
                                                name="lastname"
                                                label="Last name"
                                                type="lastname"
                                                maxlength="20"
                                                required
                                            ></v-text-field>
                                        </v-flex>
                                    </v-col>
                                </v-row>

                                <v-select
                                    :rules="[rules.required]"
                                    :items="gender"
                                    :menu-props="{ top: true, offsetY: true }"
                                    label="Gender"
                                    v-model="form.gender"
                                ></v-select>

                                <v-menu
                                    :close-on-content-click="false"
                                    :nudge-right="40"
                                    transition="scale-transition"
                                    offset-y
                                    min-width="auto"
                                >
                                    <template v-slot:activator="{ on, attrs }">
                                        <v-text-field
                                            v-model="form.birthday"
                                            :rules="[rules.required]"
                                            label="Birthday"
                                            prepend-icon="mdi-calendar"
                                            readonly
                                            v-bind="attrs"
                                            v-on="on"
                                        ></v-text-field>
                                    </template>
                                    <v-date-picker v-model="form.birthday" @input="menu2 = false"></v-date-picker>
                                </v-menu>

                                <v-flex cols="12" sm="6" md="4">
                                    <v-text-field
                                        v-model="form.email"
                                        :rules="emailRules"
                                        name="email"
                                        label="Email"
                                        type="email"
                                        required
                                    ></v-text-field>
                                </v-flex>

                                <v-flex>
                                    <v-text-field
                                        v-model="form.username"
                                        :rules="[rules.required]"
                                        name="username"
                                        label="Username"
                                        type="username"
                                        required
                                    ></v-text-field>
                                </v-flex>

                                <v-flex>
                                    <v-text-field
                                        v-model="form.password"
                                        name="password"
                                        label="Password"
                                        :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                                        :rules="[rules.required, rules.min]"
                                        :type="show1 ? 'text' : 'password'"
                                        hint="At least 8 characters"
                                        counter
                                        @click:append="show1 = !show1"
                                        required
                                    ></v-text-field>
                                </v-flex>

                                <v-col class="d-flex ml-auto" cols="12" sm="3" xsm="12">
                                    <v-flex class="text-xs-center">
                                        <v-btn x-large block :disabled="!valid" color="pink" v-on:click="submitForm">
                                            Submit
                                        </v-btn>
                                    </v-flex>
                                </v-col>
                            </v-layout>
                        </v-form>

                        <v-dialog v-model="dialog" width="500">
                            <v-card>
                                <v-card-title class="text-h5 grey lighten-2">Register</v-card-title>

                                <v-card-text>Register is successful.</v-card-text>

                                <v-divider></v-divider>

                                <v-card-actions>
                                    <v-spacer></v-spacer>
                                    <v-btn color="primary" text @click="dialog = false">I accept</v-btn>
                                </v-card-actions>
                            </v-card>
                        </v-dialog>
                    </v-tab-item>

                    <v-tab-item v-on:click="resultForm">
                        <v-card>
                            <v-card-title>
                                Registered Users
                                <v-spacer></v-spacer>
                            </v-card-title>
                            <v-data-table :headers="headers" :items="dataTable"></v-data-table>
                        </v-card>
                    </v-tab-item>
                </v-tabs>
            </v-flex>
        </v-layout>
    </v-container>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
import VueSimpleAlert from 'vue-simple-alert'
Vue.use(VueAxios, axios)
Vue.use(VueSimpleAlert)

export default {
    data: () => ({
        dialog: true,
        tab: 0,
        tabs: [
            { name: 'Register', icon: 'mdi-account-outline' },
            { name: 'Report', icon: 'mdi-folder-open' },
        ],
        on: undefined,
        gender: ['Female', 'Male', 'Other'],
        valid: true,
        form: {
            fname: '',
            lname: '',
            gender: '',
            birthday: '',
            email: '',
            username: '',
            password: '',
        },
        emailRules: [(v) => !!v || 'Required', (v) => /.+@.+\..+/.test(v) || 'E-mail must be valid'],
        show1: false,
        rules: {
            required: (value) => !!value || 'Required.',
            min: (v) => (v && v.length >= 8) || 'Min 8 characters',
        },
        dialog: false,
        alert: false,
        errMessage: '',
        usrData: '',

        return: {
            headers: [
                {
                    text: 'Firstname',
                    align: 'start',
                    filterable: false,
                    value: 'fname',
                },
                {
                    text: 'Lastname',
                    value: 'lname',
                },
                {
                    text: 'Email',
                    value: 'email',
                },
                {
                    text: 'Gender',
                    value: 'gender',
                },
                {
                    text: 'Username',
                    value: 'username',
                },
                {
                    text: 'Password',
                    value: 'password',
                },
                {
                    text: 'Birthday',
                    value: 'birthday',
                },
            ],
            dataTable: [],
        },
    }),

    computed: {
        errMessage() {
            return this.errMessage
        },
    },
    methods: {
        submitForm() {
            let parent = this
            this.axios
                .post('http://localhost:8000/vueRegis', {
                    fname: this.form.fname,
                    lname: this.form.lname,
                    gender: this.form.gender,
                    birthday: this.form.birthday,
                    email: this.form.email,
                    username: this.form.username,
                    password: this.form.password,
                })
                .then(function () {
                    parent.dialog = true
                    parent.$refs.registerForm.reset()
                })
                .catch(function (error) {
                    let err = error.response.data

                    // err.errors.forEach(function(err) {
                    //     parent.errMessage = err.msg ;
                    // });
                    // parent.alert = true;

                    let user = ''
                    for (var i = 0; i < err.errors.length; i++) {
                        user = user + err.errors[i].msg + '<br>'
                    }
                    parent.errMessage = user
                    parent.alert = true
                })
        },

        resultForm() {
            this.axios.get('http://localhost:8000/viewRegister').then((response) => {
                //console.log('test')
                console.log('response: ', response)
                // do something about response
            })
        },
    },
}
</script>
