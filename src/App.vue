<template>
  <div class="min-h-screen p-3 bg-gray-50">
    <main class="container mx-auto">
      <button
        class="px-4 py-2 bg-green-500 text-white rounded shadow-md hover:bg-green-400 text-sm"
        @click="addForm"
      >
        Agregar formulario
      </button>

      <!--
      Muestra los filtros que se han agregado como etiquetas
     -->
      <h1 class="mt-5 mb-3 text-2xl text-gray-500">Filtros agregados</h1>
      <div class="mt-3 mb-5 flex flex-wrap">
        <div
          v-for="filter in filters"
          :key="filter.id"
          @click="removeFilterAndForm(filter)"
          class="rounded px-3 py-1 mr-1 my-1 bg-yellow-300 cursor-pointer hover:bg-yellow-400"
        >
          {{ filter.field }} {{ filter.operator }} {{ filter.value }}
        </div>
      </div>

      <!--
      Renderiza los componentes "Filter",
      que contiene cada uno un formulario independiente,
      y este pasa la información al array de 'filters'
     -->
      <div v-for="form in forms" :key="form.id">
        <Filter
          :id="form.id"
          @onCommitFilter="addFilter"
          @onDeleteFilter="removeFilterAndForm(form)"
        />
      </div>
    </main>
  </div>
</template>

<script>
import Filter from './components/Filter.vue'

export default {
  name: 'App',

  components: {
    Filter
  },

  created () {
    console.log(this.forms)
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
    addForm () {
      this.forms.push({ id: this.formIdNext, name: 'FilterComponent' })
      this.formIdNext = (Number.parseInt(this.formIdNext) + 1).toString()
    },

    addFilter (payload) {
      console.log(payload)
      this.filters.push(payload)
    },

    removeFilterAndForm (param) {
      this.removeForm(param)
      this.removeFilter(param)
    },

    removeFilter (filter) {
      this.filters = this.filters.filter((item) => item.id !== filter.id)
    },

    removeForm (form) {
      this.forms = this.forms.filter((item) => item.id !== form.id)
    }
  }
}
</script>
