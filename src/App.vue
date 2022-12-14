<script setup>
import { ref, computed, watch } from "vue";
import Header from "./components/Header.vue";
import ButtonsMinMax from "./components/ButtonsMinMax.vue";
import calcularTotalPagar from "./helpers/index.js";

const cantidad = ref(10000);
const meses = ref(6);
const total = ref(0);
const MIN = 0;
const MAX = 20000;
const STEP = 100;

const formatearDinero = (valor) => {
  const formatter = new Intl.NumberFormat("es-US", {
    style: "currency",
    currency: "USD",
  });
  return formatter.format(valor);
};

const pagoMensual = computed(() => {
  return total.value / meses.value;
});

watch(
  [cantidad, meses],
  () => {
    total.value = calcularTotalPagar(cantidad.value, meses.value);
  },
  { inmediate: true }
);

const handleChangeDecrement = () => {
  cantidad.value = cantidad.value - STEP;
  if (cantidad.value < MIN) {
    cantidad.value = MIN;
  }
};

const handleChangeIncrement = () => {
  cantidad.value = cantidad.value + STEP;
  if (cantidad.value > MAX) {
    cantidad.value = MAX;
  }
};
</script>

<template>
  <div class="my-20 max-w-lg mx-auto bg-white shadow p-10">
    <Header />

    <div class="flex justify-between mt-10">
      <ButtonsMinMax :operador="'-'" @handleChange="handleChangeDecrement" />
      <ButtonsMinMax :operador="'+'" @handleChange="handleChangeIncrement" />
    </div>

    <div class="my-5">
      <input
        type="range"
        class="w-full bg-gray-200 accent-lime-500 hover:accent-lime-600"
        :min="MIN"
        :max="MAX"
        :step="STEP"
        v-model.number="cantidad"
      />
      <p class="text-center text-5xl font-extrabold text-indigo-600 mt-5">
        {{ formatearDinero(cantidad) }}
      </p>

      <h2 class="text-2xl font-extrabold text-gray-500 text-center mt-3">
        Elige un <span class="text-indigo-600">Plazo</span> a pagar
      </h2>

      <select
        name=""
        :value="meses"
        v-model.number="meses"
        id=""
        class="w-full p-2 bg-white border border-gray-300 rounded-lg text-center text-xl font-bold text-gray-500 mt-5"
      >
        <option value="6">6 meses</option>
        <option value="12">12 meses</option>
        <option value="24">24 meses</option>
      </select>
    </div>
    <div v-if="total > 0" class="my-5 space-y-3 bg-gray-50 p-5">
      <h2 class="text-2xl font-extrabold text-gray-500 text-center">
        Resumen de tu <span class="text-indigo-600">Pr??stamo</span>
      </h2>
      <p class="text-xl text-gray-500 text-center font-bold">
        {{ meses }} Meses:
      </p>
      <p class="text-xl text-gray-500 text-center font-bold">
        Total a pagar: {{ formatearDinero(total) }}
      </p>
      <p class="text-xl text-gray-500 text-center font-bold">
        Mensuales: {{ formatearDinero(pagoMensual) }}
      </p>
    </div>

    <p class="text-center font-extrabold text-gray-500 text-xl mt-5" v-else>
      A??ade una cantidad y un plazo a pagar
    </p>
  </div>
</template>
