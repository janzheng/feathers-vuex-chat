<template>
  <div>

    <v-row>
      <v-col class="pl-6" md="auto">

        <div>
          <h3 class="mb-3" >Total Users: {{ userCount }}</h3>

          <v-card outlined class="mb-3" v-for="user of users" :key="user._id">
            <v-list-item>
              <v-list-item-avatar
                size="60"
                color="grey"
              ><img :src="user.avatar"></v-list-item-avatar>

              <v-list-item-content>
                {{ user.email }}
              </v-list-item-content>

            </v-list-item>
          </v-card>

        </div>
      </v-col>

      <v-col class="pr-6">

        <v-card class="mb-3" v-for="message of latestMessages" :key="message._id">
          <v-list-item two-line>
            <v-list-item-avatar
              size="60"
              color="grey"
            ><img :src="message.user.avatar"></v-list-item-avatar>
            <v-list-item-content>
              <div class="overline mb-4">{{ message.user.email }} | {{ nicedate(message.createdAt) }}  </div>
              <div>{{ message.text }} </div>
            </v-list-item-content>
          </v-list-item>
        </v-card>

        <v-footer fixed>
          <v-row>
            <v-col>
              <v-text-field
                v-model="inputMessage"
                label="Message"
                required
              ></v-text-field>
            </v-col>
            <v-col md="auto">
              <v-btn flat @click="sendMessage">Send</v-btn>
            </v-col>
          </v-row>
        </v-footer>

      </v-col>
    </v-row>

  </div>
</template>



<script>
/* eslint-disable no-console */
/* eslint-disable no-unused-vars */

import moment from 'moment'
import { mapState, mapGetters, mapActions } from 'vuex'
import feathersClient from '../feathers-client'; // from Github Feathers-Vuex docs


// use the mapgetters instead, these just help you test better
// async function getUsers() {
//   let users = await feathersClient.service('users').find()
//   return users
// }

// async function getMessages() {
//   let messages = await feathersClient.service('messages').find()
//   return messages
// }


export default {
  name: "Chat",

  data: () => {

    // let users = getUsers(), messages = getMessages()
    // console.log('async users...', users)
    // console.log('async messages...', messages)

    return {
      valid: false,
      user: {
        email: "",
        password: ""
      },
      inputMessage: "", 
      notEmptyRules: [value => !!value || "Can not be empty"]
    }
  },
  computed: {
    ...mapState("auth", { loading: "isAuthenticatePending" }),
    ...mapGetters('users', { allUsers: 'find' }),
    ...mapGetters('messages', { allMessages: 'find' }),

    userCount() {
      return this.allUsers().total
    }, 

    users() {
      return this.allUsers().data
    }, 

    latestMessages() {
      // find the latest 25 messages. they come newest first
      return this.allMessages({
        query: {
          $sort: { createdAt: 1 },
          $limit: 25
        }
      }).data
    }, 


    // todos () {
    //   return this.findTodosInStore({ query: {} }).data
    // }
  },
  methods: {
    ...mapActions('messages', { sendMessage: 'create' }), 

    nicedate: (date) => {
      return moment(date).format('MMM Do, hh:mm:ss')
    },

    sendMessage() {
      // let txt = this.inputMessage
      // this.sendMessage({
      //   text: "123123"
      // })
      const { Messages } = this.$FeathersVuex.api
      // console.log('???', Message, this.$FeathersVuex.api.Messages)
      const data = { text: this.inputMessage }
      const message = new Messages(data)

      message.create() // --> Creates the todo on the server using the instance data
      this.inputMessage = ""
    }, 
  },
  created () {
    console.log('created /messages',this.$FeathersVuex.api.Messages.find())
    console.log('created /users',this.$FeathersVuex.api.User.find())
    // this.findMessage({ query: {} })
    //   .then(response => {
    //     // In the find action, the 'todos' array is not a reactive list, but the individual records are.
    //     const messages = response.data || response
    //    console.log('created | findMessage', messages)
    //   })
  }
};
</script>

