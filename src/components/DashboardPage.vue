<template>
    <v-card>
        <v-layout>
            <v-app-bar color="primary" prominent elevation="0">

                <v-toolbar-title>OurApp</v-toolbar-title>

                <v-spacer></v-spacer>

                <v-btn icon="mdi-power" variant="text" @click="logout_user"></v-btn>
            </v-app-bar>

            <v-main style="height: 100vh;">
                <div class="h-screen">
                    <div class="d-flex align-center" style="height: 100vh;">

                        <v-card class="mx-auto pa-lg-4 pb-8 pa-6 border-sm" elevation="0" max-width="580">
                            <div class="text-body-1">
                                Welcome to User Dashboard.
                            </div>
                            <div class="text-caption">
                                Your user dashboard is your central hub for managing your account and accessing
                                personalized features.
                            </div>

                            <div>
                                <v-row align="center" justify="center">
                                    <v-col cols="auto">
                                        <v-btn density="comfortable" color="red-darken-1" variant="plain"
                                            @click="delete_user">Delete Account</v-btn>
                                    </v-col>

                                    <v-col cols="auto">
                                        <v-btn density="comfortable" @click="logout_user" color="primary">Logout</v-btn>
                                    </v-col>
                                </v-row>
                            </div>
                        </v-card>
                    </div>
                </div>
                <v-snackbar v-model="snackbar" :color="snackcolor" :timeout="timeout">
                    {{ text }}
                </v-snackbar>
            </v-main>
        </v-layout>
    </v-card>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { getAuth, signOut, onAuthStateChanged, deleteUser } from "firebase/auth";
import router from '@/router';

const auth = getAuth();

const snackbar = ref(false);
const text = ref('');
const timeout = ref(5000);
const snackcolor = ref('');

function logout_user() {
    signOut(auth).then(() => {
        const custom_msg = 'User Logged Out Successfully';
        const color_status = 'green-darken-1';
        get_custom_snackbar(custom_msg, color_status);
        router.push({ path: '/' })
    }).catch((error) => {
        const errcode = error.code;
        const custom_msg = errcode;
        const color_status = 'red-darken-4';
        get_custom_snackbar(custom_msg, color_status);
    });
}

function get_custom_snackbar(custom_msg, color_status) {
    snackbar.value = true;
    text.value = custom_msg;
    snackcolor.value = color_status;
}

function check_userauth() {
    onAuthStateChanged(auth, (user) => {
        if (!user) {
            router.push({ path: '/' })
        }
    });
}

function delete_user() {
    const user = auth.currentUser;

    deleteUser(user).then(() => {
        const custom_msg = 'User Deleted Successfully';
        const color_status = 'green-darken-1';
        get_custom_snackbar(custom_msg, color_status);
        router.push({ path: '/' })
    }).catch((error) => {
        const errcode = error.code;
        const custom_msg = errcode;
        const color_status = 'red-darken-4';
        get_custom_snackbar(custom_msg, color_status);
    });
}

onMounted(() => {
    check_userauth();
})
</script>