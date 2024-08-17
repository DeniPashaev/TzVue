<template>
  <div class="user-table-container">
    <button @click="showModal = true" class="add-button">Добавить</button>
    <table class="user-table">
      <thead>
        <tr>
          <th>Имя</th>
          <th>Телефон</th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <UserRow
          v-for="user in users.filter(user => !user.parentId)"
          :key="user.id"
          :user="user"
          :users="users"
          :depth="1"
          @delete="deleteUser"
        />
      </tbody>
    </table>

    <UserModal v-if="showModal" :users="users" @close="showModal = false" @save="addUser" />
  </div>
</template>

<script>
import UserModal from './UserModal.vue';
import UserRow from './UserRow.vue';

export default {
  components: {
    UserModal,
    UserRow,
  },
  data() {
    return {
      users: JSON.parse(localStorage.getItem('users')) || [],
      showModal: false,
    };
  },
  methods: {
    addUser(user) {
      this.users.push(user);
      this.updateLocalStorage();
      this.showModal = false;
    },
    deleteUser(userId) {
      this.users = this.users.filter(user => user.id !== userId && user.parentId !== userId);
      this.updateLocalStorage();
    },
    updateLocalStorage() {
      localStorage.setItem('users', JSON.stringify(this.users));
    },
  },
};
</script>

<style scoped>
.user-table-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.add-button {
  background-color: #ccc;
  border: 1px solid #bbb;
  border-radius: 15px;
  padding: 10px 20px;
  font-size: 14px;
  cursor: pointer;
  margin-bottom: 10px;
  text-align: center;
}

.add-button:hover {
  background-color: #bbb;
}

.user-table {
  width: 80%;
  border-collapse: collapse;
  margin: 0 auto;
}

.user-table th, .user-table td {
  border: 1px solid #ccc;
  padding: 8px;
  text-align: center;
}

.user-table th {
  background-color: #f4f4f4;
}

.user-table td {
  background-color: #fff;
  position: relative;
}

.user-row:hover {
  background-color: #f9f9f9;
}

.delete-button {
  background-color: #e74c3c;
  border: none;
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: #c0392b;
}
</style>
