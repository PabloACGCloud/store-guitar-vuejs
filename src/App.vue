<script setup>
  import {ref, reactive, onMounted, watch} from 'vue'
  import {db} from './data/guitarras'
  import guitarra from './components/guitarra.vue'
  import Header from './components/Header.vue'
  import Footer from './components/Footer.vue'



  const guitarras = ref([])
  const carrito = ref([])
  const Guitarra = ref ({})

  watch(carrito, () =>{
    guardarLocalStorage();

  }, {
    deep: true
  })

  onMounted(()=>{
    guitarras.value = db
    Guitarra.value = db[6]
    const carritoStorage = localStorage.getItem('carrito')
    if(carritoStorage){
      carrito.value = JSON.parse(carritoStorage)
    }
  })

  const guardarLocalStorage = () =>{
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) =>{
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        if(existeCarrito >= 0){
            carrito.value[existeCarrito].cantidad++
            
        }else{
            guitarra.cantidad = 1;
            carrito.value.push(guitarra)
        }
    }

  const decrementarCarrito = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad <= 1) return
    carrito.value[index].cantidad--
    

  }
  const incrementarCarrito = (id) =>{
    const index = carrito.value.findIndex(producto => producto.id === id)
    carrito.value[index].cantidad++
  }

  const eliminarCarrito = (id) =>{
    carrito.value = carrito.value.filter(producto => producto.id !== id)
  }

  const vaciarCarrito = () =>{
    carrito.value = []
    
  }

</script>

<template>
    <Header
    :carrito="carrito"
    :Guitarra="Guitarra"
    @decrementar-carrito="decrementarCarrito"
    @incrementar-carrito="incrementarCarrito"
    @eliminar-carrito="eliminarCarrito"
    @agregar-carrito="agregarCarrito"
    @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
          <guitarra
            v-for="guitarra in guitarras"
            :key="guitarra.id"
            :guitarra="guitarra"
            @agregar-carrito="agregarCarrito"
          />
        </div>
    </main>
    <Footer/>
</template>



