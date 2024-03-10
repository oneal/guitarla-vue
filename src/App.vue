<script setup>
import {onMounted, ref, watch} from "vue";
import {db} from "./data/guitarras"
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue"
import Footer from "./components/Footer.vue";
// const state = reactive({
//   guitarras: []
// })
// state.guitarras = db
// console.log(state.guitarras)

const guitarras = ref([]);
const carrito = ref([])
const guitarra = ref({})

watch(carrito, () => {
  guardarLocalStorage()
}, {
  deep: true
})

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3]

  const carritoStorage = localStorage.getItem('itemsCarrito');
  if(carritoStorage.length > 0) {
    carrito.value = JSON.parse(carritoStorage)
  }
})

const guardarLocalStorage = () => {
  localStorage.setItem('itemsCarrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
  if(existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++
  } else {
    guitarra.cantidad = 1
    carrito.value.push(guitarra)
  }
}

const incrementarCarrito = (id) => {
  const index = carrito.value.findIndex(producto => producto.id === id)
  carrito.value[index].cantidad++
}

const disminuirCarrito = (id) => {
  const index = carrito.value.findIndex(producto => producto.id === id)
  if(carrito.value[index].cantidad > 1) {
    carrito.value[index].cantidad--
  }
}

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter(producto => producto.id !== id)
}

const vaciarCarrito = () => {
  carrito.value = [];
}

</script>

<template>
  <Header
      :carrito="carrito"
      :guitarra="guitarra"
      @agregar-carrito="agregarCarrito"
      @incrementar-carrito="incrementarCarrito"
      @disminuir-carrito="disminuirCarrito"
      @eliminar-producto="eliminarProducto"
      @vaciar-carrito="vaciarCarrito"

  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>
  <Footer />
</template>