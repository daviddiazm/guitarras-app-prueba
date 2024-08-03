<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras.js";
import  Guitarra  from "./components/Guitarra.vue";
import Carrito from "./components/Carrito.vue"
import Header from "./components/Header.vue"
import Footer from "./components/Footer.vue";

// const state=reactive({
//   guitarras: []
// })
// console.log(state.guitarras);

const guitarras = ref([])
const carrito = ref([])
const guitarraHome = ref({})

watch(carrito, () => {
  guardarCarritoLocalStorage()
},{
  deep: true
})

onMounted(() => {
  // state.guitarras = db
  guitarras.value = db
  const index =  db.findIndex(guitarra => guitarra.nombre === "VAI")
  guitarraHome.value = db[index]
  // console.log(guitarras.value);

  const carritoStorage = localStorage.getItem('carrito')
  if(carritoStorage) {
    carrito.value = JSON.parse(carritoStorage)
  }
})

const guardarCarritoLocalStorage = () => {
  localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(prodcuto => prodcuto.id === guitarra.id)
  if(existeCarrito >= 0) {
    guitarra.cantidad ++
  }else {
    guitarra.cantidad = 1
    carrito.value.push(guitarra)
  }
}

const decrementarCantidad = (id) => {
  const index = carrito.value.findIndex(prodcuto => prodcuto.id === id)
  if(carrito.value[index].cantidad <= 1 ) return 
  carrito.value[index].cantidad--
  
}

const incrementarCantidad = (id) => {
  const index = carrito.value.findIndex(prodcuto => prodcuto.id === id)
  if(carrito.value[index].cantidad >= 5 ) return 
  carrito.value[index].cantidad++
}

const eliminarCarrito = (id) => {
  // const index = carrito.value.findIndex(prodcuto => prodcuto.id === id)
  // carrito.value.splice(index,1)
  //otra forma de hacerlo
  carrito.value = carrito.value.filter(prodcuto => prodcuto.id !== id)
}

const limpiarCarrito = () => {
  // carrito.value.splice(0, carrito.value.length)
  carrito.value = []
}

</script>

<template>
  <Header
    :carrito="carrito"
    :guitarraHome="guitarraHome"
    @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-carrito="eliminarCarrito"
    @limpiar-carrito="limpiarCarrito"
  />

  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        v-bind:guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
    </div>
  </main>


  <Footer/>
</template>

<style scoped></style>
