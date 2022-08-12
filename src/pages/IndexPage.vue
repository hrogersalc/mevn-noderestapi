<template>
  <q-page padding>
    <q-btn @click="acceso">Ingresar</q-btn>
    <q-btn @click="crearLink">Crear Link</q-btn>
    <br />
    Token: {{ token }}
    <br />
    Expiracion: {{ expiracionToken }}
  </q-page>
</template>

<script setup>
import { api } from "src/boot/axios";
import { ref } from "vue";

const token = ref("");
const expiracionToken = ref("");

const acceso = async () => {
  try {
    const res = await api.post("/auth/login", {
      email: "roger@gmail.com",
      password: "123123",
    });
    token.value = res.data.token;
    expiracionToken.value = res.data.expiracionToken;
    ajustarTemporizador();
  } catch (error) {
    console.log(error);
  }
};

const crearLink = async () => {
  try {
    const res = await api({
      method: "POST",
      url: "/enlaces",
      headers: {
        Authorization: "Bearer " + token.value,
      },
      data: {
        enlaceLargo: "https://getbootstrap.com/docs/5.1/components/navbar/",
      },
    });
    console.log(res.data);
  } catch (error) {
    console.log(error);
  }
};

const ajustarTemporizador = () => {
  setTimeout(() => {
    refrescarToken();
  }, expiracionToken.value * 1000 - 6000);
};

const refrescarToken = async () => {
  try {
    const res = await api.get("/auth/refresh");
    token.value = res.data.token;
    expiracionToken.value = res.data.expiracionToken;
    ajustarTemporizador();
  } catch (error) {
    console.log(error);
  }
};

refrescarToken();
</script>
