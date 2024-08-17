<template>
  <tr>
    <td :style="{ paddingLeft: `${depth * 20}px` }">
      <span v-if="hasChildren" class="toggle-icon" @click="toggleChildren">{{ showChildren ? '-' : '+' }}</span>
      {{ user.name }}
    </td>
    <td>{{ user.phone }}</td>
    <td>
      <button @click="deleteUser" class="delete-button">Удалить</button>
    </td>
  </tr>
  <template v-if="showChildren">
    <template v-for="child in children" :key="child.id">
      <UserRow :user="child" :users="users" :depth="depth + 1" @delete="handleDelete($event)" />
    </template>
  </template>
</template>

<script>
export default {
  props: ['user', 'users', 'depth'],
  data() {
    return {
      showChildren: false,
    };
  },
  computed: {
    children() {
      return this.users.filter(child => child.parentId === this.user.id);
    },
    hasChildren() {
      return this.children.length > 0;
    },
  },
  methods: {
    toggleChildren() {
      this.showChildren = !this.showChildren;
    },
    deleteUser() {
      this.$emit('delete', this.user.id);
    },
    handleDelete(userId) {
      this.$emit('delete', userId);
    },
  },
};
</script>

<style scoped>
.toggle-icon {
  margin-right: 5px;
  cursor: pointer;
  font-weight: bold;
  display: inline-block;
  width: 15px;
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
