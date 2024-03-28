<script setup>
    import { ref, reactive, onMounted, watch } from 'vue'
    import { db } from './data/guitarras'
    import Guitarra from './components/Guitarra.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'
    
    //USANDO REACTIVE (si son datos agrupado usas reactive "arreglos")
    //(recomendado cuando es de un objeto relacionado, es decir como el state es un objeto, que cada propiedad
    //sea un objeto que tenga relacion con otro objeto)

    // const state = reactive({
    //     guitarras: db
    // });
    // console.log(state.guitarras)

    //USANDO REF (recomendando cuando es algo diferente a un arreglo )
    const guitarras = ref([]);
    const carrito = ref([]);
    const guitarra = ref({});

    //Es el encargado de ver si cambio algo en el state que le pases (en este caso el carrito)
    watch(carrito, ()=>{
        guardarLocalStorage();
    },
    {
        deep:true
    });
    //ciclo de vida de un componente (como el onInit en angular), apenas carga el componente hace la asignacion
    onMounted(() => {
        guitarras.value = db;
        //en el caso del state con reactive
        // state.guitarras = db
        guitarra.value = db[3];
        const carritoStorage = localStorage.getItem('carrito');
        if(carritoStorage){
            carrito.value = JSON.parse(carritoStorage);
        }
    })  

    const guardarLocalStorage = () =>{
        localStorage.setItem('carrito',JSON.stringify(carrito.value))
    }
    const agregarCarrito =  (guitarra)=>{
        // Si no lo encontro retorna -1, si lo encontro retorna la posicion en la que esta
        const existeCarrito = carrito.value.findIndex( producto => producto.id === guitarra.id)
        if(existeCarrito >=0){
            //si existe carrito inyectamos la posicion en la que esta y su mamamos la cantidad
            carrito.value[existeCarrito].cantidad++
        }else{
            guitarra.cantidad = 1;
            carrito.value.push(guitarra);
        }
        
    }

    const decrementarCantidad = (id) =>{
        const index = carrito.value.findIndex( producto => producto.id === id)
        if(carrito.value[index].cantidad <= 1) return
        carrito.value[index].cantidad--
        
    }
    const incrementarCantidad = (id) =>{
        const index = carrito.value.findIndex( producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
        
    }

    const eliminarProducto = (id)=>{
        carrito.value = carrito.value.filter(producto => producto.id !== id)
        
    }
    const vaciarCarrito = ()=>{
        // carrito.value = carritoLleno.splice(0,carritoLleno.lenght);
        carrito.value = [];
       
    }
</script>

<template>
<!-- Componente Header -->
<Header
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad = "decrementarCantidad"
    @incrementar-cantidad = "incrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
/>

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
                <!-- Componentente guitarra -->
                <!-- Props es la forma en que se comunican entre componentes, en este caso el v-bind -->
                <!-- v-bind:guitarra="guitarraItem" esto es exactamente igual a :guitarra="guitarraItem" -->
                <Guitarra 
                v-for="guitarraItem in guitarras"
                :guitarra="guitarraItem"
                @agregar-carrito="agregarCarrito"
                />
        </div>
    </main>
    <Footer/>

</template>

<style scoped></style>
