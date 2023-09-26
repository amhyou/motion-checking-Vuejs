<script setup>
  import { ref, onMounted, watch } from 'vue'
  import constants from "./constants"

  const loading_account = ref(false)
  const email = ref('')
  const password = ref('')
  const registered = ref(false)

  const loading_data = ref(false)
  const data = ref('')

  const task_id = ref(null)
  const progression = ref(0)
  const processed = ref([])


  onMounted( async () => {
    const url = constants.BACKEND + "check_account"
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          // Add any other headers your API requires, such as authorization token
        },
      });

      if (!response.ok) {
        // Handle the response error here, if necessary
        throw new Error('Network response was not ok.');
      }
      const responseData = await response.json();
      console.log(responseData); // You can do something with the response data here
      
      if (responseData.email){
        registered.value = true
        email.value = responseData.email
        password.value = responseData.password
      }
      if (responseData.task_id){
        task_id.value = responseData.task_id

      }
    } catch (error) {
      // Handle any errors that occurred during the fetch
      console.error('Error:', error);
    }

  })

  async function submit_account(event) {
    loading_account.value = true
    const repsonse = await register_account(email.value, password.value)
    loading_account.value = false
    alert(JSON.stringify(repsonse, null, 2))
  }

  async function submit_data(event) {
    loading_data.value = true
    const response = await process_data(data.value)
    loading_data.value = false
    alert(JSON.stringify(response, null, 2))
  }


  async function register_account(email, password){
    const url = constants.BACKEND + "register_account"
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          // Add any other headers your API requires, such as authorization token
        },
        body: JSON.stringify({email, password}),
      });

      if (!response.ok) {
        // Handle the response error here, if necessary
        throw new Error('Network response was not ok.');
      }

      const responseData = await response.json();
      console.log(responseData); // You can do something with the response data here
      return responseData
    } catch (error) {
      // Handle any errors that occurred during the fetch
      console.error('Error:', error);
    }
  }

  async function process_data(data){
    const url = constants.BACKEND + "process_data"
    try {
      const response = await fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          // Add any other headers your API requires, such as authorization token
        },
        body: JSON.stringify({data}),
      });

      if (!response.ok) {
        // Handle the response error here, if necessary
        throw new Error('Network response was not ok.');
      }

      const responseData = await response.json();
      console.log(responseData); // You can do something with the response data here
      return responseData
    } catch (error) {
      // Handle any errors that occurred during the fetch
      console.error('Error:', error);
    }
  }

</script>


<template>
  <v-app>
    <v-main>
      
        <v-container v-if="!registered">

          <v-sheet max-width="300" class="mx-auto mt-10" height="150px">
            <v-form @submit.prevent="submit_account">
              <v-text-field
              v-model="email"
              label="Email"
              ></v-text-field>

              <v-text-field
              v-model="password"
              label="Password"
              ></v-text-field>
      
              <v-btn
              :loading="loading_account"
              type="submit"
              block
              class="mt-2"
              text="Submit"
              ></v-btn>
            </v-form>
          </v-sheet>

        </v-container>

        <v-container v-else class="d-flex justify-center">
          <v-chip
              class="ma-2"
              color="primary"
              label
          >
          <v-icon start icon="mdi-account-circle-outline"></v-icon>
          John Leider
          </v-chip>
      </v-container>

    
  
    <v-container class="mx-16 mt-16 pt-16" v-if="!task_id">
      <v-form @submit.prevent="submit_data">
        <v-row>  
          <v-textarea
            variant="filled"
            bg-color="grey-lighten-2"
            color="white"
            label="Base"
            rows="20"
            row-height="30"
            shaped
            clearable
            v-model="data"
            ></v-textarea>
        </v-row>

        <v-btn
            :loading="loading_data"
            type="submit"
            block
            text="Check"
        ></v-btn>
      </v-form>
   </v-container>


   <v-container v-else>

    <v-progress-linear
        v-model="progression"
        color="amber"
        height="25"
        class="mt-7"
      >
      <template v-slot:default="{ progression }">
        <strong>{{ Math.ceil(progression) }}%</strong>
      </template>
    </v-progress-linear>

    <v-sheet class="d-flex h-100 mx-auto mt-16 justify-space-evenly">
        
      <v-list lines="one">
          <v-list-item
              title="-------- Invalid --------"
          ></v-list-item>
          <v-list-item
              v-for="item in processed"
              :title="item"
          ></v-list-item>
      </v-list>


      <v-divider
        :thickness="3"
        class="border-opacity-50"
        color="info"
        vertical
      ></v-divider>

      <v-list lines="one">
          <v-list-item
              title="-------- Valid --------"
          ></v-list-item>
          <v-list-item
              v-for="item in processed"
              :title="item"
          ></v-list-item>
      </v-list>


    </v-sheet>

   </v-container>



    </v-main>
  </v-app>
</template>