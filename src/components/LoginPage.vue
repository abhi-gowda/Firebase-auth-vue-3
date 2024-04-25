<template>
    <v-layout class="app_auth">
        <v-navigation-drawer v-model="drawer" temporary>
            <p class="ps-4 pt-3">Control UI Design</p>
            <v-list density="compact" class="px-4 pt-5">
                <div class="mb-4">
                    <v-select chips label="Text Field" variant="outlined"
                        :items="['underlined', 'outlined', 'filled', 'solo', 'solo-filled', 'solo-inverted']"
                        v-model="variants" density="comfortable"></v-select>
                    <v-divider></v-divider>
                </div>

                <div class="mb-4">
                    <v-select chips label="Button" variant="outlined"
                        :items="['flat', 'elevated', 'outlined', 'tonal', 'text', 'plain']" v-model="buttons"
                        density="comfortable"></v-select>
                    <v-divider></v-divider>
                </div>

                <div class="mb-4">
                    <v-select chips label="Button" variant="outlined"
                        :items="['blue', 'red', 'grey', 'blue-grey', 'brown', 'deep-orange', 'orange', 'amber', 'yellow', 'lime', 'light-green', 'green', 'teal', 'cyan', 'light-blue', 'indigo', 'deep-purple', 'purple', 'pink']"
                        v-model="colors" density="comfortable"></v-select>
                    <v-divider></v-divider>
                </div>
            </v-list>
        </v-navigation-drawer>
        <v-main>
            <div class="h-screen">
                <div class="d-flex align-center" style="height: 100vh;">

                    <v-card class="mx-auto pa-lg-12 pb-8 pa-6 register_card" elevation="8" max-width="448"
                        :min-width="height" rounded="lg">
                        <v-form @submit.prevent="login_user" lazy-validation v-model="valid">
                            <div class="text-h5 font-weight-bold">Sign in</div>
                            <div class="text-body-2 mb-3">Stay updated on your digital world</div>

                            <div class="text-subtitle-1 text-medium-emphasis">Email Address</div>

                            <v-text-field placeholder="Email address" prepend-inner-icon="mdi-email-outline"
                                :variant="variants" v-model="email_address" :rules="emailRules"></v-text-field>

                            <div class="text-subtitle-1 text-medium-emphasis d-flex align-center justify-space-between">
                                Password

                                <v-dialog v-model="dialog" max-width="448" persistent>
                                    <template v-slot:activator="{ props: activatorProps }">
                                        <a class="text-caption text-decoration-none text-blue" v-bind="activatorProps">
                                            Forgot login password?</a>
                                    </template>

                                    <v-card title="Reset Your Password" class="pa-4 pb-6">
                                        <v-card-text>
                                            <v-row dense>
                                                <v-col cols="12">
                                                    <v-text-field label="Email Address" :variant="variants"
                                                        :rules="emailRules" v-model="reset_emailadd"></v-text-field>
                                                </v-col>
                                                <v-col cols="12">
                                                    <v-btn :color="colors" size="large" :variant="buttons" block
                                                        @click="reset_password">Send Reset
                                                        Password</v-btn>
                                                </v-col>
                                            </v-row>
                                        </v-card-text>
                                    </v-card>
                                </v-dialog>

                            </div>

                            <v-text-field :append-inner-icon="visible ? 'mdi-eye-off' : 'mdi-eye'"
                                :type="visible ? 'text' : 'password'" placeholder="Enter your password"
                                prepend-inner-icon="mdi-lock-outline" :variant="variants"
                                @click:append-inner="visible = !visible" :rules="passwdRules"
                                v-model="passwd"></v-text-field>

                            <v-btn class="mb-4 mt-2" :color="colors" size="large" :variant="buttons" block
                                :disabled="!valid" type="submit">
                                Log In
                            </v-btn>

                            <v-card-text class="text-center">
                                <a class="text-black text-decoration-none">
                                    New to OurApp? <RouterLink to="/register_user" class="text-decoration-none"><span
                                            class="text-blue">Register
                                            now</span></RouterLink>
                                </a>
                            </v-card-text>
                        </v-form>
                    </v-card>



                </div>
            </div>
            <v-snackbar v-model="snackbar" :color="snackcolor" :timeout="timeout">
                {{ text }}
            </v-snackbar>
            <div style="position: absolute;bottom:30px;right: 30px;">
                <v-btn icon="mdi-cog" @click.stop="drawer = !drawer" class="icon_btn_rotate"></v-btn>
            </div>
        </v-main>
    </v-layout>

</template>

<script setup>
import { computed, onMounted, ref } from 'vue'
import { useDisplay } from 'vuetify'
import { getAuth, signInWithEmailAndPassword, onAuthStateChanged, sendPasswordResetEmail } from "firebase/auth";
import { useRoute } from 'vue-router';
import router from '@/router';

const { name } = useDisplay()

const height = computed(() => {
    // name is reactive and
    // must use .value
    switch (name.value) {
        case 'xs': return 340
        case 'sm': return 448
        case 'md': return 480
        case 'lg': return 480
        case 'xl': return 480
        case 'xxl': return 560
    }

    return undefined
})

const drawer = ref(false);

const variants = ref('filled');
const buttons = ref('flat');
const colors = ref('blue');

// Define a ref to store the input value
const email_address = ref('');
const reset_emailadd = ref('')
const passwd = ref('');
const visible = ref(false);
const valid = ref(true);

const snackbar = ref(false);
const text = ref('');
const timeout = ref(5000);
const snackcolor = ref('');

const auth = getAuth();
const route = useRoute();

const dialog = ref(false);

//user validation
const emailRules = [
    v => !!v || 'E-mail Address is required',
    v => /.+@.+\..+/.test(v) || 'E-mail Address must be valid']
const passwdRules = [
    v => !!v || 'Password is required']


function login_user() {
    const email = email_address.value;
    const password = passwd.value;

    if ((email != '') || (password != '')) {
        signInWithEmailAndPassword(auth, email, password)
            .then((userCredential) => {
                const user = userCredential.user;
                if (user) {
                    const custom_msg = 'User Logged In Successfully';
                    const color_status = 'green-darken-1';
                    get_custom_snackbar(custom_msg, color_status);
                    check_userauth();
                }
            })
            .catch((error) => {
                const errcode = error.code;
                const custom_msg = errcode;
                const color_status = 'red-darken-4';
                get_custom_snackbar(custom_msg, color_status);
            });
    }
}

function get_custom_snackbar(custom_msg, color_status) {
    snackbar.value = true;
    text.value = custom_msg;
    snackcolor.value = color_status;
}

function check_userauth() {
    onAuthStateChanged(auth, (user) => {
        if (user) {
            router.push({ path: '/dashboard' })
        }
    });
}

function reset_password() {
    const email = reset_emailadd.value;
    sendPasswordResetEmail(auth, email)
        .then(() => {
            const custom_msg = 'Password Reset Link sent successfully';
            const color_status = 'green-darken-1';
            get_custom_snackbar(custom_msg, color_status);
            dialog.value = false;
        })
        .catch((error) => {
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