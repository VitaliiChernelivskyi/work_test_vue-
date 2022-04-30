<template>
  <div class="form-wrapper">
    <div class="form">
      <div class="form-field">
        <input v-validate.disable="'required|min:2'"
               name="firstName"
               placeholder="First Name"
               type="text"
               v-model="firstName"
        />
        <div class="error">{{ errors.first('firstName') }}</div>
      </div>
      <div class="form-field">
        <input
            v-validate.disable="'required|min:2'"
            name="lastName"
            placeholder="Last Name"
            type="text"
            v-model="lastName"
        />
        <div class="error">{{ errors.first('lastName') }}</div>
      </div>
      <div class="form-field">
        <div
            v-for="(phone, index) in phones"
            :key="index"
            class="form-field__input"
        >
          <input
              v-validate.disable="'required|numeric'"
              name="phone"
              placeholder="Phone"
              v-model="phone.value"
              type="number"
          />
          <div class="error">{{ errors.first('phone') }}</div>
          <button
              v-if="index > 0"
              @click="removePhone(index)"
          >-
          </button>
        </div>
      </div>

      <div class="form-field form-field--end">
        <button @click="addPhone">+</button>
        <button @click="addUser">addUser</button>
        <div v-if="phoneAlreadyExist" class="error">this phone is already exist</div>
      </div>

    </div>

    <div class="list"
         v-for="(user, index) in users"
         :key="index"
    >
      <div class="list_item">{{ user.firstName }}</div>
      <div class="list_item">{{ user.lastName }}</div>
      <div class="list_item">
        <div
            v-for="(phone, index) in user.phones"
            :key="index"
        >{{ phone.value }}
        </div>
        <button @click="removeUser(index)">remove user</button>
      </div>
    </div>
  </div>
</template>

<script>
import usersDB from "@/db/users";

export default {
  name: 'Contact',
  data: function () {
    return {
      users: [],
      phoneAlreadyExist: false,
      firstName: '',
      lastName: '',
      phones: [{
        value: '',
      }]
    }
  },
  created() {
    this.getUsers();
  },
  methods: {
    addPhone() {
      this.phones.push({
        value: ''
      });
    },
    removePhone(index) {
      this.phones.splice(index, 1);
    },
    addUser() {
      this.phoneAlreadyExist = this.users.find(item => item.phones[0].value === this.phones[0].value);
      if (this.phoneAlreadyExist) {
        return;
      }

      this.$validator.validate().then(valid => {
        if (!valid) {
          return;
        } else {
          let lastUserId = this.users[this.users.length - 1].id;
          const user = {
            id: lastUserId += 1,
            firstName: this.firstName,
            lastName: this.lastName,
            phones: this.phones,
          };

          this.users.push(user);
          this.firstName = '';
          this.lastName = '';
          this.phones = [{value: ''}];
        }
      });
    },
    getUsers() {
      setTimeout(() => this.users = usersDB, 1000);
    },
    removeUser(index) {
      this.users.splice(index, 1);
    },
  },
}
</script>

<style scoped>

input::-webkit-inner-spin-button {
  -webkit-appearance: none;
}

input {
  padding: 5px;
}

.list {
  display: flex;
  justify-content: space-between;
  padding: 10px;
}

.list_item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex: 1;
}

.form {
  background: #eeeeee;
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border: 1px solid #000000;
}

.form-field {
  flex: 1;
  padding: 10px 0;
}

.form-field--end {
  text-align: right;
}

.form-field__input + .form-field__input {
  margin-top: 10px;
}

button {
  height: 30px;
  min-width: 50px;
  background: #2cd920bd;
  color: #ffffff;
  border: none;
  border-radius: 2px;
  margin-right: 5px;
}

.error {
  font-size: 12px;
  margin-top: 5px;
  color: red;
}

@media screen and (max-width: 767px) {
  .list {
    display: flex;
    flex-direction: column;
  }

  .list_item {
    flex-direction: column;
    margin-bottom: 10px;
  }

  input {
    width: 100%;
  }

  .form {
    flex-direction: column;
  }
}
</style>