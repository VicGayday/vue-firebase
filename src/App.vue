<template>
  <div class="container">
    <app-alert
    :alert="alert"
    @close="alert = null">
  </app-alert>
    <form class="card" @click.prevent="createUser">
      <h2>Работа с базой данных</h2>
      <div class="form-control">
        <label for="name"></label>
        <input type="text" id="name" v-model.trim="name">
      </div>
      <button class="btn primary" :disabled="validateName" >Создать пользователя</button>
    </form>
    <app-loader v-if="isLoading"></app-loader>
    <app-users-list
    v-else
    :users="users"
    @load="loadUsers"
    @remove="removeUsers"
    ></app-users-list>
  </div>
</template>

<script>
import axios from 'axios';
import { defineComponent, ref, computed, onMounted } from "vue";
import AppUsersList from "./AppUsersList.vue";
import AppAlert from "./AppAlert.vue";
import AppLoader from "./AppLoader.vue";
export default defineComponent({
  name: "App",
  components: {
    AppUsersList,
    AppAlert,
    AppLoader,
  },
  setup() {
    const name = ref('');
    const users = ref([]);
    const alert = ref(null);
    const isLoading = ref(false);

    const validateName = computed(()=> {
      //return !name.value.trim();
     return name.value.length === 0 ? true : false;
    })

    async function createUser() {
      const response = await fetch('https://vue-users-base-default-rtdb.europe-west1.firebasedatabase.app/users.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          firstName: name.value
        })
      })
      const firebaseData = await response.json();
      users.value.push({
        firstName: name.value,
        id: firebaseData.name
      })
      name.value = "";
    }

    async function loadUsers() {

        isLoading.value = true;
        setTimeout( async () => {
           try {
        const { data } = await axios('https://vue-users-base-default-rtdb.europe-west1.firebasedatabase.app/users.json');
          users.value = Object.keys(data).map((key) => {
          return {
            id: key,
            firstName: data[key].firstName
          }
        })
        const filteredUsers = users.value.filter(user => user.firstName !== '')
        if (!filteredUsers.length) {
          throw new Error('В базе данных нет пользователей')
        }
         isLoading.value = false;
       } catch (e) {
        alert.value = {
          type: 'danger',
          title: 'Ошибка',
          text: e.message,
        }
        isLoading.value = false;
      }
      }, 1500)
    }

    async function removeUsers(id) {
      try {
      const name = users.value.find((user) => user.id === id).firstName
      await axios.delete(`https://vue-users-base-default-rtdb.europe-west1.firebasedatabase.app/users/${id}.json`)
      users.value = users.value.filter((user) => {
        return user.id !== id
      })
      alert.value = {
        type: 'primary',
        title: "Успешно!",
        text: `Пользователь ${name} был удален`
      }
      }
      catch(e) {
        console.log(e);
      }
    }

    onMounted(() => {
      loadUsers()
    })


  return {
    name,
    validateName,
    createUser,
    loadUsers,
    removeUsers,
    users: computed(() => users.value),
    alert,
    isLoading,
  }
  }
})
</script>

<style>

</style>
