<template>
  <div class="modal">
    <div class="modal-content">
      <span class="close" @click="$emit('close')">&times;</span>
      <h2>Добавление пользователя</h2>
      <form @submit.prevent="validateAndSave">
        <div class="form-group">
          <label>Имя</label>
          <input v-model="name" required placeholder="Введите имя (только буквы)" />
          <span v-if="nameError" class="error">{{ nameError }}</span>
        </div>
        <div class="form-group">
          <label>Телефон</label>
          <input v-model="phone" required placeholder="Введите номер +7(777)777-77-77" />
          <span v-if="phoneError" class="error">{{ phoneError }}</span>
        </div>
        <div class="form-group">
          <label>Начальник</label>
          <select v-model="parentId">
            <option :value="null">Без начальника</option>
            <option v-for="user in users" :key="user.id" :value="user.id">
              {{ user.name }}
            </option>
          </select>
        </div>
        <button type="submit" class="save-button">Сохранить</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  props: ['users'],
  data() {
    return {
      name: '',
      phone: '',
      parentId: null,
      nameError: '', 
      phoneError: '', 
    };
  },
  methods: {
    validateAndSave() {
      if (this.validateName() && this.validatePhone()) {
        this.saveUser();
      }
    },
    validateName() {
      const nameRegex = /^[a-zA-Zа-яА-ЯёЁ\s]+$/; 
      if (!this.name.trim()) {  
        this.nameError = 'Имя не может быть пустым';
        return false;
      }
      if (!nameRegex.test(this.name.trim())) {
        this.nameError = 'Имя может содержать только буквы и пробелы';
        return false;
      }
      this.nameError = '';
      return true;
    },
    validatePhone() {
      const phoneRegex = /^\+7\(\d{3}\)\d{3}-\d{2}-\d{2}$/; 
      if (!this.phone.trim()) {  
        this.phoneError = 'Телефон не может быть пустым';
        return false;
      }
      if (!phoneRegex.test(this.phone.trim())) {
        this.phoneError = 'Телефон должен быть в формате +7(777)777-77-77';
        return false;
      }
      this.phoneError = '';
      return true;
    },
    saveUser() {
      const newUser = {
        id: Date.now(),
        name: this.name.trim(),
        phone: this.phone.trim(),
        parentId: this.parentId,
      };
      this.$emit('save', newUser);
    },
  },
};
</script>

<style scoped>
.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-content {
  background-color: white;
  padding: 30px;
  border-radius: 12px;
  width: 320px;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  position: relative;
}

.close {
  position: absolute;
  top: 15px;
  right: 15px;
  cursor: pointer;
  font-size: 22px;
  color: #999;
}

h2 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 20px;
  text-align: center;
  color: #333;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-size: 14px;
  color: #555;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 14px;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.form-group input:focus,
.form-group select:focus {
  border-color: #66afe9;
  box-shadow: 0 0 8px rgba(102, 175, 233, 0.6);
}

.error {
  color: #e74c3c;
  font-size: 12px;
  margin-top: 5px;
  display: block;
}

.save-button {
  display: block;
  width: 100%;
  padding: 12px;
  background-color: #5cb85c;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

.save-button:hover {
  background-color: #4cae4c;
}
</style>
