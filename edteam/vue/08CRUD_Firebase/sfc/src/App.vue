<template>
     <main id="main" class="container center">
        <section class="row valign-wrapper">
            <div class="col s12 m3">
                <img src="https://vuejs.org/images/logo.png"
                     alt="Vue.js"
                     class="responsive-img" />
            </div>
            <div class="col s12 m6">
                <h1>CRUD</h1>
                <h5>(Firebase)</h5>
            </div>
            <div class="col s12 m3">
                <img src="https://ed.team/static/images/logo-alt.svg"
                     alt="" class="responsive-img" />
            </div>
        </section>
        <section class="row valign-wrapper">
            <div class="col s12">
                <h2>Curso de Vue.js desde cero</h2>
            </div>
        </section>
        <section class="row valign-wrapper">
            <div class="col s10">
                <h3 class="left">Lista de estudiantes</h3>
            </div>
            <div class="col s2">
                <button class="btn-large btn-floating" v-on:click="toggleModal('add')">
                    <i class="material-icons">add_circle</i>
                </button>
            </div>
        </section>
        <hr />
        <transition name="fade">
            <p class="u-flexColumnCenter red accent-1 red-text text-darken-4"
               v-if="errorMessage">
                {{ errorMessage }}
                <i class="material-icons prefix">error</i>
            </p>
            <p class="u-flexColumnCenter green accent-1 green-text text-darken-4"
               v-if="successMessage">
                {{ successMessage }}
                <i class="material-icons prefix">check_circle</i>
            </p>
        </transition>
        <transition name="fade">
            <table class="responsive-table striped">
                <tr>
                    <th>Id</th>
                    <th>Nombre</th>
                    <th>Email</th>
                    <th>Web</th>
                    <th>Editar</th>
                    <th>Borrar</th>
                </tr>
                <tr v-for="student in students" :key="student['.key']">
                    <td>{{ student['.key'] }}</td>
                    <td>{{ student.name }}</td>
                    <td>{{ student.email }}</td>
                    <td>{{ student.web }}</td>
                    <td>
                        <button class="btn btn-floating">
                            <i class="material-icons" v-on:click="getStudent('edit', student)">edit</i>
                        </button>
                    </td>
                    <td>
                        <button class="btn btn-floating">
                            <i class="material-icons" v-on:click="getStudent('delete', student)">delete</i>
                        </button>
                    </td>
                </tr>
            </table>
        </transition>
         <transition name="fade">
            <section :class="['ModalWindow', displayAddModal]" v-if="showAddModal">
                <div class="ModalWindow-contairner">
                    <header class="ModalWindow-heading">
                        <div class="row valign-wrapper">
                            <div class="col s10">
                                <h4 class="left">Agregar Estudiante</h4>
                            </div>
                            <div class="col s12">
                                <button class="btn btn-floating right" v-on:click="toggleModal('add')">
                                    <i class="material-icons">close</i>
                                </button>
                            </div>
                        </div>
                    </header>
                    <form class="ModalWindow-content row" v-on:submit.prevent="createStudent">
                        <div class="input-field col s12">
                            <i class="material-icons prefix">account_circle</i>
                            <input name="Name" type="text" value="" placeholder="Nombre" required />
                        </div>
                        <div class="input-field col s12">
                            <i class="material-icons prefix">email</i>
                            <input name="Email" type="text" value="" placeholder="Correo" required />
                        </div>
                        <div class="input-field col s12">
                            <i class="material-icons prefix">web</i>
                            <input name="Web" type="text" value="" placeholder="Web" required />
                        </div>
                        <div class="input-field col s12">
                            <button class="btn-large btn-floating right" type="submit">
                                <i class="material-icons">save</i>
                            </button>
                        </div>
                    </form>
                </div>
            </section>
        </transition>
        <transition name="fade">
            <section :class="['ModalWindow', displayEditModal]" v-if="showEditModal">
                <div class="ModalWindow-contairner">
                    <header class="ModalWindow-heading">
                        <div class="row valign-wrapper">
                            <div class="col s10">
                                <h4 class="left">Editar Estudiante</h4>
                            </div>
                            <div class="col s12">
                                <button class="btn btn-floating right" v-on:click="toggleModal('edit')">
                                    <i class="material-icons">close</i>
                                </button>
                            </div>
                        </div>
                    </header>
                    <form class="ModalWindow-content row" v-on:submit.prevent="updateStudent">
                        <div class="input-field col s12">
                            <i class="material-icons prefix">account_circle</i>
                            <input v-model="activeStudent.name" name="Name" type="text" value="" placeholder="Nombre" required />
                        </div>
                        <div class="input-field col s12">
                            <i class="material-icons prefix">email</i>
                            <input v-model="activeStudent.email" name="Email" type="text" value="" placeholder="Correo" required />
                        </div>
                        <div class="input-field col s12">
                            <i class="material-icons prefix">web</i>
                            <input v-model="activeStudent.web" name="Web" type="text" value="" placeholder="Web" required />
                        </div>
                        <div class="input-field col s12">
                            <button class="btn-large btn-floating right" type="submit">
                                <i class="material-icons">save</i>
                            </button>
                            <input v-model="activeStudent['.key']" type="hidden" name="StudentId" value="" />
                        </div>
                    </form>
                </div>
            </section>
        </transition>
        <transition name="fade">
          <section :class="['ModalWindow', displayDeleteModal]" v-if="showDeleteModal">
              <div class="ModalWindow-contairner">
                  <header class="ModalWindow-heading">
                      <div class="row valign-wrapper">
                          <div class="col s10">
                              <h4 class="left">Eliminiar Estudiante</h4>
                          </div>
                          <div class="col s12">
                              <button class="btn btn-floating right" v-on:click="toggleModal('delete')">
                                  <i class="material-icons">close</i>
                              </button>
                          </div>
                      </div>
                  </header>
                  <form class="ModalWindow-content row" @submit.prevent="deleteStudent">
                      <div class="input-field col s12">
                          <p class="flow-text-center">¿Estás seguro de eliminar al estudiante:
                            <b>{{ activeStudent.name }}</b>
                          </p>
                          <input v-model="activeStudent['.key']" name="StudentId" type="hidden" required />
                      </div>
                      <div class="input-field col s4 offset-s4">
                          <button class="btn-large btn-floating left" type="submit">
                            <i class="material-icons">check</i>
                          </button>
                          <button class="btn-large btn-floating right" type="button"
                            @click="toggleModal('delete')">
                              <i class="material-icons">close</i>
                          </button>
                      </div>
                  </form>
              </div>
          </section>
        </transition>
    </main>
