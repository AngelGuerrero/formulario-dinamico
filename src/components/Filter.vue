<template>
  <div class="my-2 rounded p-1 bg-white">
    <div
      v-if="notification.message"
      class="border p-2 mt-3 rounded"
      :class="[
        notification.error
          ? 'border-red-300 bg-red-100 text-red-900 font-bold'
          : 'border-green-300 bg-green-100 text-green-900 font-bold'
      ]"
    >
      {{ notification.message }}
    </div>

    <form
      class="w-full mt-1 md:flex md:justify-evenly md:items-center"
      v-on:keydown.enter.prevent="emitAddFilter(); emitUpdate()"
      v-on:submit.prevent="emitAddFilter"
    >
      <input
        type="text"
        placeholder="Campo"
        v-model="payload.field"
        class="input"
      />

      <input
        type="text"
        placeholder="Operador"
        v-model="payload.operator"
        class="input"
      />

      <input
        type="text"
        placeholder="Valor"
        v-model="payload.value"
        class="input"
      />

      <div
        class="w-full py-1 flex justify-between md:w-3/12 md:justify-evenly md:items-center"
      >
        <!-- Eliminar filtro -->
        <button
          class="btn bg-red-500 text-white hover:bg-red-400"
          @click="emitDeleteFilter"
        >
          Eliminar
        </button>

        <!-- Confirmar creación de filtro -->
        <input
          v-if="!committed"
          type="submit"
          value="Agregar"
          class="btn bg-purple-500 text-white hover:bg-purple-400"
        />

        <!-- Confirmar cambios del filtro -->
        <input
          v-if="committed"
          type="submit"
          value="Actualizar"
          class="btn"
          :disabled="!changed"
          :class="[ changed ? 'bg-yellow-300 hover:bg-yellow-200' : 'disabled:opacity-100 shadow-none text-gray-400']"
          @click="emitUpdate"
        />
      </div>
    </form>
  </div>
</template>

<script>
//
// Constantes para reestablecer los valores a su estado inicial
//
const notification = () => ({ message: '', error: false })

export default {
  name: 'Filter',

  props: {
    //
    // @prop id
    //
    // Prop para identificar al componente y su información
    // la cual se emitirá al componente padre.
    //
    id: {
      type: String,
      required: true
    }
  },

  data () {
    return {
      notification: notification(),

      //
      //
      // Información que emitirá al componente padre.
      payload: {
        field: '',
        operator: '',
        value: ''
      },

      //
      // Flag para saber si la información del componente ya
      // ha sido agregada a los filtros.
      committed: false,

      //
      // Flag para saber si ha ocurrido un cambio en el modelo
      // de datos de interés, en este caso en 'payload'.
      changed: false
    }
  },

  watch: {
    payload: {
      immediate: true,
      deep: true,
      handler (newValue) {
        //
        // Si aún no está confirmado,
        // no escucha los cambios
        if (!this.committed) return

        //
        // Si ha ocurrido un cambio en las variables
        // entonces notifica al componente padre.

        //
        // Podría emitirse el cambio directamente
        // pero quizás pueda afectar en el rendimiento
        // es por eso que establecí mejor un flag 'changed'
        // para saber si ha habido un cambio en las variables
        // if (newValue) this.emitUpdate()

        if (newValue) this.changed = true
      }
    }
  },

  methods: {
    //
    // CUSTOM EVENTS
    //

    /**
     * emitAddFilter
     *
     * Método para emitir y mandar los datos ingresados del usuario
     * para el componente padre.
     *
     * Realiza algunas validaciones, en las cuales se encuentra un flag,
     * en la variable 'committed' para saber si este componente ya ha
     * sido utilizado, y no se pueda mandar más datos distintos al
     * componente padre.
     *
     * Realiza una validación para que todos los campos contentan valores.
     *
     * Al final resetea la información necesaria a su estado original.
     */
    emitAddFilter () {
      if (this.committed) return

      if (!this.validateInputs()) {
        this.showNotification('Los campos no pueden estar vacíos', true)
        return
      }

      this.committed = true
      this.showNotification('Filtro agregado correctamente', false)
      this.$emit('onCommitFilter', { ...this.payload, id: this.id })
    },

    /**
     * emitDeleteFilter
     *
     * Este método simplemente manda una señal al componente padre
     * para saber que el componente se ha querido eliminar,
     * y el componente padre tiene que tomar las acciones necesarias.
     */
    emitDeleteFilter () {
      this.$emit('onDeleteFilter')
    },

    /**
     * emitUpdate
     *
     * Esta función se encarga de emitir una señal al componente padre
     * que significa que el modelo de datos 'payload', ha sido modificado.
     *
     * Primero hace una validación si ha existido un cambio, si no, termina la función.
     *
     * Realiza las validaciones por defecto.
     *
     * Al final manda una notificación al usuario y reestablece las variables a
     * su estado original para volver a escuchar algún cambio en el modelo de datos.
     */
    emitUpdate () {
      if (!this.changed) return

      if (!this.validateInputs()) {
        this.showNotification('Ningún campo debe estar vacío.', true)
        return
      }

      this.changed = false
      this.showNotification('Filtro actualizado correctamente', false)
      this.$emit('onUpdate', { ...this.payload, id: this.id })
    },

    /**
     * validateInputs
     *
     * Función para validar los inputs.
     * Si alguno de los campos requeridos está vacío
     * entonces devuelve false, de lo contrario
     * de vuelve true, significado de que han pasado
     * la validación.
     */
    validateInputs () {
      let retval = true

      for (const key of Object.keys(this.payload)) {
        if (this.payload[key] === '') {
          retval = false
          return
        }
      }

      return retval
    },

    /**
     * showNotification
     *
     * Muestra una notificación al usuario si ha ocurrido un error.
     * Se elimina la notificación después de un periodo de tiempo
     * establecido si no ha ocurrido error.
     */
    showNotification (message, error) {
      this.notification.message = message
      this.notification.error = error

      if (!error) setInterval(() => this.resetData(), 8000)
    },

    /**
     * resetData
     *
     * Función para reestablecer los valores a su estado inicial.
     */
    resetData () {
      Object.assign(this.$data.notification, notification())
    }
  }
}
</script>

<style scoped>
.input {
  @apply w-full
         py-1
         px-3
         mt-3
         mb-1
         border
         border-solid
         border-gray-200
         rounded

         md:w-3/12
         md:m-0
         md:mr-3

         lg:w-3/12
         lg:ml-1

         focus:outline-none
         focus:border-pink-300;
}
</style>
