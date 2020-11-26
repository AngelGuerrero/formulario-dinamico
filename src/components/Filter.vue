<template>
  <div>
    <div
      v-if="notification.message"
      class="border p-2 mt-3 rounded"
      :class="[
        notification.error
          ? 'border-red-300 bg-red-100 text-red-900'
          : 'border-green-300 bg-green-100 text-green-900'
      ]"
    >
      {{ notification.message }}
    </div>

    <form
      class="mt-1"
      v-on:keydown.enter.prevent="emitAddFilter"
      v-on:submit.prevent="emitAddFilter"
    >
      <input
        type="text"
        placeholder="Campo"
        v-model="payload.field"
        class="
            w-full
            px-3
            py-1
            mt-3
            mb-1
            border
            border-solid
            border-gray-200
            rounded
            focus:outline-none
            focus:border
            focus:border-solid
            focus:border-pink-300
            "
      />

      <input
        type="text"
        placeholder="Operador"
        v-model="payload.operator"
        class="
            w-full
            px-3
            py-1
            mb-1
            border
            border-solid
            border-gray-200
            rounded
            focus:outline-none
            focus:border
            focus:border-solid
            focus:border-pink-300
            "
      />

      <input
        type="text"
        placeholder="Valor"
        v-model="payload.value"
        class="
            w-full
            px-3
            py-1
            mb-1
            border
            border-solid
            border-gray-200
            rounded
            focus:outline-none
            focus:border
            focus:border-solid
            focus:border-pink-300
            "
      />

      <div class="w-full flex justify-between py-1">
        <button
          class="px-3
          py-2
          bg-red-500
          text-white
          rounded
          shadow-md
          hover:bg-red-400
          text-sm
          focus:bg-gray-700
          "
          @click="emitDeleteFilter"
        >
          Eliminar filtro
        </button>
        <input
          v-if="!committed"
          type="submit"
          value="Agregar filtro"
          class="px-3
          py-2
          bg-blue-500
          text-white
          rounded
          shadow-md
          hover:bg-blue-400
          text-sm
          focus:bg-gray-700
          "
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
    id: {
      type: String,
      required: true
    }
  },

  data () {
    return {
      notification: notification(),

      payload: {
        field: '',
        operator: '',
        value: ''
      },

      committed: false
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

    showNotification (message, error) {
      this.notification.message = message
      this.notification.error = error

      if (!error) setInterval(() => this.resetData(), 4000)
    },

    resetData () {
      Object.assign(this.$data.notification, notification())
    }
  }
}
</script>
