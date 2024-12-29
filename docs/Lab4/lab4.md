# Документация проекта Hotel System

## Содержание

1. [Введение](#I=introduction)
2. [Установка и настройка](#installing)
3. [Структура проекта](#S=structure)
4. [Компоненты](#components)
    - [NavigationBar.vue](#navigationbarvue)
    - [Home.vue](#homevue)
    - [Login.vue](#loginvue)
    - [Register.vue](#registervue)
    - [Profile.vue](#profilevue)
    - [Clients.vue](#clientsvue)
    - [Rooms.vue](#roomsvue)
    - [Stays.vue](#staysvue)
    - [Cleanings.vue](#cleaningsvue)
    - [Schedules.vue](#schedulesvue)
    - [Report.vue](#reportvue)
    - [Staff.vue](#staffvue)
    - [Start.vue](#startvue)
5. [Маршрутизация](#routing)
6. [Авторизация и аутентификация](#auth)
7. [Интеграция с API](#integration-api)
8. [Использование Vuetify](#vuetify)
10. [Заключение](#end)

---

## Introduction

**Hotel System** — это веб-приложение для управления гостиницей, реализованное с использованием Vue.js и Vite на клиентской стороне и Django REST framework на серверной стороне. Приложение предоставляет интерфейсы для управления клиентами, сотрудниками, номерами, бронированиями и уборками, а также функции авторизации и регистрации пользователей.

### Используемые технологии

- **Frontend:**
  - Vue.js 3
  - Vite
  - Vuetify
  - Axios

- **Backend:**
  - Django REST framework


---

## Installing

### Предварительные требования

- Node.js (версии 14 и выше)
- npm или yarn
- Python 3.8 и выше
- Django и Django REST framework
- Django CORS Headers


## Structure

```
hotel-system/
├── src/
│   ├── assets/
│   │   └── hotel-styles.css
│   ├── components/
│   │   └── NavigationBar.vue
│   ├── views/
│   │   ├── Home.vue
│   │   ├── Login.vue
│   │   ├── Register.vue
│   │   ├── Profile.vue
│   │   ├── Clients.vue
│   │   ├── Rooms.vue
│   │   ├── Stays.vue
│   │   ├── Cleanings.vue
│   │   ├── Schedules.vue
│   │   ├── Report.vue
│   │   ├── Staff.vue
│   │   └── Start.vue
│   ├── router/
│   │   └── router.js
│   ├── App.vue
│   └── main.js
├── public/
├── package.json
├── vite.config.js
└── ... (другие файлы)
```

---

## Components

### NavigationBar.vue

Навигационная панель, отображающаяся на всех страницах после авторизации пользователя. Показывает различные кнопки в зависимости от роли пользователя (администратор или обычный пользователь).

```vue
<template>
  <v-app-bar elevation="8" color="#a5f5f4">
    <v-toolbar-title>Hotel System</v-toolbar-title>
    <v-spacer></v-spacer>

    <v-btn text to="/">Главная</v-btn>
    <v-btn text to="/profile">Профиль</v-btn>
    
    <!-- Кнопки для сотрудников, отображаются только если пользователь является сотрудником -->
    <template v-if="userData.is_staff">
      <v-btn text to="/staff">Сотрудники</v-btn>
      <v-btn text to="/rooms">Номера</v-btn>
      <v-btn text to="/clients">Клиенты</v-btn>
      <v-btn text to="/stays">Проживания</v-btn>
      <v-btn text to="/cleanings">Уборки</v-btn>
      <v-btn text to="/schedules">Расписания</v-btn>
      <v-btn text to="/report">Отчёт</v-btn>
    </template>

    <v-btn variant="elevated" color="error" @click="logout">Выйти</v-btn>
  </v-app-bar>
</template>

<script>
import axios from 'axios'

export default {
  name: 'NavigationBar',
  data() {
    return {
      token: null,
      userData: {}
    }
  },
  created() {
    // Получение токена из локального хранилища при создании компонента
    this.token = localStorage.getItem('token')
    if (this.token) {
      this.fetchUser() // Если токен существует, получить данные пользователя
    }
  },
  methods: {
    async fetchUser() {
      try {
        const response = await axios.get('http://127.0.0.1:8000/auth/users/me/', {
          headers: { Authorization: `Token ${this.token}` }
        })
        this.userData = response.data
      } catch (error) {
        console.error(error) 
      }
    },
    logout() {
      // Удаление токена и данных пользователя при выходе
      localStorage.removeItem('token')
      this.token = null
      this.userData = {}
      this.$router.push('/start') 
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `token`: Хранит токен авторизации пользователя.
  - `userData`: Хранит данные пользователя, полученные с сервера.

- **Created Hook:**
  - При создании компонента извлекает токен из `localStorage`.
  - Если токен существует, вызывает метод `fetchUser` для получения данных пользователя.

- **Methods:**
  - `fetchUser`: Отправляет GET-запрос к серверу для получения информации о текущем пользователе.
  - `logout`: Удаляет токен из `localStorage`, очищает данные пользователя и перенаправляет на страницу входа.

---

### Home.vue

Главная страница приложения с приветственным сообщением и кнопкой перехода к профилю.

```vue
<template>
  <v-container class="d-flex justify-center">
    <v-card
      class="pa-5 elevated-card"
      width="400"
      height="215"
    >
      <h2 class="text-center">Добро пожаловать в гостиницу!</h2>
      <p class="text-center">Здесь можно управлять клиентами, сотрудниками, номерами и т. д.</p>
      <div class="d-flex justify-center mt-4">
        <v-btn variant="elevated" color="primary" to="/profile">
          Перейти к профилю
        </v-btn>
      </div>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: 'Home'
}
</script>
```

**Описание:**

- Простая страница с приветственным сообщением и кнопкой для перехода к профилю пользователя.

---

### Login.vue

Компонент авторизации пользователя. Позволяет пользователю войти в систему, введя логин и пароль.

```vue
<template>
  <v-container class="mt-4" max-width="400">
    <v-card class="pa-4 elevated-card">
      <h2>Авторизация</h2>
      <v-form @submit.prevent="handleLogin">
        <!-- Поле для ввода логина -->
        <v-text-field
          v-model="form.username"
          label="Логин"
          required
        ></v-text-field>
        <!-- Поле для ввода пароля -->
        <v-text-field
          v-model="form.password"
          label="Пароль"
          type="password"
          required
        ></v-text-field>
        <!-- Кнопка для отправки формы -->
        <v-btn variant="elevated" color="primary" type="submit" class="mr-1">
          Войти
        </v-btn>
        <!-- Кнопка для возврата на предыдущую страницу -->
        <v-btn variant="outlined" color="error" @click="$router.push('/start')" class="ml-1">
          Назад
        </v-btn>
      </v-form>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Login',
  data() {
    return {
      form: {
        username: '',
        password: ''
      }
    }
  },
  methods: {
    async handleLogin() {
      try {
        // Отправка данных для авторизации на сервер
        const response = await axios.post('http://127.0.0.1:8000/auth/token/login/', {
          username: this.form.username,
          password: this.form.password
        })
        // Сохранение токена в localStorage
        const token = response.data.auth_token
        localStorage.setItem('token', token)
        // Переход на главную страницу после успешной авторизации
        this.$router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `form`: Объект, содержащий поля `username` и `password`.

- **Methods:**
  - `handleLogin`: Отправляет POST-запрос на сервер с данными формы для авторизации. При успешном ответе сохраняет токен в `localStorage` и перенаправляет пользователя на главную страницу.

---

### Register.vue

Компонент регистрации нового пользователя. Позволяет создать новую учетную запись, введя необходимые данные.

```vue
<template>
  <v-container class="mt-4" max-width="400">
    <v-card class="pa-4 elevated-card">
      <h2>Регистрация</h2>
      <v-form @submit.prevent="handleRegister">
        <!-- Поле для ввода логина -->
        <v-text-field
          v-model="form.username"
          label="Логин"
          required
        ></v-text-field>
        <!-- Поле для ввода email -->
        <v-text-field
          v-model="form.email"
          label="Email"
          required
        ></v-text-field>
        <!-- Поле для ввода пароля -->
        <v-text-field
          v-model="form.password"
          label="Пароль"
          type="password"
          required
        ></v-text-field>
        <!-- Поле для повторного ввода пароля -->
        <v-text-field
          v-model="form.password2"
          label="Повтор пароля"
          type="password"
          required
        ></v-text-field>
        <!-- Кнопка для отправки формы регистрации -->
        <v-btn variant="elevated" color="primary" type="submit" class="mr-1">
          Зарегистрироваться
        </v-btn>
        <!-- Кнопка для возврата на предыдущую страницу -->
        <v-btn variant="outlined" color="error" @click="$router.push('/start')" class="ml-1">
          Назад
        </v-btn>
      </v-form>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Register',
  data() {
    return {
      form: {
        username: '',
        email: '',
        password: '',
        password2: ''
      }
    }
  },
  methods: {
    async handleRegister() {
      try {
        // Отправка данных формы на сервер для регистрации пользователя
        await axios.post('http://127.0.0.1:8000/auth/users/', {
          username: this.form.username,
          email: this.form.email,
          password: this.form.password,
          re_password: this.form.password2
        })
        // Переход на страницу логина после успешной регистрации
        this.$router.push('/login')
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `form`: Объект, содержащий поля `username`, `email`, `password` и `password2`.

- **Methods:**
  - `handleRegister`: Отправляет POST-запрос на сервер с данными формы для регистрации. При успешном ответе перенаправляет пользователя на страницу входа.

---

### Profile.vue

Компонент профиля пользователя, отображающий информацию о текущем пользователе и его бронированиях. Позволяет редактировать профиль и управлять данными клиента.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Профиль пользователя</h2>

    <!-- Основная карта с данными пользователя -->
    <v-card class="elevated-card pa-4 mb-4">
      <v-card-title class="text-h5">Информация пользователе</v-card-title>
      <v-card-text>
        <div><strong>Роль:</strong> {{ userData.is_staff ? 'Администратор' : 'Пользователь' }}</div>
        <div><strong>Имя пользователя:</strong> {{ userData.username }}</div>
        <div><strong>Email:</strong> {{ userData.email }}</div>
      </v-card-text>
      <v-card-actions class="d-flex justify-center">
        <v-btn variant="elevated" color="primary" @click="editProfile">Редактировать профиль</v-btn>
        <v-btn variant="outlined" color="error" @click="logout">Выйти</v-btn>
      </v-card-actions>
    </v-card>

    <!-- Карточка с данными о клиенте (Client) -->
    <v-card v-if="userClientData" class="elevated-card pa-4 mb-4">
      <v-card-title class="text-h5">
        Мои данные
      </v-card-title>
      <v-card-text>
        <div>
          <strong>ФИО:</strong>
          {{ userClientData.last_name }} {{ userClientData.first_name }} {{ userClientData.middle_name }}
        </div>
        <div>
          <strong>Паспорт:</strong> {{ userClientData.passport_number }}
        </div>
        <div>
          <strong>Город:</strong> {{ userClientData.city_of_origin }}
        </div>
      </v-card-text>
      <v-card-actions class="d-flex justify-center">
        <v-btn variant="elevated" color="primary" @click="editClient">
          Редактировать данные
        </v-btn>
        <v-btn variant="outlined" color="error" @click="deleteClient">
          Удалить данные
        </v-btn>
      </v-card-actions>
    </v-card>

    <!-- Если клиентские данные отсутствуют, показываем кнопку "Добавить" -->
    <v-card v-else class="elevated-card pa-4 mb-4">
      <v-card-title class="text-h5">У вас нет клиентской информации</v-card-title>
      <v-card-text>
        Хотите добавить собственные данные клиента?
      </v-card-text>
      <v-card-actions>
        <v-btn variant="elevated" color="primary" @click="createClientData">
          Добавить данные клиента
        </v-btn>
      </v-card-actions>
    </v-card>

    <!-- Карточка с бронированиями (Stays) -->
    <v-card class="elevated-card pa-4 mb-4">
      <v-card-title class="text-h5">Мои бронирования</v-card-title>
      <v-card-text>
        <div v-if="userStays.length === 0">
          Нет текущих бронирований
        </div>
        <v-row v-else>
          <v-col
            v-for="stay in userStays"
            :key="stay.stay_id"
            cols="12"
            md="6"
            lg="4"
          >
            <v-card class="mb-4 elevated-card pa-2">
              <v-card-item>
                <div>
                  <div class="text-h6">
                    Номер: {{ stay.room?.room_id }} (Этаж: {{ stay.room?.floor }})
                  </div>
                  <div class="text-subtitle-2 mb-2">
                    Дата заезда: {{ stay.check_in_date }}<br/>
                    Дата выезда: {{ stay.check_out_date }}
                  </div>
                </div>
              </v-card-item>
              <v-card-actions>
                <v-btn variant="elevated" color="primary" @click="editStay(stay)">
                  Редактировать
                </v-btn>
                <v-btn variant="outlined" color="error" @click="deleteStay(stay.stay_id)">
                  Удалить
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-btn variant="elevated" color="primary" @click="createStay">
          Добавить бронирование
        </v-btn>
      </v-card-actions>
    </v-card>

    <!-- Диалог редактирования профиля -->
    <v-dialog v-model="dialogProfile" max-width="500px" persistent>
      <v-card class="dialog-card pa-6">
        <v-card-title class="dialog-title text-h5">
          Редактирование профиля
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-text-field v-model="userForm.username" label="Имя пользователя"></v-text-field>
          <v-text-field v-model="userForm.email" label="Email"></v-text-field>
          <v-text-field v-model="userForm.password" label="Пароль" type="password"></v-text-field>
          <v-text-field v-model="userForm.password2" label="Повторите пароль" type="password"></v-text-field>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions class="d-flex justify-end">
          <v-btn variant="outlined" color="primary" class="mr-2" @click="saveProfile">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialogProfile = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Диалог редактирования/добавления клиента -->
    <v-dialog v-model="dialogClient" max-width="600px" persistent>
      <v-card class="dialog-card pa-6">
        <v-card-title class="dialog-title text-h5">
          {{ userClientData?.client_id ? 'Редактирование данных клиента' : 'Новые данные клиента' }}
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-text-field v-model="clientForm.passport_number" label="Паспорт"></v-text-field>
          <v-text-field v-model="clientForm.last_name" label="Фамилия"></v-text-field>
          <v-text-field v-model="clientForm.first_name" label="Имя"></v-text-field>
          <v-text-field v-model="clientForm.middle_name" label="Отчество"></v-text-field>
          <v-text-field v-model="clientForm.city_of_origin" label="Город"></v-text-field>
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions class="d-flex justify-end">
          <v-btn variant="outlined" color="primary" @click="saveClient">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialogClient = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Диалог создания/редактирования бронирования (Stay) -->
    <v-dialog v-model="dialogStay" max-width="600px" persistent>
      <v-card class="dialog-card pa-6">
        <v-card-title class="dialog-title text-h5">
          {{ stayForm.stay_id ? 'Редактирование бронирования' : 'Новое бронирование' }}
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-text-field
            v-if="clientForm.client_id"
            :value="clientForm.client_id"
            label="Ваш client_id"
            disabled
          />
          <v-select
            v-model="stayForm.room_id"
            :items="availableRooms"
            item-title="roomDisplay"
            item-value="room_id"
            :return-object="false"
            label="Номер"
          />
          <v-text-field
            v-model="stayForm.check_in_date"
            label="Дата заезда (YYYY-MM-DD)"
          />
          <v-text-field
            v-model="stayForm.check_out_date"
            label="Дата выезда (YYYY-MM-DD)"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateStay">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialogStay = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Profile',
  data() {
    return {
      userData: {},         // данные из /auth/users/me/
      userClientData: null, // данные клиента, связанные с пользователем
      userStays: [],        // список бронирований, связанных с пользователем
      dialogProfile: false,
      dialogClient: false,
      dialogStay: false,

      userForm: {
        username: '',
        email: '',
        password: '',
        password2: ''
      },
      clientForm: {
        client_id: null,
        passport_number: '',
        last_name: '',
        first_name: '',
        middle_name: '',
        city_of_origin: ''
      },
      stayForm: {
        stay_id: null,
        room_id: null,
        check_in_date: '',
        check_out_date: ''
      },

      availableRooms: []
    }
  },
  async created() {
    await this.fetchUserData()
    await this.fetchUserClientData()
    await this.fetchUserStays()
    await this.fetchRooms()
  },
  methods: {
    // --- [1] Профиль ---
    async fetchUserData() {
      const token = localStorage.getItem('token')
      try {
        const response = await axios.get('http://127.0.0.1:8000/auth/users/me/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.userData = response.data
      } catch (error) {
        console.error('Ошибка при загрузке данных пользователя', error)
      }
    },
    editProfile() {
      this.userForm = {
        username: this.userData.username,
        email: this.userData.email,
        password: '',
        password2: ''
      }
      this.dialogProfile = true
    },
    async saveProfile() {
      const token = localStorage.getItem('token')
      if (this.userForm.password !== this.userForm.password2) {
        console.error('Пароли не совпадают')
        return
      }
      try {
        await axios.put('http://127.0.0.1:8000/auth/users/me/', this.userForm, {
          headers: { Authorization: `Token ${token}` }
        })
        this.dialogProfile = false
        this.fetchUserData()
      } catch (error) {
        console.error('Ошибка при обновлении профиля', error)
      }
    },

    logout() {
      localStorage.removeItem('token')
      this.$router.push('/start')
    },

    // --- Клиентские данные ---
    async fetchUserClientData() {
      const token = localStorage.getItem('token')
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/clients/me/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.userClientData = response.data
        this.clientForm = { ...response.data }
      } catch (error) {
        console.error('Данные клиента не найдены или не созданы', error)
      }
    },
    createClientData() {
      this.clientForm = {
        client_id: null,
        passport_number: '',
        last_name: '',
        first_name: '',
        middle_name: '',
        city_of_origin: ''
      }
      this.dialogClient = true
    },
    editClient() {
      if (!this.userClientData) return
      this.clientForm = { ...this.userClientData }
      this.dialogClient = true
    },
    async saveClient() {
      const token = localStorage.getItem('token')
      try {
        if (this.clientForm.client_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/clients/${this.clientForm.client_id}/`,
            this.clientForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post('http://127.0.0.1:8000/api/clients/', this.clientForm, {
            headers: { Authorization: `Token ${token}` }
          })
        }
        this.dialogClient = false
        this.fetchUserClientData()
      } catch (error) {
        console.error('Ошибка при сохранении данных клиента', error)
      }
    },
    async deleteClient() {
      if (!this.userClientData?.client_id) return
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/clients/${this.userClientData.client_id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.userClientData = null
        this.clientForm = {
          client_id: null,
          passport_number: '',
          last_name: '',
          first_name: '',
          middle_name: '',
          city_of_origin: ''
        }
      } catch (error) {
        console.error('Ошибка при удалении клиента', error)
      }
    },

    // --- Бронирования (проживания) ---
    async fetchUserStays() {
      const token = localStorage.getItem('token')
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/stays/user/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.userStays = response.data
      } catch (error) {
        console.error('Ошибка при загрузке списка бронирований', error)
      }
    },
    async fetchRooms() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/rooms/', {
          headers: { Authorization: `Token ${token}` }
        })
        // Показываем только свободные номера: is_available === true
        const allRooms = res.data
        this.availableRooms = allRooms
          .filter(r => r.is_available)
          .map(room => ({
            ...room,
            roomDisplay: `Номер ${room.room_id}, Этаж ${room.floor}`
          }))
      } catch (error) {
        console.error('Ошибка при загрузке списка номеров', error)
      }
    },
    createStay() {
      this.stayForm = {
        stay_id: null,
        // Если у нас уже есть userClientData, берём его id
        client_id: this.clientForm.client_id || null,
        room_id: null,
        check_in_date: '',
        check_out_date: ''
      }
      this.dialogStay = true
    },
    editStay(stay) {
      this.stayForm = {
        stay_id: stay.stay_id,
        client_id: stay.client ? stay.client.client_id : null,
        room_id: stay.room ? stay.room.room_id : null,
        check_in_date: stay.check_in_date || '',
        check_out_date: stay.check_out_date || ''
      }
      this.dialogStay = true
    },
    async createOrUpdateStay() {
      const token = localStorage.getItem('token')
      try {
        if (this.stayForm.stay_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/stays/${this.stayForm.stay_id}/`,
            this.stayForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            'http://127.0.0.1:8000/api/stays/',
            this.stayForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialogStay = false
        this.fetchUserStays()
      } catch (error) {
        console.error('Ошибка при сохранении бронирования', error)
      }
    },
    async deleteStay(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/stays/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchUserStays()
      } catch (error) {
        console.error('Ошибка при удалении бронирования', error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `userData`: Информация о текущем пользователе.
  - `userClientData`: Информация о клиенте, связанном с пользователем.
  - `userStays`: Список бронирований пользователя.
  - `dialogProfile`, `dialogClient`, `dialogStay`: Управление состоянием диалоговых окон.
  - `userForm`, `clientForm`, `stayForm`: Формы для редактирования профиля, данных клиента и бронирований.
  - `availableRooms`: Список доступных номеров для бронирования.

- **Created Hook:**
  - Загружает данные пользователя, данные клиента, бронирования и список комнат при создании компонента.

- **Methods:**
  - **Профиль:**
    - `fetchUserData`: Получает данные текущего пользователя.
    - `editProfile`: Открывает диалог для редактирования профиля.
    - `saveProfile`: Сохраняет изменения профиля на сервере.
  
  - **Клиентские данные:**
    - `fetchUserClientData`: Получает данные клиента, связанные с пользователем.
    - `createClientData`: Открывает диалог для добавления данных клиента.
    - `editClient`: Открывает диалог для редактирования данных клиента.
    - `saveClient`: Сохраняет данные клиента на сервере.
    - `deleteClient`: Удаляет данные клиента.

  - **Бронирования:**
    - `fetchUserStays`: Получает список бронирований пользователя.
    - `fetchRooms`: Получает список доступных номеров.
    - `createStay`: Открывает диалог для создания нового бронирования.
    - `editStay`: Открывает диалог для редактирования существующего бронирования.
    - `createOrUpdateStay`: Создает или обновляет бронирование на сервере.
    - `deleteStay`: Удаляет бронирование.

---

### Clients.vue

Компонент управления клиентами. Позволяет просматривать, добавлять, редактировать и удалять клиентов, а также выполнять специальные запросы, такие как подсчет клиентов по городу и поиск пересекающихся проживаний.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Клиенты</h2>

    <!-- Кнопка для вызова count_by_city -->
    <v-card class="mb-4 pa-2 elevated-card">
      <v-card-title class="text-h6">Поиск клиентов по городу</v-card-title>
      <v-card-text>
        <v-text-field
          v-model="cityParam"
          label="Город"
        ></v-text-field>
        <v-btn variant="outlined" color="primary" @click="countByCity">
          Посчитать
        </v-btn>
      </v-card-text>
      <v-card-subtitle v-if="cityResult !== null">
        Найдено клиентов: {{ cityResult }}
      </v-card-subtitle>
    </v-card>

    <!-- Кнопка для вызова clients_shared_stay -->
    <v-card class="mb-4 pa-2 elevated-card">
      <v-card-title class="text-h6">Пересекающиеся проживания</v-card-title>
      <v-card-text>
        <v-text-field
          v-model="sharedClientId"
          label="client_id"
          type="number"
        ></v-text-field>
        <v-btn variant="outlined" color="primary" @click="clientsSharedStay">
          Посмотреть
        </v-btn>
      </v-card-text>
      <v-card-subtitle v-if="sharedClients.length > 0">
        <div class="mt-2">
          <div v-for="c in sharedClients" :key="c.client_id">
            {{ c.last_name }} {{ c.first_name }} (ID={{ c.client_id }})
          </div>
        </div>
      </v-card-subtitle>
    </v-card>

    <!-- Кнопка для добавления клиента -->
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openCreateDialog"
    >
      Добавить клиента
    </v-btn>

    <!-- Список клиентов -->
    <v-row>
      <v-col
        v-for="client in clients"
        :key="client.client_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="elevated-card mb-4 pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">{{ client.last_name }} {{ client.first_name }} {{ client.middle_name }}</div>
              <div class="text-subtitle-2 mb-2">
                Паспорт - {{ client.passport_number }}<br>
                Город - {{ client.city_of_origin }}
              </div>
              <div class="text-caption">
                Clinet ID - {{ client.client_id }}
              </div>
            </div>
          </v-card-item>
          <v-card-actions>
            <v-btn
              variant="outlined"
              color="primary"
              @click="editClient(client)"
            >
              Редактировать
            </v-btn>
            <v-btn
              variant="outlined"
              color="error"
              @click="deleteClient(client.client_id)"
            >
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалог для создания/редактирования клиента -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
    >
      <v-card class="dialog-card pa-6">
        <v-card-title class="dialog-title text-h5">
          {{ clientForm.client_id ? 'Редактирование клиента' : 'Новый клиент' }}
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="clientForm.passport_number"
            label="Паспорт"
          ></v-text-field>
          <v-text-field
            v-model="clientForm.last_name"
            label="Фамилия"
          ></v-text-field>
          <v-text-field
            v-model="clientForm.first_name"
            label="Имя"
          ></v-text-field>
          <v-text-field
            v-model="clientForm.middle_name"
            label="Отчество"
          ></v-text-field>
          <v-text-field
            v-model="clientForm.city_of_origin"
            label="Город"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateClient">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialog = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Clients',
  data() {
    return {
      clients: [],
      dialog: false,
      clientForm: {
        client_id: null,
        passport_number: '',
        last_name: '',
        first_name: '',
        middle_name: '',
        city_of_origin: ''
      },
      // Для count_by_city
      cityParam: '',
      cityResult: null,

      // Для clients_shared_stay
      sharedClientId: '',
      sharedClients: []
    }
  },
  async created() {
    this.fetchClients()
  },
  methods: {
    // Получение списка клиентов
    async fetchClients() {
      const token = localStorage.getItem('token')
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/clients/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.clients = response.data.results ? response.data.results : response.data
      } catch (error) {
        console.error(error)
      }
    },
    // Открытие диалога для создания клиента
    openCreateDialog() {
      this.clearForm()
      this.dialog = true
    },
    // Очистка формы клиента
    clearForm() {
      this.clientForm = {
        client_id: null,
        passport_number: '',
        last_name: '',
        first_name: '',
        middle_name: '',
        city_of_origin: ''
      }
    },
    // Редактирование клиента
    editClient(client) {
      this.clientForm = { ...client }
      this.dialog = true
    },
    // Создание или обновление клиента
    async createOrUpdateClient() {
      const token = localStorage.getItem('token')
      try {
        if (this.clientForm.client_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/clients/${this.clientForm.client_id}/`,
            this.clientForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            `http://127.0.0.1:8000/api/clients/`,
            this.clientForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialog = false
        this.fetchClients()
      } catch (error) {
        console.error(error)
      }
    },
    // Удаление клиента
    async deleteClient(clientId) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/clients/${clientId}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchClients()
      } catch (error) {
        console.error(error)
      }
    },
    // Подсчет клиентов по городу
    async countByCity() {
      const token = localStorage.getItem('token')
      if (!this.cityParam) return
      try {
        const resp = await axios.get(`http://127.0.0.1:8000/api/clients/count_by_city/?city=${this.cityParam}`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.cityResult = resp.data.client_count
      } catch (error) {
        console.error(error)
      }
    },
    // Получение пересекающихся проживаний
    async clientsSharedStay() {
      const token = localStorage.getItem('token')
      if (!this.sharedClientId) return
      try {
        const resp = await axios.get(`http://127.0.0.1:8000/api/clients/clients_shared_stay/?client_id=${this.sharedClientId}`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.sharedClients = resp.data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `clients`: Список всех клиентов.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования клиента.
  - `clientForm`: Объект формы клиента.
  - `cityParam`, `cityResult`: Параметры и результат для подсчета клиентов по городу.
  - `sharedClientId`, `sharedClients`: Параметры и результат для поиска пересекающихся проживаний.

- **Created Hook:**
  - Загружает список клиентов при создании компонента.

- **Methods:**
  - `fetchClients`: Получает список клиентов с сервера.
  - `openCreateDialog`: Открывает диалоговое окно для добавления нового клиента.
  - `clearForm`: Очищает форму клиента.
  - `editClient`: Открывает диалоговое окно для редактирования существующего клиента.
  - `createOrUpdateClient`: Создает нового клиента или обновляет существующего на сервере.
  - `deleteClient`: Удаляет клиента по `client_id`.
  - `countByCity`: Подсчитывает количество клиентов в указанном городе.
  - `clientsSharedStay`: Получает список клиентов с пересекающимися проживаниями.

---

### Rooms.vue

Компонент управления номерами. Позволяет просматривать, добавлять, редактировать и удалять номера гостиницы.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Номера</h2>
    
    <!-- Кнопка для добавления нового номера -->
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openCreateDialog"
    >
      Добавить номер
    </v-btn>

    <!-- Список номеров -->
    <v-row>
      <v-col
        v-for="room in rooms"
        :key="room.room_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="mb-4 elevated-card pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">
                {{ room.room_type }} - {{ room.is_available ? 'Свободен' : 'Занят' }}
              </div>
              <div class="text-subtitle-2 mb-2">
                Этаж - {{ room.floor }} <br>
                Стоимость в сутки - {{ room.cost_per_day }} <br>
                Телефон - {{ room.phone_number }}
              </div>
              <div class="text-caption">
                ID - {{ room.room_id }} 
              </div>
            </div>
          </v-card-item>
          <!-- Действия с номером -->
          <v-card-actions>
            <v-btn
              variant="outlined"
              color="primary"
              @click="editRoom(room)"
            >
              Редактировать
            </v-btn>
            <v-btn
              variant="outlined"
              color="error"
              @click="deleteRoom(room.room_id)"
            >
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалоговое окно для создания/редактирования номера -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
    >
      <v-card class="dialog-card pa-6">
        <!-- Заголовок диалога -->
        <v-card-title class="my-dialog-title dialog-title">
          {{ roomForm.room_id ? 'Редактирование номера' : 'Новый номер' }}
        </v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-select
            v-model="roomForm.room_type"
            :items="roomTypes"
            label="Тип номера"
            dense
          ></v-select>
          <v-text-field
            v-model="roomForm.cost_per_day"
            label="Стоимость в сутки"
            type="number"
            dense
          ></v-text-field>
          <v-text-field
            v-model="roomForm.floor"
            label="Этаж"
            type="number"
            dense
          ></v-text-field>
          <v-switch
            v-model="roomForm.is_available"
            label="Свободен?"
            hide-details
          ></v-switch>
          <v-text-field
            v-model="roomForm.phone_number"
            label="Телефон номера"
            dense
          ></v-text-field>
        </v-card-text>
        <v-divider></v-divider>
        <!-- Действия в диалоге -->
        <v-card-actions class="d-flex justify-end">
          <v-btn
            variant="outlined"
            color="primary"
            @click="createOrUpdateRoom"
            class="mr-2"
          >
            Сохранить
          </v-btn>
          <v-btn
            variant="outlined"
            color="error"
            @click="dialog = false"
          >
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Rooms',
  data() {
    return {
      rooms: [],
      dialog: false,
      roomForm: {
        room_id: null,
        room_type: 'SINGLE',
        cost_per_day: 0,
        floor: 1,
        is_available: true,
        phone_number: ''
      },
      roomTypes: ['SINGLE', 'DOUBLE', 'TRIPLE']
    }
  },
  async created() {
    this.fetchRooms()
  },
  methods: {
    async fetchRooms() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/rooms/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.rooms = res.data
      } catch (error) {
        console.error(error)
      }
    },
    openCreateDialog() {
      this.clearForm()
      this.dialog = true
    },
    clearForm() {
      this.roomForm = {
        room_id: null,
        room_type: 'SINGLE',
        cost_per_day: 0,
        floor: 1,
        is_available: true,
        phone_number: ''
      }
    },
    editRoom(room) {
      this.roomForm = { ...room }
      this.dialog = true
    },
    // Создание или обновление номера
    async createOrUpdateRoom() {
      const token = localStorage.getItem('token')
      try {
        if (this.roomForm.room_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/rooms/${this.roomForm.room_id}/`,
            this.roomForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            'http://127.0.0.1:8000/api/rooms/',
            this.roomForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialog = false
        this.fetchRooms()
      } catch (error) {
        console.error(error)
      }
    },
    // Удаление номера по ID
    async deleteRoom(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/rooms/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchRooms()
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `rooms`: Список всех номеров.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования номера.
  - `roomForm`: Объект формы номера.
  - `roomTypes`: Типы номеров.

- **Created Hook:**
  - Загружает список номеров при создании компонента.

- **Methods:**
  - `fetchRooms`: Получает список номеров с сервера.
  - `openCreateDialog`: Открывает диалоговое окно для добавления нового номера.
  - `clearForm`: Очищает форму номера.
  - `editRoom`: Открывает диалоговое окно для редактирования существующего номера.
  - `createOrUpdateRoom`: Создает новый номер или обновляет существующий на сервере.
  - `deleteRoom`: Удаляет номер по `room_id`.

---

### Stays.vue

Компонент управления проживаниями. Позволяет пользователям просматривать, добавлять, редактировать и удалять свои бронирования.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Проживания</h2>
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openCreateDialog"
    >
      Добавить проживание
    </v-btn>

    <v-row>
      <v-col
        v-for="stay in stays"
        :key="stay.stay_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="mb-4 elevated-card pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">
                {{ stay.client?.last_name }} {{ stay.client?.first_name }} {{ stay.client?.middle_name }} - Номер {{ stay.room?.room_id }}
              </div>
              <div class="text-subtitle-2 mb-2">
                Дата заезда: {{ stay.check_in_date }}<br/>
                Дата выезда: {{ stay.check_out_date }}
              </div>
              <div class="text-caption">
                Client ID: {{ stay.stay_id }} <br/>
                Stay ID - {{ stay.client?.client_id }}
              </div>
            </div>
          </v-card-item>
          <v-card-actions>
            <v-btn variant="outlined" color="primary" @click="editStay(stay)">
              Редактировать
            </v-btn>
            <v-btn variant="outlined" color="error" @click="deleteStay(stay.stay_id)">
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалог -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
    >
      <v-card class="dialog-card pa-6">
        <v-card-title class="my-dialog-title dialog-title">
          {{ stayForm.stay_id ? 'Редактирование проживания' : 'Новое проживание' }}
        </v-card-title>
        <v-card-text>
          <v-select
            v-model="stayForm.client_id"
            :items="availableClients"
            item-title="fullName"
            item-value="client_id"
            :return-object="false"
            label="Клиент"
          />
          <v-select
            v-model="stayForm.room_id"
            :items="availableRooms"
            item-title="roomDisplay"
            item-value="room_id"
            :return-object="false"
            label="Номер"
          />
          <v-text-field
            v-model="stayForm.check_in_date"
            label="Дата заезда (YYYY-MM-DD)"
          />
          <v-text-field
            v-model="stayForm.check_out_date"
            label="Дата выезда (YYYY-MM-DD)"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateStay">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialog = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Stays',
  data() {
    return {
      stays: [],
      availableClients: [],
      availableRooms: [],
      dialog: false,
      stayForm: {
        stay_id: null,
        client_id: null,
        room_id: null,
        check_in_date: '',
        check_out_date: ''
      }
    }
  },
  async created() {
    await this.fetchStays()
    await this.fetchClients()
    await this.fetchRooms()
  },
  methods: {
    async fetchStays() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/stays/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.stays = res.data
      } catch (error) {
        console.error(error)
      }
    },
    async fetchClients() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/clients/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.availableClients = res.data.map(client => ({
          ...client,
          fullName: `${client.last_name} ${client.first_name} ${client.middle_name}` || 'NoName'
        }))
      } catch (error) {
        console.error(error)
      }
    },
    async fetchRooms() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/rooms/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.availableRooms = res.data.map(room => ({
          ...room,
          roomDisplay: `Room ${room.room_id}`
        }))
      } catch (error) {
        console.error(error)
      }
    },
    openCreateDialog() {
      this.clearForm()
      this.dialog = true
    },
    clearForm() {
      this.stayForm = {
        stay_id: null,
        client_id: null,
        room_id: null,
        check_in_date: '',
        check_out_date: ''
      }
    },
    editStay(stay) {
      this.stayForm = {
        stay_id: stay.stay_id,
        client_id: stay.client ? stay.client.client_id : null,
        room_id: stay.room ? stay.room.room_id : null,
        check_in_date: stay.check_in_date || '',
        check_out_date: stay.check_out_date || ''
      }
      this.dialog = true
    },
    // Создание или обновление бронирования
    async createOrUpdateStay() {
      const token = localStorage.getItem('token')
      try {
        if (this.stayForm.stay_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/stays/${this.stayForm.stay_id}/`,
            this.stayForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            'http://127.0.0.1:8000/api/stays/',
            this.stayForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialog = false
        this.fetchStays()
      } catch (error) {
        console.error(error)
      }
    },
    // Удаление бронирования
    async deleteStay(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/stays/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchStays()
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `stays`: Список всех бронирований.
  - `availableClients`: Список доступных клиентов для выбора при бронировании.
  - `availableRooms`: Список доступных номеров для выбора при бронировании.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования бронирования.
  - `stayForm`: Объект формы бронирования.

- **Created Hook:**
  - Загружает список бронирований, клиентов и номеров при создании компонента.

- **Methods:**
  - `fetchStays`: Получает список бронирований с сервера.
  - `fetchClients`: Получает список клиентов с сервера.
  - `fetchRooms`: Получает список номеров с сервера.
  - `openCreateDialog`: Открывает диалоговое окно для добавления нового бронирования.
  - `clearForm`: Очищает форму бронирования.
  - `editStay`: Открывает диалоговое окно для редактирования существующего бронирования.
  - `createOrUpdateStay`: Создает новое бронирование или обновляет существующее на сервере.
  - `deleteStay`: Удаляет бронирование по `stay_id`.

---

### Cleanings.vue

Компонент управления уборками. Позволяет сотрудникам просматривать, добавлять, редактировать и удалять записи об уборках, а также выполнять поиск сотрудников по клиентским номерам и дням недели.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Уборки (CleaningExecution)</h2>

    <!-- Карточка для поиска сотрудников, убирающих номер клиента в определенный день недели -->
    <v-card class="mb-4 pa-2 elevated-card">
      <v-card-title class="text-h6">Кто убирал номер клиента в день недели</v-card-title>
      <v-card-text>
        <v-text-field
          v-model="cleaningParams.client_id"
          label="client_id"
          type="number"
          class="mb-2"
        ></v-text-field>
        <v-text-field
          v-model="cleaningParams.day_of_week"
          label="День недели (например, 'понедельник')"
        ></v-text-field>
        <v-btn variant="outlined" color="primary" @click="staffCleanedClientRoom">
          Посмотреть
        </v-btn>
      </v-card-text>
      <v-card-subtitle v-if="cleaningResult.length > 0">
        <div class="mt-2">
          <div
            v-for="st in cleaningResult"
            :key="st.staff_id"
          >
            {{ st.last_name }} {{ st.first_name }} (ID={{ st.staff_id }})
          </div>
        </div>
      </v-card-subtitle>
    </v-card>

    <!-- Кнопка для открытия диалога добавления новой уборки -->
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openDialog"
    >
      Добавить запись об уборке
    </v-btn>

    <!-- Список всех уборок -->
    <v-row>
      <v-col
        v-for="cleaning in cleanings"
        :key="cleaning.cleaning_execution_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="elevated-card mb-4 pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">
                Этаж - {{ cleaning.floor }} - {{ cleaning.time_and_day_of_week }}
              </div>
              <div class="text-subtitle-2 mb-2">
                Сотрудник - {{ cleaning.staff?.last_name }} {{ cleaning.staff?.first_name }} {{ cleaning.staff?.middle_name }} <br>
                Комната - {{ cleaning.room?.room_id }}
              </div>
              <div class="text-caption">
                Execution ID - {{ cleaning.cleaning_execution_id }} <br>
                Schedule ID - {{ cleaning.cleaning_schedule?.cleaning_schedule_id }}
              </div>
            </div>
          </v-card-item>
          <v-card-actions>
            <v-btn
              variant="outlined"
              color="primary"
              @click="editCleaning(cleaning)"
            >
              Редактировать
            </v-btn>
            <v-btn
              variant="outlined"
              color="error"
              @click="deleteCleaning(cleaning.cleaning_execution_id)"
            >
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалог для добавления/редактирования уборки -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
    >
      <v-card class="dialog-card pa-6">
        <v-card-title class="text-h5" style="color: #1867C0;">
          {{ cleaningForm.cleaning_execution_id ? 'Редактирование' : 'Новая' }} уборка
        </v-card-title>
        <v-card-text>
          <v-select
            v-model="cleaningForm.cleaning_schedule_id"
            :items="scheduleList"
            item-title="cleaningSchedule"
            item-value="cleaning_schedule_id"
            :return-object="false"
            label="Cleaning Schedule ID"
          />
          <v-select
            v-model="cleaningForm.staff_id"
            :items="staffList"
            item-title="fullName"
            item-value="staff_id"
            :return-object="false"
            label="Сотрудник"
          />
          <v-select
            v-model="cleaningForm.room_id"
            :items="roomsList"
            item-title="roomDisplay"
            item-value="room_id"
            label="Комната"
            :return-object="false"
            :allow-empty="true"
          />
          <v-text-field
            v-model="cleaningForm.floor"
            label="Этаж"
            type="number"
          />
          <v-text-field
            v-model="cleaningForm.time_and_day_of_week"
            label="День/время"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateCleaning">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialog = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Cleanings',
  data() {
    return {
      cleanings: [],
      dialog: false,
      cleaningForm: {
        cleaning_execution_id: null,
        cleaning_schedule_id: null, 
        staff_id: null,
        room_id: null,
        floor: 1,
        time_and_day_of_week: ''
      },
      scheduleList: [],
      staffList: [],
      roomsList: [],

      cleaningParams: {
        client_id: '',
        day_of_week: ''
      },
      cleaningResult: []
    }
  },
  async created() {
    await this.fetchCleanings()
    await this.fetchStaff()
    await this.fetchRooms()
    await this.fetchSchedules()
  },
  methods: {
    // Получение списка всех уборок
    async fetchCleanings() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get(`http://127.0.0.1:8000/api/cleaning-executions/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.cleanings = res.data.results ? res.data.results : res.data
      } catch (error) {
        console.error(error)
      }
    },
    // Получение списка расписаний уборок
    async fetchSchedules() {
      const token = localStorage.getItem('token')
      try {
        const resp = await axios.get('http://127.0.0.1:8000/api/cleaning-schedules/', {
          headers: { Authorization: `Token ${token}` }        
        })
        this.scheduleList = resp.data.map(item => ({
          ...item,
          cleaningSchedule: `${item.cleaning_schedule_id}`,
        }))
      } catch (error) {
        console.error(error)
      }
    },
    // Получение списка сотрудников
    async fetchStaff() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/staff/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.staffList = res.data.map(stuff => ({
          ...stuff,
          fullName: `${stuff.last_name} ${stuff.first_name} ${stuff.middle_name}` || 'NoName'
        }))
      } catch (error) {
        console.error(error)
      }
    },
    // Получение списка комнат
    async fetchRooms() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/rooms/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.roomsList = res.data.map(room => ({
          ...room,
          roomDisplay: `Room ${room.room_id}`
        }))
      } catch (error) {
        console.error(error)
      }
    },
    // Открытие диалога для добавления новой уборки
    openDialog() {
      this.cleaningForm = {
        cleaning_execution_id: null,
        cleaning_schedule_id: null,
        staff_id: null,
        room_id: null,
        floor: 1,
        time_and_day_of_week: ''
      }
      this.dialog = true
    },
    // Открытие диалога для редактирования уборки
    editCleaning(item) {
      this.cleaningForm = {
        cleaning_execution_id: item.cleaning_execution_id,
        cleaning_schedule_id: item.cleaning_schedule?.cleaning_schedule_id || null,
        staff_id: item.staff?.staff_id || null,
        room_id: item.room?.room_id || null,
        floor: item.floor,
        time_and_day_of_week: item.time_and_day_of_week
      }
      this.dialog = true
    },
    // Создание или обновление уборки
    async createOrUpdateCleaning() {
      const token = localStorage.getItem('token')
      try {
        if (this.cleaningForm.cleaning_execution_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/cleaning-executions/${this.cleaningForm.cleaning_execution_id}/`,
            this.cleaningForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            'http://127.0.0.1:8000/api/cleaning-executions/',
            this.cleaningForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialog = false
        this.fetchCleanings()
      } catch (error) {
        console.error(error)
      }
    },
    // Удаление уборки
    async deleteCleaning(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/cleaning-executions/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchCleanings()
      } catch (error) {
        console.error(error)
      }
    },
    // Поиск сотрудников, убирающих номер клиента в определенный день недели
    async staffCleanedClientRoom() {
      const token = localStorage.getItem('token')
      const { client_id, day_of_week } = this.cleaningParams
      if (!client_id || !day_of_week) return
      try {
        const resp = await axios.get(
          `http://127.0.0.1:8000/api/cleaning-executions/staff_cleaned_client_room/?client_id=${client_id}&day_of_week=${day_of_week}`,
          { headers: { Authorization: `Token ${token}` } }
        )
        this.cleaningResult = resp.data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `cleanings`: Список всех уборок.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования уборки.
  - `cleaningForm`: Объект формы уборки.
  - `scheduleList`, `staffList`, `roomsList`: Списки расписаний, сотрудников и комнат для выбора в формах.
  - `cleaningParams`, `cleaningResult`: Параметры и результат для поиска сотрудников по клиентскому номеру и дню недели.

- **Created Hook:**
  - Загружает список уборок, сотрудников, комнат и расписаний при создании компонента.

- **Methods:**
  - `fetchCleanings`: Получает список уборок с сервера.
  - `fetchSchedules`: Получает список расписаний уборок с сервера.
  - `fetchStaff`: Получает список сотрудников с сервера.
  - `fetchRooms`: Получает список комнат с сервера.
  - `openDialog`: Открывает диалоговое окно для добавления новой уборки.
  - `editCleaning`: Открывает диалоговое окно для редактирования существующей уборки.
  - `createOrUpdateCleaning`: Создает новую уборку или обновляет существующую на сервере.
  - `deleteCleaning`: Удаляет уборку по `cleaning_execution_id`.
  - `staffCleanedClientRoom`: Выполняет поиск сотрудников, убирающих номер клиента в определенный день недели.

---

### Schedules.vue

Компонент управления расписаниями уборок. Позволяет просматривать, добавлять, редактировать и удалять расписания уборок.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Расписания уборок</h2>
    
    <!-- Кнопка для добавления нового расписания -->
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openDialog"
    >
      Добавить расписание
    </v-btn>

    <!-- Список расписаний -->
    <v-row>
      <v-col
        v-for="schedule in schedules"
        :key="schedule.cleaning_schedule_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="mb-4 elevated-card pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">
                Этаж: {{ schedule.floor }} - {{ schedule.time_and_day_of_week }}
              </div>
              <div class="text-subtitle-2 mb-2">
                Комната: {{ schedule.room?.room_id }} <br>
                Сотрудник: {{ schedule.staff?.last_name }} {{ schedule.staff?.first_name }} {{ schedule.staff?.middle_name }}
              </div>
              <div class="text-caption">
                ID: {{ schedule.cleaning_schedule_id }}
              </div>
            </div>
          </v-card-item>
          
          <!-- Действия с расписанием -->
          <v-card-actions>
            <v-btn
              variant="outlined"
              color="primary"
              @click="editSchedule(schedule)"
            >
              Редактировать
            </v-btn>
            <v-btn
              variant="outlined"
              color="error"
              @click="deleteSchedule(schedule.cleaning_schedule_id)"
            >
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалоговое окно для создания или редактирования расписания -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
      >
      <v-card class="dialog-card pa-6">
        <v-card-title class="my-dialog-title dialog-title">
          {{ scheduleForm.cleaning_schedule_id ? 'Редактирование' : 'Новое' }} расписание
        </v-card-title>
        <v-card-text>
          <v-select
            v-model="scheduleForm.staff_id"
            :items="staffList"
            item-title="fullName"
            item-value="staff_id"
            :return-object="false"
            label="Сотрудник"
          />
          <v-select
            v-model="scheduleForm.room_id"
            :items="roomsList"
            item-title="roomDisplay"
            item-value="room_id"
            :return-object="false"
            label="Комната"
            :allow-empty="true"
          />
          <v-text-field
            v-model="scheduleForm.floor"
            label="Этаж"
            type="number"
          />
          <v-text-field
            v-model="scheduleForm.time_and_day_of_week"
            label="День/время"
          />
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateSchedule">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialog = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Schedules',
  data() {
    return {
      schedules: [],
      dialog: false,
      scheduleForm: {
        cleaning_schedule_id: null,
        staff_id: null,
        room_id: null,
        floor: 1,
        time_and_day_of_week: ''
      },
      staffList: [],
      roomsList: []
    }
  },
  async created() {
    await this.fetchSchedules()
    await this.fetchStaff()
    await this.fetchRooms()
  },
  methods: {
    // Получение списка расписаний
    async fetchSchedules() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/cleaning-schedules/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.schedules = res.data
      } catch (error) {
        console.error(error)
      }
    },
    // Получение списка сотрудников
    async fetchStaff() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/staff/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.staffList = res.data.map(stuff => ({
          ...stuff,
          fullName: `${stuff.last_name} ${stuff.first_name} ${stuff.middle_name}` || 'NoName'
        }))
      } catch (error) {
        console.error(error)
      }
    },
    // Получение списка комнат
    async fetchRooms() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/rooms/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.roomsList = res.data.map(room => ({
          ...room,
          roomDisplay: `Room ${room.room_id}`
        }))
      } catch (error) {
        console.error(error)
      }
    },
    // Открытие диалогового окна
    openDialog() {
      this.scheduleForm = {
        cleaning_schedule_id: null,
        staff_id: null,
        room_id: null,
        floor: 1,
        time_and_day_of_week: ''
      }
      this.dialog = true
    },
    // Редактирование расписания
    editSchedule(item) {
      this.scheduleForm = {
        cleaning_schedule_id: item.cleaning_schedule_id,
        staff_id: item.staff?.staff_id || null,
        room_id: item.room?.room_id || null,
        floor: item.floor,
        time_and_day_of_week: item.time_and_day_of_week
      }
      this.dialog = true
    },
    // Создание или обновление расписания
    async createOrUpdateSchedule() {
      const token = localStorage.getItem('token')
      try {
        if (this.scheduleForm.cleaning_schedule_id) {
          await axios.put(
            `http://127.0.0.1:8000/api/cleaning-schedules/${this.scheduleForm.cleaning_schedule_id}/`,
            this.scheduleForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        } else {
          await axios.post(
            'http://127.0.0.1:8000/api/cleaning-schedules/',
            this.scheduleForm,
            { headers: { Authorization: `Token ${token}` } }
          )
        }
        this.dialog = false
        this.fetchSchedules()
      } catch (error) {
        console.error(error)
      }
    },
    // Удаление расписания
    async deleteSchedule(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/cleaning-schedules/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchSchedules()
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `schedules`: Список всех расписаний уборок.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования расписания.
  - `scheduleForm`: Объект формы расписания.
  - `staffList`, `roomsList`: Списки сотрудников и комнат для выбора в формах.

- **Created Hook:**
  - Загружает список расписаний, сотрудников и комнат при создании компонента.

- **Methods:**
  - `fetchSchedules`: Получает список расписаний с сервера.
  - `fetchStaff`: Получает список сотрудников с сервера.
  - `fetchRooms`: Получает список комнат с сервера.
  - `openDialog`: Открывает диалоговое окно для добавления нового расписания.
  - `editSchedule`: Открывает диалоговое окно для редактирования существующего расписания.
  - `createOrUpdateSchedule`: Создает новое расписание или обновляет существующее на сервере.
  - `deleteSchedule`: Удаляет расписание по `cleaning_schedule_id`.

---

### Report.vue

Компонент для формирования и отображения отчетов по гостинице. Позволяет выбирать квартал и получать различные статистические данные.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Отчёт по гостинице</h2>
    <!-- Карточка для выбора квартала и кнопки формирования отчета -->
    <v-card variant="elevated" :elevation="4" class="pa-4 mb-4">
      <v-select
        :items="[1,2,3,4]"
        v-model="selectedQuarter"
        label="Выберите квартал"
        variant="outlined"
      ></v-select>
      <v-btn
        variant="outlined"
        color="primary"
        class="mt-2"
        @click="fetchReport"
      >
        Сформировать отчёт
      </v-btn>
    </v-card>

    <!-- Карточка для отображения данных отчета -->
    <v-card
      v-if="reportData"
      variant="elevated"
      class="pa-4 mb-4"
      :elevation="4"
    >
      <div class="mb-2">
        <strong>Число клиентов по номерам:</strong>
      </div>
      <v-list density="compact" v-for="item in reportData.stays_per_room" :key="item.room__room_id">
        <v-list-item>
          Номер {{ item.room__room_id }} — Клиентов: {{ item.client_count }}
        </v-list-item>
      </v-list>

      <div class="mb-2 mt-3">
        <strong>Количество номеров на этажах:</strong>
      </div>
      <v-list density="compact" v-for="floorInfo in reportData.rooms_per_floor" :key="floorInfo.floor">
        <v-list-item>
          Этаж: {{ floorInfo.floor }} — Номеров: {{ floorInfo.room_count }}
        </v-list-item>
      </v-list>

      <div class="mb-2 mt-3">
        <strong>Доход по каждому номеру:</strong>
      </div>
      <v-list density="compact" v-for="roomIncome in reportData.income_per_room" :key="roomIncome.room_id">
        <v-list-item>
          Номер {{ roomIncome.room_id }} — Доход: {{ roomIncome.total_income }}
        </v-list-item>
      </v-list>

      <div class="mt-3">
        <strong>Суммарный доход:</strong> {{ reportData.total_income }}
      </div>
    </v-card>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Report',
  data() {
    return {
      selectedQuarter: null, // Выбранный квартал
      reportData: null // Данные отчета
    }
  },
  methods: {
    async fetchReport() {
      if (!this.selectedQuarter) return
      const token = localStorage.getItem('token')
      try {
        const response = await axios.get(
          `http://127.0.0.1:8000/report/?quarter=${this.selectedQuarter}`, 
          { headers: { Authorization: `Token ${token}` } }
        )
        this.reportData = response.data
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `selectedQuarter`: Выбранный пользователем квартал для формирования отчета.
  - `reportData`: Полученные данные отчета.

- **Methods:**
  - `fetchReport`: Отправляет GET-запрос на сервер для получения отчета за выбранный квартал и сохраняет данные в `reportData`.

---

### Staff.vue

Компонент управления сотрудниками. Позволяет просматривать, добавлять, редактировать и удалять сотрудников гостиницы.

```vue
<template>
  <v-container>
    <h2 class="mb-4">Сотрудники</h2>
    <!-- Кнопка для открытия диалога создания сотрудника -->
    <v-btn
      variant="outlined"
      color="primary"
      class="mb-4"
      @click="openCreateDialog"
    >
      Нанять сотрудника
    </v-btn>

    <v-row>
      <v-col
        v-for="(person, index) in staff"
        :key="person.staff_id"
        cols="12"
        md="6"
        lg="4"
      >
        <v-card class="mb-4 elevated-card pa-2">
          <v-card-item>
            <div>
              <div class="text-h6">{{ person.last_name }} {{ person.first_name }} {{ person.middle_name }}</div>
              <div class="text-caption" v-if="person.middle_name">
                ID: {{ person.staff_id }}
              </div>
            </div>
          </v-card-item>
          <v-card-actions>
            <v-btn
              variant="outlined"
              color="primary"
              @click="editStaff(person)"
            >
              Редактировать
            </v-btn>
            <v-btn
              variant="outlined"
              color="error"
              @click="deleteStaff(person.staff_id)"
            >
              Удалить
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>

    <!-- Диалог для редактирования/добавления -->
    <v-dialog
      v-model="dialog"
      max-width="600px"
      persistent
      >
      <v-card class="dialog-card pa-6">
        <v-card-title class="my-dialog-title dialog-title">
          {{ staffForm.staff_id ? 'Редактирование' : 'Новый' }} сотрудник
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="staffForm.last_name" label="Фамилия" />
          <v-text-field v-model="staffForm.first_name" label="Имя" />
          <v-text-field v-model="staffForm.middle_name" label="Отчество" />
        </v-card-text>
        <v-card-actions>
          <v-btn variant="outlined" color="primary" @click="createOrUpdateStaff">
            Сохранить
          </v-btn>
          <v-btn variant="outlined" color="error" @click="dialog = false">
            Отмена
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Staff',
  data() {
    return {
      staff: [],
      dialog: false,
      staffForm: {
        staff_id: null,
        last_name: '',
        first_name: '',
        middle_name: ''
      }
    }
  },
  async created() {
    this.fetchStaff()
  },
  methods: {
    // Метод для получения списка сотрудников
    async fetchStaff() {
      const token = localStorage.getItem('token')
      try {
        const res = await axios.get('http://127.0.0.1:8000/api/staff/', {
          headers: { Authorization: `Token ${token}` }
        })
        this.staff = res.data
      } catch (error) {
        console.error(error)
      }
    },
    // Открытие диалога для создания нового сотрудника
    openCreateDialog() {
      this.clearForm()
      this.dialog = true
    },
    // Очистка формы
    clearForm() {
      this.staffForm = {
        staff_id: null,
        last_name: '',
        first_name: '',
        middle_name: ''
      }
    },
    // Открытие диалога для редактирования существующего сотрудника
    editStaff(person) {
      this.staffForm = { ...person }
      this.dialog = true
    },
    // Метод для создания или обновления сотрудника
    async createOrUpdateStaff() {
      const token = localStorage.getItem('token')
      try {
        if (this.staffForm.staff_id) {
          await axios.put(`http://127.0.0.1:8000/api/staff/${this.staffForm.staff_id}/`, this.staffForm, {
            headers: { Authorization: `Token ${token}` }
          })
        } else {
          await axios.post('http://127.0.0.1:8000/api/staff/', this.staffForm, {
            headers: { Authorization: `Token ${token}` }
          })
        }
        this.dialog = false
        this.fetchStaff()
      } catch (error) {
        console.error(error)
      }
    },
    // Метод для удаления сотрудника
    async deleteStaff(id) {
      const token = localStorage.getItem('token')
      try {
        await axios.delete(`http://127.0.0.1:8000/api/staff/${id}/`, {
          headers: { Authorization: `Token ${token}` }
        })
        this.fetchStaff()
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
```

**Описание:**

- **Data:**
  - `staff`: Список всех сотрудников.
  - `dialog`: Управление состоянием диалогового окна для добавления/редактирования сотрудника.
  - `staffForm`: Объект формы сотрудника.

- **Created Hook:**
  - Загружает список сотрудников при создании компонента.

- **Methods:**
  - `fetchStaff`: Получает список сотрудников с сервера.
  - `openCreateDialog`: Открывает диалоговое окно для добавления нового сотрудника.
  - `clearForm`: Очищает форму сотрудника.
  - `editStaff`: Открывает диалоговое окно для редактирования существующего сотрудника.
  - `createOrUpdateStaff`: Создает нового сотрудника или обновляет существующего на сервере.
  - `deleteStaff`: Удаляет сотрудника по `staff_id`.

---

### Start.vue

Стартовая страница приложения, предлагающая пользователю выбрать между входом в систему и регистрацией.

```vue
<template>
  <v-container class="d-flex align-center justify-center fill-height">
    <v-card class="pa-5 elevated-card" width="400">
      <h2 class="text-center mb-4">Добро пожаловать</h2>
      <p class="text-center">
        Пожалуйста, выберите, что вы хотите сделать:
      </p>
      <v-btn
        variant="elevated"
        color="primary"
        class="mb-2"
        block
        @click="goToLogin"
      >
        Войти
      </v-btn>
      <v-btn
        variant="flat"
        color="warning"
        block
        @click="goToRegister"
      >
        Зарегистрироваться
      </v-btn>
    </v-card>
  </v-container>
</template>

<script>
export default {
  name: 'Start',
  methods: {
    goToLogin() {
      this.$router.push('/login')
    },
    goToRegister() {
      this.$router.push('/register')
    }
  }
}
</script>
```

**Описание:**

- Простой интерфейс, предлагающий пользователю выбрать между входом в систему и регистрацией.
- Кнопки `Войти` и `Зарегистрироваться` перенаправляют пользователя на соответствующие страницы.

---

## Routing

Маршрутизация настроена с использованием `vue-router`. Определены маршруты для всех основных страниц приложения.

```javascript
// router.js
import { createRouter, createWebHistory } from 'vue-router'

import Start from '@/views/Start.vue'
import Home from '@/views/Home.vue'
import Login from '@/views/Login.vue'
import Register from '@/views/Register.vue'
import Profile from '@/views/Profile.vue'
import Clients from '@/views/Clients.vue'
import Staff from '@/views/Staff.vue'
import Rooms from '@/views/Rooms.vue'
import Stays from '@/views/Stays.vue'
import Cleanings from '@/views/Cleanings.vue'
import Schedules from '@/views/Schedules.vue'
import Report from '@/views/Report.vue'

const routes = [
  { path: '/', name: 'home', component: Home },
  { path: '/start', name: 'start', component: Start },
  { path: '/login', name: 'login', component: Login },
  { path: '/register', name: 'register', component: Register },
  { path: '/profile', name: 'profile', component: Profile },
  { path: '/clients', name: 'clients', component: Clients },
  { path: '/staff', name: 'staff', component: Staff },
  { path: '/rooms', name: 'rooms', component: Rooms },
  { path: '/stays', name: 'stays', component: Stays },
  { path: '/cleanings', name: 'cleanings', component: Cleanings },
  { path: '/schedules', name: 'schedules', component: Schedules },
  { path: '/report', name: 'report', component: Report }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

// Перехватчик маршрутов для проверки авторизации
router.beforeEach((to, from, next) => {
  const token = localStorage.getItem('token')
  // Если пользователь не авторизован и не на /login, /register или /start, перенаправляем на /start
  if (!token && to.name !== 'login' && to.name !== 'register' && to.name !== 'start') {
    next('/start')
  } else {
    next()
  }
})
  
export default router
```

**Описание:**

- **Routes:**
  - Определены маршруты для всех основных страниц приложения, таких как `Home`, `Login`, `Register`, `Profile`, `Clients`, `Staff`, `Rooms`, `Stays`, `Cleanings`, `Schedules`, `Report` и `Start`.

- **Перехватчик маршрутов:**
  - Проверяет наличие токена авторизации в `localStorage`.
  - Если токен отсутствует и пользователь пытается перейти на защищенную страницу, перенаправляет его на страницу `Start`.

---

## Auth

Авторизация реализована с использованием токенов. После успешного входа пользователя получает токен, который сохраняется в `localStorage`. Этот токен используется для аутентификации последующих запросов к серверу.

### Компоненты авторизации

- **Login.vue**: Отправляет логин и пароль на сервер для получения токена.
- **Register.vue**: Позволяет создать новую учетную запись.
- **NavigationBar.vue** и **Profile.vue**: Используют токен для получения и отображения информации о пользователе.

### Пример использования токена

В каждом компоненте, который взаимодействует с сервером, токен добавляется в заголовки запросов:

```javascript
const token = localStorage.getItem('token')
const response = await axios.get('http://127.0.0.1:8000/auth/users/me/', {
  headers: { Authorization: `Token ${token}` }
})
```

---

## Integration API

Приложение взаимодействует с сервером через REST API, предоставляемый Django REST framework. Для выполнения запросов используется библиотека `axios`.

### Основные эндпоинты

- **Авторизация и регистрация:**
  - `POST /auth/token/login/`: Вход в систему, получение токена.
  - `POST /auth/users/`: Регистрация нового пользователя.
  - `GET /auth/users/me/`: Получение информации о текущем пользователе.
  - `PUT /auth/users/me/`: Обновление информации о текущем пользователе.

- **Клиенты:**
  - `GET /api/clients/`: Получение списка клиентов.
  - `POST /api/clients/`: Создание нового клиента.
  - `PUT /api/clients/{client_id}/`: Обновление клиента.
  - `DELETE /api/clients/{client_id}/`: Удаление клиента.
  - Дополнительные эндпоинты для специальных запросов:
    - `GET /api/clients/count_by_city/`: Подсчет клиентов по городу.
    - `GET /api/clients/clients_shared_stay/`: Поиск пересекающихся проживаний.

- **Номера:**
  - `GET /api/rooms/`: Получение списка номеров.
  - `POST /api/rooms/`: Создание нового номера.
  - `PUT /api/rooms/{room_id}/`: Обновление номера.
  - `DELETE /api/rooms/{room_id}/`: Удаление номера.

- **Бронирования:**
  - `GET /api/stays/`: Получение списка бронирований.
  - `POST /api/stays/`: Создание нового бронирования.
  - `PUT /api/stays/{stay_id}/`: Обновление бронирования.
  - `DELETE /api/stays/{stay_id}/`: Удаление бронирования.
  - `GET /api/stays/user/`: Получение бронирований текущего пользователя.

- **Уборки:**
  - `GET /api/cleaning-executions/`: Получение списка уборок.
  - `POST /api/cleaning-executions/`: Создание новой уборки.
  - `PUT /api/cleaning-executions/{cleaning_execution_id}/`: Обновление уборки.
  - `DELETE /api/cleaning-executions/{cleaning_execution_id}/`: Удаление уборки.
  - Дополнительные эндпоинты для специальных запросов.

- **Расписания уборок:**
  - `GET /api/cleaning-schedules/`: Получение списка расписаний уборок.
  - `POST /api/cleaning-schedules/`: Создание нового расписания.
  - `PUT /api/cleaning-schedules/{cleaning_schedule_id}/`: Обновление расписания.
  - `DELETE /api/cleaning-schedules/{cleaning_schedule_id}/`: Удаление расписания.

- **Сотрудники:**
  - `GET /api/staff/`: Получение списка сотрудников.
  - `POST /api/staff/`: Создание нового сотрудника.
  - `PUT /api/staff/{staff_id}/`: Обновление сотрудника.
  - `DELETE /api/staff/{staff_id}/`: Удаление сотрудника.

- **Отчеты:**
  - `GET /report/?quarter={quarter}`: Получение отчета за указанный квартал.

### Пример запроса с использованием axios

```javascript
const token = localStorage.getItem('token')
try {
  const response = await axios.get('http://127.0.0.1:8000/api/clients/', {
    headers: { Authorization: `Token ${token}` }
  })
  this.clients = response.data.results ? response.data.results : response.data
} catch (error) {
  console.error(error)
}
```

---

## Vuetify

Vuetify — это популярный UI-фреймворк для Vue.js, используемый в данном проекте для создания адаптивных и эстетически приятных интерфейсов.

### Подключение Vuetify

```javascript
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

import 'vuetify/styles'
import { createVuetify } from 'vuetify'
import * as components from 'vuetify/components'
import * as directives from 'vuetify/directives'

// Подключаем глобальные стили
import '@/assets/hotel-styles.css'

// Тема
const myHotelTheme = {
  dark: false,
  colors: {
    background: '#FAFAFA',
    surface: '#FFFFFF',
    primary: '#4A90E2',
    'primary-darken-1': '#357ABD',
    secondary: '#7BDCB5',
    'secondary-darken-1': '#57C09C',
    error: '#D32F2F',
    info: '#2D9CDB',
    success: '#27AE60',
    warning: '#CFCFCF',
  },
  variables: {
    'border-color': '#000000',
    'border-opacity': 0.12,
  }
}

const vuetify = createVuetify({
  components,
  directives,
  theme: {
    defaultTheme: 'myHotelTheme',
    themes: {
      myHotelTheme
    }
  }
})

createApp(App)
  .use(router)
  .use(vuetify)
  .mount('#app')
```

### Использование компонентов Vuetify

В каждом компоненте используется различные компоненты Vuetify, такие как `v-container`, `v-card`, `v-btn`, `v-text-field`, `v-dialog`, `v-select`, `v-switch`, и другие для создания интерфейса.

**Пример использования кнопки Vuetify:**

```vue
<v-btn variant="outlined" color="primary" @click="createOrUpdateClient">
  Сохранить
</v-btn>
```

**Описание:**

- `variant`: Определяет стиль кнопки (например, `outlined`, `elevated`).
- `color`: Задает цвет кнопки.
- `@click`: Обработчик события клика.

---


## NavigationBar.vue

Навигационная панель, отображающаяся на всех страницах после авторизации пользователя.

**Код:**

```vue
<template>
  <v-app-bar elevation="8" color="#a5f5f4">
    <!-- Контент -->
  </v-app-bar>
</template>

<script>
export default {
  // Логика компонента
}
</script>
```

**Описание:**

- **Data:** `token`, `userData`
- **Methods:** `fetchUser`, `logout`
```
```
#### api.md

# API

Приложение взаимодействует с сервером через REST API, предоставляемый Django REST framework.

## Edpoints

- **Клиенты:**
  - `GET /api/clients/`
  - `POST /api/clients/`
  - `PUT /api/clients/{client_id}/`
  - `DELETE /api/clients/{client_id}/`
  - `GET /api/clients/count_by_city/`
  - `GET /api/clients/clients_shared_stay/`

- **Номера:**
  - `GET /api/rooms/`
  - `POST /api/rooms/`
  - `PUT /api/rooms/{room_id}/`
  - `DELETE /api/rooms/{room_id}/`

- **Бронирования:**
  - `GET /api/stays/`
  - `POST /api/stays/`
  - `PUT /api/stays/{stay_id}/`
  - `DELETE /api/stays/{stay_id}/`
  - `GET /api/stays/user/`

- **Уборки:**
  - `GET /api/cleaning-executions/`
  - `POST /api/cleaning-executions/`
  - `PUT /api/cleaning-executions/{cleaning_execution_id}/`
  - `DELETE /api/cleaning-executions/{cleaning_execution_id}/`

- **Расписания уборок:**
  - `GET /api/cleaning-schedules/`
  - `POST /api/cleaning-schedules/`
  - `PUT /api/cleaning-schedules/{cleaning_schedule_id}/`
  - `DELETE /api/cleaning-schedules/{cleaning_schedule_id}/`

- **Сотрудники:**
  - `GET /api/staff/`
  - `POST /api/staff/`
  - `PUT /api/staff/{staff_id}/`
  - `DELETE /api/staff/{staff_id}/`

- **Отчеты:**
  - `GET /report/?quarter={quarter}`

#### authentication.md

# Авторизация и Аутентификация

Авторизация реализована с использованием токенов. После успешного входа пользователя получает токен, который сохраняется в `localStorage`. Этот токен используется для аутентификации последующих запросов к серверу.

## Процесс авторизации

1. **Вход в систему:**
   - Пользователь вводит логин и пароль на странице `Login.vue`.
   - Отправляется POST-запрос на эндпоинт `/auth/token/login/`.
   - Сервер возвращает токен, который сохраняется в `localStorage`.

2. **Регистрация:**
   - Пользователь вводит необходимые данные на странице `Register.vue`.
   - Отправляется POST-запрос на эндпоинт `/auth/users/`.
   - При успешной регистрации перенаправляет пользователя на страницу входа.

3. **Использование токена:**
   - При каждом запросе к защищенным эндпоинтам токен добавляется в заголовки запросов.
   - Если токен отсутствует или недействителен, пользователь перенаправляется на страницу входа.

## Пример использования токена

```js
const token = localStorage.getItem('token')
const response = await axios.get('http://127.0.0.1:8000/auth/users/me/', {
  headers: { Authorization: `Token ${token}` }
})
```

---

## Использование Vuetify

Vuetify используется для создания адаптивного и эстетически приятного интерфейса. Все компоненты оформлены с использованием стилей и компонентов Vuetify.

### Настройка темы

В файле `main.js` настроена пользовательская тема:

```js
const myHotelTheme = {
  dark: false,
  colors: {
    background: '#FAFAFA',
    surface: '#FFFFFF',
    primary: '#4A90E2',
    'primary-darken-1': '#357ABD',
    secondary: '#7BDCB5',
    'secondary-darken-1': '#57C09C',
    error: '#D32F2F',
    info: '#2D9CDB',
    success: '#27AE60',
    warning: '#CFCFCF',
  },
  variables: {
    'border-color': '#000000',
    'border-opacity': 0.12,
  }
}
```

### Пример использования компонента Vuetify

**Кнопка:**

```vue
<v-btn variant="outlined" color="primary" @click="saveProfile">
  Сохранить
</v-btn>
```

**Описание:**

- `variant`: Определяет стиль кнопки (`outlined`, `elevated` и т.д.).
- `color`: Задает цвет кнопки.
- `@click`: Обработчик события клика.

**Текстовое поле:**

```vue
<v-text-field v-model="form.username" label="Логин" required></v-text-field>
```

**Описание:**

- `v-model`: Двусторонняя привязка данных.
- `label`: Метка поля ввода.
- `required`: Поле обязательно для заполнения.

---


## Заключение

Проект **Hotel System** представляет собой полнофункциональное веб-приложение для управления гостиницей, включающее управление клиентами, сотрудниками, номерами, бронированиями и уборками. Использование Vue.js и Vuetify обеспечивает интуитивно понятный и адаптивный интерфейс, а интеграция с Django REST framework позволяет эффективно взаимодействовать с серверной частью. Документация, созданная с помощью MkDocs, предоставляет подробные инструкции и описания всех аспектов проекта, облегчая его использование и дальнейшую разработку.
