<template>
  <div>
    <base-dialog
      :show="!!hiba"
      :title="hiba === 'Tiltás' ? 'Jelenleg ki vagy tiltva az oldalról' : 'Hibás kitöltés'"
      btn="info"
      type="danger"
      @close="bezar"
    >
      <ul v-if="typeof hiba === 'object'" class="list-unstyled pb-3">
        <li v-for="h in hiba" :key="h" class="p-1">{{ h }}</li>
      </ul>
      <p v-else class="p-1">
        <span v-if="hiba === 'Tiltás'">
          A tiltást valószínűleg egy nem megengedett kihágásodnak köszönheted.
          <br />
          <br />
          <small>
            Ha úgy érzed a tiltásod nem szabályos kérjük vedd fel a kapcsolatot az
            ügyfélszolgálattal!
          </small>
        </span>
        <span v-else>{{ hiba }}</span>
      </p>
    </base-dialog>
    <div
      v-if="mode"
      class="reg d-flex flex-md-row flex-column justify-content-center justify-content-md-around align-items-center flex-grow-1"
    >
      <form @submit.prevent="submit">
        <base-input v-model="reg.fullName" type="text" pattern="fullName" icon="signature">
          Teljes név
        </base-input>
        <base-input v-model="reg.userName" type="text" pattern="name" icon="user">
          Felhasználó név
        </base-input>
        <base-input v-model="reg.email" type="email" pattern="email" icon="at">
          E-mail
        </base-input>
        <base-input v-model="reg.phoneNumber" type="phone" pattern="phone" icon="phone">
          Telefonszám
        </base-input>
        <base-input v-model="reg.password" type="password" pattern="password" icon="lock">
          Jelszó
        </base-input>
        <base-input v-model="reg.pass2" type="password" pattern="password" icon="lock">
          Jelszó megismétlése
        </base-input>
        <base-button type="info" submit>Tovább</base-button>
      </form>
      <!-- <hr class="border-dark bg-dark text-dark p-1" /> -->
      <div class="separator m-3"></div>
      <div class="Social">
        <button
          class="btn google w-75 text-light d-flex flex-row align-items-center p-0 pe-1"
          @click="google"
        >
          <img :src="Google" alt="Google brand logo" class="bg-light google-logo me-3" />
          <div class="">Belépés Google-lal</div>
        </button>
      </div>
    </div>
    <div
      v-else
      class="login d-flex flex-md-row flex-column justify-content-center justify-content-md-around align-items-center flex-grow-1"
    >
      <div>
        <form @submit.prevent="submit">
          <base-input v-model="login.email" pattern="email" icon="at">
            E-mail
          </base-input>
          <base-input v-model="login.password" type="password" pattern="password" icon="lock">
            Jelszó
          </base-input>
          <base-button type="info" submit>Bejelentkezés</base-button>
        </form>
      </div>
      <div class="separator m-3"></div>
      <div class="Social">
        <button class="btn google w-75 text-light d-flex flex-row align-items-center p-0 pe-1">
          <img :src="Google" alt="Google brand logo" class="bg-light google-logo me-3" />
          <div class="bg-danger">MAJD social belépés</div>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Google from '../assets/pics/brand/googleLogo.svg';
import axios from '@/config/axios.js';
import { useRoute, useRouter } from 'vue-router';
import { useStore } from 'vuex';
import { computed, reactive, ref } from 'vue';

export default {
  name: 'Auth',
  setup() {
    const store = useStore();
    const route = useRoute();
    const router = useRouter();
    const mode = computed(() => {
      return route.query.mode === 'reg';
    });

    const hiba = ref();
    function bezar() {
      hiba.value = '';
    }
    //TODO Regisztrációhoz google reCaptcha
    //TODO Google belépés
    const reg = reactive({
      fullName: '',
      userName: '',
      email: '',
      phoneNumber: '',
      password: '',
      pass2: '',
    });
    const login = reactive({
      email: 'alex@alex.hu',
      password: 'Alex1234',
    });
    async function submit() {
      if (!mode.value) {
        axios
          .post('/users/signin', { email: login.email, password: login.password })
          .then(res => {
            store.dispatch('changeAuth', { token: res.data.accessToken });
            router.replace(route.query.redirect ? `/${route.query.redirect}` : '/home');
          })
          .catch(err => {
            hiba.value = err.response.data.message;
          });
      } else {
        axios
          .post('/users/signup', {
            email: reg.email,
            password: reg.password,
            name: reg.userName,
            fullName: reg.fullName,
            phoneNumber: reg.phoneNumber,
          })
          .then(res => {
            store.dispatch('changeAuth', { token: res.data.accessToken });
            router.replace(route.query.redirect ? `/${route.query.redirect}` : '/home');
          })
          .catch(err => {
            hiba.value = err.response.data.message;
          });
      }
    }

    return { mode, submit, reg, login, Google, hiba, bezar };
  },
};
</script>

