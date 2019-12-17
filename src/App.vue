<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title>feathers-vuex-chat && Feathers-4 + F-Vuex-2.0</v-toolbar-title>
      <div class="flex-grow-1"></div>

      <v-spacer></v-spacer>

      <div v-if="!user">
        <v-row>
          <v-col>
            <v-btn flat :to="{ name: 'login'}">Login</v-btn>
          </v-col>
          <v-col>
            <v-btn flat :to="{ name: 'signup'}">SignUp</v-btn>
          </v-col>
          <v-col>
            <!-- the server oAuth github endpoint is set up in the Feathers chat tutorial
                we're just using the same endpoint, but make sure you change the callback url to the vue server
                e.g. localhost:8080/chat
             -->
            <v-btn flat href="http://localhost:3030/oauth/github">Login with GitHub</v-btn>
          </v-col>
        </v-row>
      </div>
      <div v-if="user">
        <v-row>
          <v-col>
            <v-btn flat :to="{ name: 'about'}">About</v-btn>
          </v-col>
          <v-col>
            <v-btn flat :to="{ name: 'chat'}">Chat</v-btn>
          </v-col>
          <v-col>
            <v-btn flat @click="logout">LogOut</v-btn>
          </v-col>
        </v-row>
      </div>
    </v-app-bar>

    <v-footer :fixed="fixed" app>
      <p>Inspiration from this example: <a href="https://github.com/lturel/Feathers4-Vue-FVuex2">https://github.com/lturel/Feathers4-Vue-FVuex2</a> and the <a href="https://github.com/feathersjs/feathers-chat">Feathers chat guide</a></p>
    </v-footer>

    <v-content>
      <router-view />
    </v-content>

  </v-app>
</template>

<script>
/* eslint-disable no-console */

import { mapActions, mapState } from "vuex";

export default {
  name: "App",
  data: () => ({
    fixed: false
  }),
  computed: {
    ...mapState("auth", { user: "payload" })
  },
  methods: {
    //  ...mapActions("auth", ["logout"])  // bu şekilde logout eder ve olduğu sayfada kalır. Login için ;
    ...mapActions("auth", { myLogout: "logout" }), // bu logout auth'un logout'u
    ...mapActions("auth", ["authenticate"]),

    // github() {
    //   if (this.valid) {
    //     // Authenticate with the local email/password strategy
    //     this.authenticate({ ...this.user, strategy: "local" })
    //       .then(() => {
    //         console.log(" Logged in ...");
    //         this.$router.push("/about");
    //       })
    //       .catch(e => {
    //         console.error("Authentication ERROR ...", e);
    //       });
    //   }
    // }

    logout() {
      // bu logout;  yeni custom logout
      this.myLogout().then(() => this.$router.push("/login"));
    },

  }
};

</script>














