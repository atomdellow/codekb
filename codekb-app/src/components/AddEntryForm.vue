<template>
  <div class="add-entry-form">
    <input type="text" v-model="newEntry.title" placeholder="Title" />

 <textarea v-model="newEntry.body" placeholder="Enter your text here"></textarea>

    <input
      type="text"
      v-model="newTag"
      placeholder="Add a tag"
      @keyup.enter="addTag"
    />
    <ul>
      <li v-for="tag in newEntry.tags" :key="tag">{{ tag }}</li>
    </ul>

    <select v-model="newEntry.category">
      <option disabled value="">Select a category</option>
      <option v-for="category in categories" :key="category" :value="category">
        {{ category }}
      </option>
    </select>

    <div v-for="(link, index) in newEntry.references" :key="index">
      <input
        type="text"
        v-model="newEntry.references[index]"
        placeholder="Reference link"
      />
    </div>
    <button @click="addReference">Add Reference</button>

    <button @click="submitEntry">Submit Entry</button>
  </div>
</template>

<script>


export default {
  data() {
    return {
      newEntry: {
        title: "",
        body: "",
        tags: "",
        category: "",
        references:"",
        isEditing:false
      },
      newTag: "",
      categories: ["JavaScript", "HTML", "CSS", "Vue", "SQL", "C#"],
      editorOptions: {
        // Specify your quill editor toolbar options here
      },
    };
  },
  methods: {
    entryAdd(entryData) {
      entryData.push()
      console.log(entryData);
    },
    addTag() {
      if (this.newTag) {
        this.newEntry.tags.push(this.newTag);
        this.newTag = "";
      }
    },
    addReference() {
      this.newEntry.references.push("");
    },
    submitEntry(entry) {
      // Handle the submission of the form
      console.log("Entry data: " + JSON.stringify(entry))
       this.$emit('add-entry', this.newEntry);
       
       
      console.log(this.newEntry);
    },
    resetForm() {
      this.newEntry = { title: '', body: '', category: '', reference: '', tags: '', isEditing: false /* reset other fields as well */ };
    }
  },
};
</script>

<style>
/* Add your styles here */
textarea {
  width: 100%;
  height: 200px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
  resize: vertical; /* Allows the user to vertically resize the textarea */
}
</style>
