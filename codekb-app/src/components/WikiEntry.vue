<template>
  <div class="entry">
    <h2>{{ entry.title }}</h2>
    <p>{{ entry.body }}</p>
    <span>{{ entry.category }}</span>
    <p>{{ entry.tags }}</p>
    <a :href="entry.reference" target="_blank">Reference</a>
     <button @click="editEntry">Edit</button>
    <button @click="deleteEntry">Delete</button>
  </div>
</template>

<script>
export default {
  props: {
    entry: Object
  },
  methods: {
    editEntry() {
      this.$emit('edit-entry', this.entry);
    },
    deleteEntry() {
      this.$emit('delete-entry', this.entry.id);
    }
  }
};
</script>
