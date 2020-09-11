<template>
    <div>
        <button class="btn btn-primary btn-tweet btn-xl" @click="mostrar()">+</button>
        <form @submit.prevent="editarTweet(tweet)" v-if="estadoEditar === 3">
            <h3>Editar Tweet</h3>
            <input type="text" class="form-control mb-2" placeholder="Nombre" v-model="tweet.nombre">
            <input type="text" class="form-control mb-2" placeholder="tweet" v-model="tweet.descripcion">
            <button class="btn btn-warning" type="submit">Guardar</button>
            <button class="btn btn-danger" type="submit" @click="cancelarEdicion">Cancelar</button>
        </form>
        <form @submit.prevent="agregarTweet" v-else-if ="estadoEditar === 2">
            <h3>Agregar tweet</h3>
            <input type="text" class="form-control mb-2" placeholder="Nombre" v-model="tweet.nombre">
            <input type="text" class="form-control mb-2" placeholder="tweet" v-model="tweet.descripcion">
            <button class="btn btn-primary" type="submit">Agregar Tweet</button>
        </form>
        <hr>
        <h3>Lista de Tweets:</h3>
        <ul class="list-group">
            <li class="list-group-item" v-for="(item, index) in tweets" :key="index" >
          <span class="badge badge-primary float-right">
            {{item.updated_at}}
          </span>
                <p>{{item.nombre}}</p>
                <p>{{item.descripcion}}</p>
                <p>
                    <button class="btn btn-success btn-tweet btn-xl" @click="editarFormulario(item)"><></button>
                    <button class="btn btn-danger btn-tweet btn-xl" @click="eliminarTweet(item, index)">X</button>
                </p>
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            tweets: [],
            estadoEditar: 1,
            tweet: {nombre: '', descripcion: ''}
        }
    },
    created(){
        axios.get('/tweets').then(res=>{
            this.tweets = res.data;
        })
    },
    methods:{
        mostrar(){
            this.estadoEditar = 2;
        },
        agregarTweet(){
            if(this.tweet.nombre.trim() === '' || this.tweet.descripcion.trim() === ''){
                alert('Debes completar todos los campos antes de guardar');
                return;
            }
            const tweetNuevo = this.tweet;
            this.tweet = {nombre: '', descripcion: ''};
            axios.post('/tweets', tweetNuevo)
                .then((res) =>{
                    const tweetServidor = res.data;
                    this.tweets.push(tweetServidor);
                })
        },
        editarFormulario(item){
            this.tweet.nombre = item.nombre;
            this.tweet.descripcion = item.descripcion;
            this.tweet.id = item.id;
            this.estadoEditar = 3;
        },
        editarTweet(tweet){
            const params = {nombre: tweet.nombre, descripcion: tweet.descripcion};
            axios.put(`/tweets/${tweet.id}`, params)
                .then(res=>{
                    const index = this.tweets.findIndex(item => item.id === tweet.id);
                    this.tweets[index] = res.data;
                    axios.get('/tweets').then(res=>{
                        this.tweets = res.data;
                    })
                })
            this.estadoEditar = 1;
        },
        eliminarTweet(tweet, index){
            const confirmacion = confirm(`Eliminar tweet ${tweet.nombre}`);
            if(confirmacion){
                axios.delete(`/tweets/${tweet.id}`)
                    .then(()=>{
                        this.tweets.splice(index, 1);
                    })
            }
        },
        cancelarEdicion(){
            this.modoEditar = false;
            this.tweet = {nombre: '', descripcion: ''};
        }
    }
}
</script>

<style scoped>
.btn-tweet {
    width: 30px;
    height: 30px;
    padding: 6px 0px;
    border-radius: 30px;
    text-align: center;
    font-size: 12px;
    line-height: 1.42857;
}
</style>
