<template>
  <div>
    <input placeholder="Name" type="text" id="name" v-model="username"/>
  </div>
   <ul role="list" class="divide-y divide-gray-100 p-10">
      <li v-for="message in messages" :key="message" class="flex justify-between gap-x-6 py-5">
        <div class="flex min-w-0 gap-x-4">
          <img class="h-12 w-12 flex-none rounded-full bg-gray-50" :src="message.imageUrl" />
          <div class="min-w-0 flex-auto">
            <p class="text-sm font-semibold leading-6 text-gray-900">{{ message.username }}</p>
            <p class="mt-1 truncate text-xs text-gray-500">{{ message.message }}</p>
          </div>
        </div>
      </li>
    </ul>
    <div class="fixed bottom-10 left-4 right-4 lg:left-16 lg:right-16">
      <form  @submit.prevent="submit">
        <div class="relative mt-2 rounded-md shadow-sm">
          <input placeholder="Write a message" 
          type="text" 
          id="chat" 
          class="block w-full border-b border-gray-300 focus:border-indigo-600 focus:outline-none focus:border-b-2 py-1.5 pl-3 pr-20 text-gray-90 sm:text-sm sm:leading-6" 
          v-model="message"
          />
        </div>
      </form>
    </div>
</template>

<script>
import Pusher from 'pusher-js';
import axios from 'axios';


export default {
  el: '#app',
  data() {
    return {
      username: 'username',
      message: '',
      messages: []
    }
  },
  mounted() {
    Pusher.logToConsole = true;
 
    const pusher = new Pusher('', {
      cluster: ''
    });

    const channel = pusher.subscribe('chat');
    channel.bind('message', (data) => {
      this.messages.push(data);
    });
  },
  created() {
    this.$axios = axios; // Assign Axios to the Vue instance
  },
  methods: {
    async submit() {
      try {
        await this.$axios.post('http://localhost:8000/api/messages', {
          username: this.username,
          message: this.message
        });

        this.message = '';
      } catch (error) {
        // Handle any errors that occur during the API call
        console.error('Error sending message:', error);
     }
    }
  }
};
</script>

<style scoped>

</style>