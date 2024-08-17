<template>
  <div class="user-table-container">
    <button @click="showModal = true" class="add-button">Добавить</button>
    <table class="user-table">
      <thead>
        <tr>
          <th @click="sortBy('name')">Имя <span v-if="sortKey === 'name'">{{ sortOrder === 1 ? '▲' : '▼' }}</span></th>
          <th @click="sortBy('phone')">Телефон <span v-if="sortKey === 'phone'">{{ sortOrder === 1 ? '▲' : '▼' }}</span></th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <UserRow
          v-for="user in sortedUsers.filter(user => !user.parentId)"
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
      sortKey: '',
      sortOrder: 1, // 1 for ascending, -1 for descending
    };
  },
  computed: {
    sortedUsers() {
      const sortByKey = (key, order) => (a, b) => {
        if (a[key] < b[key]) return -1 * order;
        if (a[key] > b[key]) return 1 * order;
        return 0;
      };
      
      const sortUsers = (users, key, order) => {
        users.sort(sortByKey(key, order));
        users.forEach(user => {
          const children = users.filter(child => child.parentId === user.id);
          if (children.length) {
            sortUsers(children, key, order);
          }
        });
      };

      const sortedUsers = [...this.users.filter(user => !user.parentId)];
      sortUsers(sortedUsers, this.sortKey, this.sortOrder);
      return sortedUsers;
    }
  },
  methods: {
    sortBy(key) {
      if (this.sortKey === key) {
        this.sortOrder = this.sortOrder === 1 ? -1 : 1;
      } else {
        this.sortKey = key;
        this.sortOrder = 1;
      }
    },
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
  text-align: left;
  margin-top: 20px;
  padding-left: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.add-button {
  background-color: #ccc;
  border: 1px solid #bbb;
  border-radius: 15px;
  padding: 10px 20px;
  font-size: 14px;
  cursor: pointer;
  margin-bottom: 10px;
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
  cursor: pointer;
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
