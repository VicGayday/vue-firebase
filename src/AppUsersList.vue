<template>
  <div v-if="loadedUsers.length">
    <div class="card inline" v-for="user in loadedUsers" :key="user.id">
      <h3>{{ user.firstName }}</h3>
      <button class="btn danger" @click="emitRemove(user.id)">Удалить</button>
    </div>
  </div>
  <div class="card center" v-else>
    <h4>В базе данных нет ни одного пользователя</h4>
    <button class="btn" @click="emitLoad">Загрузить список</button>
  </div>
</template>

<script>
import { defineComponent, watchEffect, ref } from 'vue';
export default defineComponent({
  name: "AppUsersList",
  emits: ['load', 'remove'],
  props: ['users'],
  setup(props, {emit}) {
    const loadedUsers = ref([]);
    const emitLoad = () => {
      emit('load')
    };

    const emitRemove = (id) => {
      emit('remove', id)
    }

     watchEffect(() => {
      loadedUsers.value = props.users.filter(user => user.firstName !== '');
    });

    return {
      loadedUsers,
      emitLoad,
      emitRemove,
    }
  }
})
</script>

<style scoped>
.inline {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>