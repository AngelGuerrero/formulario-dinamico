<template>
  <div class="mt-3 mb-5 flex flex-wrap">
    <div
      v-for="filter in filters"
      :key="filter.id"
      @click="emitRemoveFilter(filter)"
      class="text-sm rounded px-3 py-1 mr-1 my-1 bg-gray-300 cursor-pointer hover:bg-purple-300"
    >
      {{ filter.field }} {{ filter.operator }} {{ filter.value }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'FilterList',

  props: {
    filters: {
      type: Array,
      required: true
    }
  },

  methods: {
    emitRemoveFilter (filter) {
      this.$emit('onRemoveFilter', filter)
    }
  }
}
</script>
