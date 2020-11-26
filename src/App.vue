<template>
  <div class="min-h-screen p-3 bg-gray-50">
    <main class="container mx-auto">
      <h1 class="text-3xl tex-gray-500 my-3">
        Ejemplo de formularios dinámicos
      </h1>
      <button
        class="btn bg-green-500 text-white hover:bg-green-400"
        @click="addForm"
      >
        Agregar formulario
      </button>

      <!--
      Muestra lo lista de filtros que se han agregado.
     -->
      <h1 class="mt-5 mb-1 text-2xl text-gray-700">Filtros agregados</h1>
      <h4 class="mb-2 text-sm text-gray-500">
        Selecciona para eliminar un filtro
      </h4>
      <filter-list :filters="filters" @onRemoveFilter="removeFilterAndForm" />

      <!--
      Renderiza los componentes "Filter",
      que contiene cada uno un formulario independiente,
      y este pasa la información al array de 'filters'
      a través del evento personalizado 'onCommitFilter'.
     -->
      <div v-for="form in forms" :key="form.id">
        <Filter
          :id="form.id"
          @onCommitFilter="addFilter"
          @onUpdate="updateFilter"
          @onDeleteFilter="removeFilterAndForm(form)"
        />
      </div>
    </main>
  </div>
</template>

<script>
import Filter from './components/Filter.vue'
import FilterList from './components/FilterList.vue'

export default {
  name: 'App',

  components: {
    Filter,
    FilterList
  },

  data () {
    return {
      //
      // Cantidad de formularios a mostrar
      // en base a la cantidad que el usuario
      // quiera agregar.
      forms: [],

      //
      // id para asignar al nuevo formulario
      // que se vaya a crear, lo manejo como
      // string para no afectar el consumo
      // de memoria en la aplicación.
      //
      // Es necesario para identificar el
      // formulario que se quiera eliminar.
      //
      formIdNext: '0',

      //
      // Filters.
      //
      // Contiene la información que el usuario
      // ha ingresado a través de los formularios.
      filters: []
    }
  },

  methods: {
    //
    // Agrega una nueva fila para editar los campos y agregar un filtro.
    /**
     * addForm
     *
     * Agrega una nueva fila para agregar un nuevo filtro.
     *
     * Agrega un nuevo objeto al array 'forms', el cual contiene
     * un id en formato de string, este es requerido para encontrar
     * el componente, eliminar o actualizar la información.
     *
     * Contiene también una propiedad 'name', la cual es meramente
     * informativa.
     *
     * Es importante que el id esté en forma de 'string' por cuestiones
     * de memoria y rendimiento, además su única función es que el
     * string sea único para buscarlo.
     */
    addForm () {
      this.forms.push({ id: this.formIdNext, name: 'FilterComponent' })
      this.formIdNext = (Number.parseInt(this.formIdNext) + 1).toString()
    },

    /**
     * addFilter
     */
    addFilter (payload) {
      this.filters.push(payload)
    },

    /**
     * updateFilter
     *
     * Para actualizar un filtro es necesario quitarlo y reemplazarlo,
     * para que Vue pueda hacer un renderizado adecuado de los arrays
     * y se pueda visualizar los cambios sin problemas.
     */
    updateFilter (payload) {
      this.removeFilter(payload)
      this.addFilter(payload)
    },

    /**
     * removeFilterAndForm
     */
    removeFilterAndForm (param) {
      this.removeForm(param)
      this.removeFilter(param)
    },

    /**
     * removeFilter
     *
     * Elimina un item de 'filters' filtrándolo por su id
     * previamente establecido.
     */
    removeFilter (filter) {
      this.filters = this.filters.filter((item) => item.id !== filter.id)
    },

    /**
     * removeForm
     *
     * Elimina un item de 'forms' filtrándolo por su id
     * previamente establecido.
     */
    removeForm (form) {
      this.forms = this.forms.filter((item) => item.id !== form.id)
    }
  }
}
</script>

<style>
.btn {
  @apply px-3
         py-2
         rounded
         shadow-lg
         text-sm
         cursor-pointer
         focus:outline-none
         focus:bg-indigo-900
         focus:text-white;
}
</style>
