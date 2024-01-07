<template>
  <input type="text" :value="filter" @input="updateFilter" placeholder="Filter by category or tag" />
</template>

<script>
export default {
  props: {
    filter: String
  },
  methods: {
    updateFilter(event) {
      this.$emit('update-filter', event.target.value);
    }
  }
};
</script>
