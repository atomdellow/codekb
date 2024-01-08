<template>
  <div class="entry">
    <h2>{{ entry.title }}</h2>
    
    <!-- Use pre tag for body to preserve formatting and spacing -->
    <pre>{{ entry.body }}</pre>

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

<style>
/* Add styles for pre tag if needed */
.entry pre {
  white-space: pre-wrap; /* Wraps the text */
  word-break: break-word; /* Breaks long words */
  background-color: #f5f5f5; /* Optional: background color */
  padding: 10px; /* Optional: padding */
  border-radius: 4px; /* Optional: border radius */
  border: 1px solid #ccc; /* Optional: border */
}
</style>
