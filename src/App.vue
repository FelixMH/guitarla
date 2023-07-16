<!-- lógica de codigo js ( vue ) -->

<script setup>

    import { ref, onMounted, watch } from 'vue'
    import { db } from './data/guitarras'

    import Guitarra from "./components/Guitarra.vue";
    import Header from "./components/Header.vue";
    import Footer from "./components/Footer.vue";

    const guitarras = ref([])
    const carrito = ref([])
    const guitarra = ref({})


    watch(carrito, () => {
        guardarLocalStorage()
    }, {
        deep: true
    })

    onMounted( () => {
        guitarras.value = db
        guitarra.value = db[3]

        const carritoStorage = localStorage.getItem('carrito')
        if ( carritoStorage ) {
            carrito.value = JSON.parse(carritoStorage)
        }
    })


    const guardarLocalStorage = () => {
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) => {

        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)

        if ( existeCarrito >= 0 ) {
            carrito.value[existeCarrito].cantidad++
        } else {
            guitarra.cantidad = 1;
            carrito.value.push(guitarra);
        }

        // guardarLocalStorage()
    }

    const decrementarCantidad = (id) => {
        const index = carrito.value.findIndex(producto => producto.id === id)
        if ( carrito.value[index].cantidad <= 1 ) return
        carrito.value[index].cantidad--

        // guardarLocalStorage()
    }

    const incrementarCantidad = (id) => {
        const index = carrito.value.findIndex(producto => producto.id === id)
        if ( carrito.value[index].cantidad >= 5 ) return
        carrito.value[index].cantidad++

        // guardarLocalStorage()
    }

    const eliminarProducto = ( id ) => {
        carrito.value = carrito.value.filter( producto => producto.id !== id )

        // guardarLocalStorage()
    }

    const vaciarCarrito = () => {
        carrito.value = []

        // guardarLocalStorage()
    }

</script>


<!-- template ( la vista de html para el sitio ) -->
<template>

    <Header
        :carrito="carrito"
        :guitarra="guitarra"
        @decrementar-cantidad="decrementarCantidad"
        @incrementar-cantidad="incrementarCantidad"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">

            <!--    Componente guitarra, creado para inyectar el scroll de guitarras disponibles. -->
            <!--    @agregar-carrito: es un method hanlder customizado, que envia el metodo agregarCarrito definido en esta clase App.vue como evento por props
                  a el componente Guitarra.vue.

                  para los method handlers se suele convenir sus nombres con '-' y para las funciones de clase, notación camelCase.
            -->
            <Guitarra
                v-for="guitarra in guitarras"
                :key="guitarra.id"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />

        </div>
    </main>

    <Footer />

</template>