</template>

<script>
import {dbRef} from './helpers/firebase'
export default {
  name: 'app',
  data () {
    return {
      showAddModal: false,
      showEditModal: false,
      showDeleteModal: false,
      errorMessage: '',
      successMessage: '',
      students: [],
      activeStudent: {}
    }
  },
  firebase: {
    students: dbRef
  },
  computed: {
      displayAddModal() {
          return this.showAddModal ? 'u-show' : '';
      },
      displayEditModal() {
          return this.showEditModal ? 'u-show' : '';
      },
      displayDeleteModal() {
          return this.showDeleteModal ? 'u-show' : '';
      }
  },
  methods: {
    toggleModal(modal) {
        if (modal === 'add')
            this.showAddModal = !this.showAddModal;
        else if (modal === 'edit')
            this.showEditModal = !this.showEditModal;
        else if (modal === 'delete')
            this.showDeleteModal = !this.showDeleteModal;
    },
    setMessages(err, res) {
        console.trace();
        console.log('err:', err, 'res:', res);
        if (err) {
            this.errorMessage = res;
        } else {
            this.successMessage = res;
        }

        setTimeout(() => {
            this.errorMessage = '';
            this.successMessage = '';
          },
          2000
        );
    },
    createStudent(e) {
        const els = e.target.elements;
        const data = {
            name : els['Name'].value,
            email: els['Email'].value,
            web  : els['Web'].value
        };

        dbRef.push(data)
            .then(() => {
                this.setMessages(false, 'Estudiante agregado con exito');
                //this.students.push(res.data);
                //this.getAllStudents();
            })
            .catch(() => this.setMessages(true, 'Error al tragar de agregar'));
        
        e.target.reset();
        this.toggleModal('add');
    },
    getStudent(action, student) {
        this.toggleModal(action);
        this.activeStudent = student;
    },
    updateStudent(e) {
      const els = e.target.elements;
      const data = {
        studentId: els['StudentId'].value,
        name     : els['Name'].value,
        email    : els['Email'].value,
        web      : els['Web'].value
      }

      dbRef.child(data.studentId).update(data)
        .then(() => {
          this.setMessages(false, 'Estudiante actualizado con exito');
          //this.students.push(res.data);
          //this.getAllStudents();
        })
        .catch(() => this.setMessages(true, 'Error al tratar de actualizar estudiante'));
      
      e.target.reset();
      this.toggleModal('edit');
    },
    deleteStudent(e) { 
      const els = e.target.elements;
      const data = {
        studentId: els['StudentId'].value,
      };

      dbRef.child(data.studentId).remove()
        .then(() => {
          this.setMessages(false, 'Estudiante eliminado con exito');
          //this.students.push(res.data);
          //this.getAllStudents();
        })
        .catch(() => this.setMessages(true, 'Error al tratar de eliminar estudiante'));

      e.target.reset();
      this.toggleModal('delete');
    }
  }
}
</script>

<style lang="scss">
// #app {
//   font-family: 'Avenir', Helvetica, Arial, sans-serif;
//   -webkit-font-smoothing: antialiased;
//   -moz-osx-font-smoothing: grayscale;
//   text-align: center;
//   color: #2c3e50;
//   margin-top: 60px;
// }

// h1, h2 {
//   font-weight: normal;
// }

// ul {
//   list-style-type: none;
//   padding: 0;
// }

// li {
//   display: inline-block;
//   margin: 0 10px;
// }

// a {
//   color: #42b983;
// }

:root {
  --color-primary: #41B883;
  --color-secondary: #35495E;
  --bg-color: #727f80;
}

.btn {
  &,
  &-large {
    background-color: var(--color-primary);
  }

  &:hover,
  &-large:hover {
      background-color: var(--color-secondary);
  }
}



.ModalWindow {
    position: fixed;
    z-index: 999;
    top:0;
    left:0;
    right:0;
    bottom:0;
    background-color: rgba(0,0,0,.25);
    display: none;

  &-contairner {
    margin: 5vh auto 0;
    width: 60%;
    background-color: #fff;
  }

  &-heading {
    padding: 0.1rem;
    background-color: var(--bg-color);
    color: #FFF;
  }

  &-content {
    padding: 1rem;
  }
}



.u-flexColumnCenter {
    padding: 1rem;
    display: flex;
    justify-content: center;
    align-items: center;
}

.u-show {
    display: initial;
}

.fade {
  &-enter-active,
  &-leave-active {
      transition: opacity .5s;
  }

  &-enter,
  &-leave-to {
      opacity: 0;
  }
}
</style>
