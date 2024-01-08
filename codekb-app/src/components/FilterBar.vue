
<template>

   <select v-model="selectedCategory" @change="onCategoryChange">
      <option value="">All Categories</option>
      <option v-for="category in categories" :key="category" :value="category">
        {{ category }}
      </option>
    </select>
</template>
<script>

export default {
  props: {
    categories: Array, // Pass the list of categories from the parent component
  },
   emits: ['categoryChanged'], // Declare the custom event
    data() {
    return {
      selectedCategory: '',
    };
  },
//   watch: {
//   selectedCategory(newCategory) {
//     this.fetchWikiEntries(newCategory);
//   }
// },
  methods: {
    onCategoryChange() {
      this.$emit('categoryChanged', this.selectedCategory);
    },
  }
};
</script>
