<template>
    <div>
        <h1>Atencion alumnos con problemas</h1>
        <table class="table">
          <thead>
            <tr>
              <th class="d-none" scope="col">#</th>
              <th scope="col">Nombre</th>
              <th scope="col">Descripción</th>
              <th scope="col">Solución</th>
              <th scope="col">Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr  v-for="(item, index) in alumnos" :key="index">
              <th class="d-none" scope="row">{{item._id}}</th>
              <td>{{item.nombre}}</td>
              <td>{{item.descripcion}}</td>
              <td v-if="!item.solucion">ATENCIÓN</td>
              <td v-if="item.solucion">{{item.solucion}}</td>
              <td>
                <b-button class="btn-warning btn-sm mx-2" @click="activarEdicion(item._id),$bvModal.show('modal-actualizar')">Actualizar solucion</b-button>
                <b-button class="btn-danger btn-sm mx-2" @click="eliminarNota(item._id)">Eliminar</b-button>
              </td>
            </tr>
          </tbody>
        </table>

        <b-modal id="modal-actualizar" hide-footer>
          <template v-slot:modal-title>
            <h3 class="text-center">Editar Solucion</h3>
          </template>
          <div class="d-block text-center">
            <form @submit.prevent="editarAlumno(alumnoEditar)" >
              <input type="text" placeholder="Nombre" class="form-control my-2" v-model="alumnoEditar.nombre">
              <input type="text" placeholder="Descripcion" 
              class="form-control my-2" v-model="alumnoEditar.descripcion">
              <input type="text" placeholder="Solucion" 
              class="form-control my-2" v-model="alumnoEditar.solucion">
              <b-button class="btn-sm btn-block mb-1 btn-warning" type="submit" @click="$bvModal.hide('modal-actualizar')">Editar</b-button>
              <b-button class="btn-sm btn-block" @click="$bvModal.hide('modal-actualizar')">Cancelar</b-button>
            </form>
          </div>
        </b-modal>
    </div>
</template>

<script>
export default {
  data() {
    return {
      alumnos: [],
      agregar: true,
      alumnoEditar: {},
    };
  },
  created(){
    this.listarAlumnos();
  },
  methods:{
    listarAlumnos(){
      this.axios.get('alumnos')
      .then((response) => {
        this.alumnos = response.data;
      })
      .catch((e)=>{
        console.log('error' + e);
      })
    },
    eliminarAlumno(id){
      this.axios.delete(`alumno/${id}`)
        .then(res => {
          let index = this.alumnos.findIndex( item => item._id === res.data._id )
          this.notas.splice(index, 1);
        })
        .catch( e => {
          console.log(e.response);
        })
    },
    activarEdicion(id){
    this.agregar = false;
    this.axios.get(`buscar/${id}`)
      .then(res => {
        this.alumnoEditar = res.data;
      })
      .catch(e => {
        console.log(e.response);
      })
    },
    editarAlumno(item){
      this.axios.put(`alumno/${item._id}`, item)
        .then(res => {
          let index = this.alumnos.findIndex( itemAlumno => itemAlumno._id === this.alumnoEditar._id);
          this.alumnos[index].solucion = this.alumnoEditar.solucion;
          this.alumnoEditar = {}
        })
        .catch(e => {
          console.log(e);
        })
      this.agregar = true;
    }
  }
};
</script>