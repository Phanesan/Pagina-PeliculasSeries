<script setup>
import axios from 'axios'
import { ref } from 'vue'
</script>

<template>
  <div class="loginForm">
    <h1>Iniciar Sesión</h1>

    <div class="inputs">
      <div class="label">
        <label for="username">Usuario</label>
        <input
          type="username"
          name="username"
          id="username"
          v-model="username"
        />
      </div>

      <div class="label">
        <label for="password">Contraseña</label>
        <input
          type="password"
          name="password"
          id="password"
          v-model="password"
        />
      </div>

      <div class="errorSection">
        <p v-if="error">Credenciales incorrectas</p>
      </div>

      <div class="button">
        <button @click="sendLogin()">Iniciar Sesión</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'LoginView',
  data() {
    const error = ref(false)

    return {
      username: '',
      password: '',
      error,
    }
  },
  methods: {
    sendLogin() {
      const api_key = import.meta.env.VITE_API_KEY;

      let config = {
        method: 'get',
        maxBodyLength: Infinity,
        url:
          'https://api.themoviedb.org/3/authentication/token/new?api_key=' +
          api_key,
        headers: {},
      }

      axios
        .request(config)
        .then(response => {
          let data = JSON.stringify({
            username: this.username,
            password: this.password,
            request_token: response.data.request_token,
          })

          let config = {
            method: 'post',
            maxBodyLength: Infinity,
            url:
              'https://api.themoviedb.org/3/authentication/token/validate_with_login?api_key=' +
              api_key,
            headers: {
              'Content-Type': 'application/json',
            },
            data: data,
          }

          axios
            .request(config)
            .then(response => {
              localStorage.setItem('payload', JSON.stringify(response.data))

              let data = JSON.stringify({
                request_token: response.data.request_token,
              })

              let config = {
                method: 'post',
                maxBodyLength: Infinity,
                url:
                  'https://api.themoviedb.org/3/authentication/session/new?api_key=' +
                  api_key,
                headers: {
                  'Content-Type': 'application/json',
                },
                data: data,
              }

              axios
                .request(config)
                .then(response => {
                  localStorage.setItem('session_id', response.data.session_id)
                  this.$emit('changePage', 'Home')
                })
                .catch(error => {
                  console.log(error)
                  this.error = true
                })
            })
            .catch(error => {
              console.log(error)
              this.error = true
            })
        })
        .catch(error => {
          console.log(error)
          this.error = true
        })
    },
  },
}
</script>

<style>
.view {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100vw;
}

.loginForm {
  background-color: var(--color-secondary);
  color: var(--color-text);
  width: 350px;
  height: 450px;
  margin-top: 13vh;
  border-radius: 1rem;
  border: 1px solid var(--color-text);

  h1 {
    text-align: center;
    padding-top: 1rem;
  }

  .inputs {
    display: flex;
    flex-direction: column;
    padding: 2rem;
    padding-top: 3rem;

    .label {
      display: flex;
      flex-direction: column;
      margin-bottom: 2rem;

      label {
        font-size: 1.1em;
        padding-bottom: 0.3rem;
      }

      input {
        font-size: 1.3em;
        border-radius: 0.4rem;
      }
    }

    .errorSection {
      p {
        color: red;
        text-align: center;
      }
    }

    button {
      background-color: var(--success);
      color: black;
      font-size: 1.2em;
      padding-top: 0.4rem;
      padding-bottom: 0.4rem;
      margin-top: 4.5rem;
      width: 100%;
      border-radius: 0.5rem;
      border: 1px solid black;
      cursor: pointer;
    }
  }
}
</style>