<style scoped lang="scss">
.google {
  background-color: #4285f4;
}
.google-logo {
  width: 15%;
}

$szin1: #009fe6;
$szin2: #00b0ff;
$betuszin: #333;
$h2szin: #666;
$feher: #fff;
.container {
  width: 100vw;
  height: 100vh;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 7rem;
  padding: 0.2rem;
  .img {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    img {
      width: 500px;
      @media screen and (max-width: 1000px) {
        width: 400px;
      }
    }
    @media screen and (max-width: 900px) {
      display: none;
    }
  }
  .login-container {
    display: flex;
    align-items: center;
    text-align: center;
    form {
      width: 360px;
      .avatar {
        width: 100px;
      }
      h1 {
        font-size: 2.6rem;
        text-transform: uppercase;
        margin: 15px 0;
        color: $betuszin;
        @media screen and (max-width: 1000px) {
          font-size: 2.4rem;
          margin: 8px 0;
        }
      }
      .input-div {
        position: relative;
        display: grid;
        grid-template-columns: 7% 93%;
        margin: 25px 0;
        padding: 5px 0;
        border-bottom: 2px solid #ccc;
        &:after,
        &:before {
          content: '';
          position: absolute;
          bottom: -2px;
          width: 0;
          height: 2px;
          background-color: $szin1;
          transition: 0.3s;
        }
        &:after {
          right: 50%;
        }
        &:before {
          left: 50%;
        }
        &.one {
          margin-top: 0;
        }
        &.two {
          margin-bottom: 4px;
        }
        &.focus {
          &:after,
          &:before {
            width: 50%;
          }
          .i em {
            color: $szin1;
          }
          div h2 {
            top: -5px;
            font-size: 15px;
          }
        }
        .i {
          display: flex;
          justify-content: center;
          align-items: center;
          em {
            color: $h2szin;
            transition: 0.3s;
          }
        }
        & > div {
          position: relative;
          height: 45px;
          h2 {
            position: absolute;
            left: 10px;
            top: 50%;
            transform: translateY(-50%);
            color: $h2szin;
            font-size: 18px;
            transition: 0.3s;
          }
          .input {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 0;
            left: 0;
            border: none;
            background: none;
            padding: 0.5rem;
            font-size: 1.2rem;
            color: $h2szin;
            font-family: 'Poppins', sans-serif;
            outline: none;
          }
        }
      }
      #forget {
        display: block;
        text-align: right;
        text-decoration: none;
        color: $h2szin;
        font-size: 0.9rem;
        transition: 0.3s;
        cursor: pointer;
        &:hover {
          color: $betuszin;
        }
      }
      .btn {
        display: block;
        width: 100%;
        height: 50px;
        border-radius: 25px;
        margin: 1rem 0;
        font-size: 1.2rem;
        outline: none;
        border: none;
        background-image: linear-gradient(to right, $szin2, $szin1, $szin2);
        cursor: pointer;
        color: $feher;
        text-transform: uppercase;
        font-family: 'Poppins', sans-serif;
        background-size: 200%;
        transition: 0.5s;
        &:hover {
          background-position: right;
        }
      }
      @media screen and (max-width: 1000px) {
        width: 290px;
      }
    }
    @media screen and (max-width: 900px) {
      justify-content: center;
    }
  }
  @media screen and (max-width: 1050px) {
    grid-gap: 5rem;
  }
  @media screen and (max-width: 900px) {
    grid-template-columns: 1fr;
  }
}
</style>
