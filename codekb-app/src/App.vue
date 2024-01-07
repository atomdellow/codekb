<template>
  <div id="app">
    <div class="menu">
      <button @click="activeSection = 'create'">Create Wiki</button>
      <button @click="activeSection = 'view'">View</button>
    </div>
    <div v-if="showFilter">
      <filter-bar v-model:filter="filter"> </filter-bar>
    </div>

    <div class="container">
      <div v-if="showCreateEntry">
        <add-entry-form @add-entry="addNewEntry" class="card"></add-entry-form>
      </div>
      <div class="card" v-if="showWikis">
        <button @click="prevPage" :disabled="currentPage <= 1">Previous</button>
        <button @click="nextPage" :disabled="currentPage >= maxPage">
          Next
        </button>
        <div v-for="entry in paginatedEntries" :key="entry.id">
          <div v-if="entry.isEditing">
            <!-- Inline Edit Form -->
            <input type="text" v-model="entry.title" placeholder="Title" />
            <textarea v-model="entry.body" placeholder="Body"></textarea>
            <!-- Add other editable fields as needed -->
            <button @click="updateEntry(entry)">Save Changes</button>
            <button @click="cancelEdit(entry)">Cancel</button>
          </div>
          <div v-else>
            <div class="card">
              <wiki-entry
                :entry="entry"
                @edit-entry="editEntry"
                @delete-entry="deleteEntry"
              ></wiki-entry>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import WikiEntry from "./components/WikiEntry.vue";
import AddEntryForm from "./components/AddEntryForm.vue";
import FilterBar from "./components/FilterBar.vue";
import apiService from "./APIService";
export default {
  components: {
    WikiEntry,
    AddEntryForm,
    FilterBar,
  },
  data() {
    return {
      activeSection: "view",
      newEntry: {
        title: "",
        body: "",
        category: "",
        tags: "",
        reference: "",
        isEditing: false,
      },
      entries: [],
      filter: "",
      currentPage: 1,
      pageSize: 3,
      currentEdit: null,
      entryStub: [
        {
          id: 1,
          title: "console.log(message)",
          body: "Logs a message to the console",
          category: "JavaScript",
          tags: ["logging", "debugging"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/API/Console/log",
          isEditing: false,
        },
        {
          id: 2,
          title: "canvas",
          body: "Displays graphics inside an area in the window, can have its own dimensions as well as the page.",
          category: "HTML",
          tags: ["graphics", "display"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/HTML/Element/canvas",
          isEditing: false,
        },
        // ... more entries

        {
          id: 3,
          title: "CSS Flexbox",
          body: "A layout model that allows items in a container to be aligned and distributed space within a container.",
          category: "CSS",
          tags: ["layout", "flexbox"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox",
          isEditing: false,
        },
        {
          id: 4,
          title: "HTML <a> Tag",
          body: "Defines a hyperlink, which is used to link from one page to another.",
          category: "HTML",
          tags: ["elements", "links"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a",
          isEditing: false,
        },
        {
          id: 5,
          title: "Vue.js Reactive Data",
          body: "Vue.js uses a reactivity system that allows data to be automatically updated when it changes.",
          category: "Vue",
          tags: ["reactivity", "framework"],
          reference: "https://vuejs.org/v2/guide/reactivity.html",
          isEditing: false,
        },
        {
          id: 6,
          title: "SQL JOIN",
          body: "A SQL JOIN clause is used to combine rows from two or more tables, based on a related column between them.",
          category: "SQL",
          tags: ["database", "join"],
          reference: "https://www.w3schools.com/sql/sql_join.asp",
          isEditing: false,
        },
        {
          id: 7,
          title: "C# Classes",
          body: "Classes in C# are blueprints for creating objects. A class defines the kinds of data and the functionality their objects will have.",
          category: "C#",
          tags: ["oop", "classes"],
          reference:
            "https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/classes",
          isEditing: false,
        },
        {
          id: 8,
          title: "JavaScript Promises",
          body: "The Promise object represents the eventual completion (or failure) of an asynchronous operation and its resulting value.",
          category: "JavaScript",
          tags: ["asynchronous", "promises"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise",
          isEditing: false,
        },
        {
          id: 9,
          title: "CSS Grid Layout",
          body: "CSS Grid Layout is a two-dimensional layout system for the web. It lets you layout items into rows and columns.",
          category: "CSS",
          tags: ["layout", "grid"],
          reference:
            "https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout",
          isEditing: false,
        },
      ],
    };
  },
  mounted() {
    this.fetchWikiEntries(); // Fetch entries when the component is mounted
  },
  computed: {
    showCreateEntry() {
      return this.activeSection === "create";
    },
    showWikis() {
      return this.activeSection === "view";
    },
    showFilter() {
      return this.activeSection === "search";
    },
    paginatedEntries() {
      const start = (this.currentPage - 1) * this.pageSize;
      return this.entries.slice(start, start + this.pageSize);
    },
    maxPage() {
      return Math.ceil(this.entries.length / this.pageSize);
    },
    filteredEntries() {
      return this.entries.filter(
        (entry) =>
          entry.category.includes(this.filter) ||
          entry.tags.some((tag) => tag.includes(this.filter))
      );
    },
  },
  methods: {
    // showWiki(){return this.showWiki === true? false : true},
    // addEntry(newEntry) {
    //   this.entries.push(newEntry);
    // },
    nextPage() {
      if (this.currentPage < this.maxPage) this.currentPage++;
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--;
    },
    editEntry(entry) {
      entry.isEditing = true;
    },
    // deleteEntry(entryId) {
    //   this.entries = this.entries.filter((entry) => entry.id !== entryId);
    // },
    saveEntry(entry) {
      // Find the original entry in the array
      const index = this.entries.findIndex((e) => e.id === entry.id);
      if (index !== -1) {
        // Directly update the entry in the array
        this.entries[index] = { ...entry, isEditing: false }; // Set isEditing to false after saving
      }
    },

    async fetchWikiEntries() {
      try {
        const response = await apiService.getWikiEntries();
        this.entries = response.data;
      } catch (error) {
        console.error("Error fetching entries:", error);
      }
    },
    async addNewEntry(newEntryData) {
      try {
        if (newEntryData.id) {
          // Update existing entry
          await apiService.updateWikiEntry(newEntryData.id, newEntryData);
          // Update the entry in the local state
          const index = this.entries.findIndex((e) => e.id === newEntryData.id);
          if (index !== -1) {
            this.entries.splice(index, 1, {
              ...newEntryData,
              isEditing: false,
            });
            this.resetForm();
          }
        } else {
          // Create new entry
          const response = await apiService.createWikiEntry(newEntryData);
          // Add the new entry to the local state
          this.entries.push({ ...response.data, isEditing: false });
        }
      } catch (error) {
        console.error("Error saving entry:", error);
      }
    },
    cancelEdit(entry) {
      entry.isEditing = false;
    },
    async updateEntry(entry) {
      try {
        const response = await apiService.updateWikiEntry(entry.id, entry);
        console.log("Response:", response);

        if (response.data) {
          const index = this.entries.findIndex((e) => e.id === entry.id);
          if (index !== -1) {
            this.entries.splice(index, 1, {
              ...response.data,
              isEditing: false,
            });
          }
        } else {
          console.error("No data returned from the update API");
        }
      } catch (error) {
        console.error("Error updating entry:", error);
      }
    },

    async deleteEntry(entryId) {
      try {
        await apiService.deleteWikiEntry(entryId);
        console.log("Entry deleted");
        this.entries = this.entries.filter((entry) => entry.id !== entryId); // Remove the entry from the local state
      } catch (error) {
        console.error("Error deleting entry:", error);
      }
    },
  },
};
</script>
<style>
/* App.vue or main.css */
body {
  font-family: "Arial", sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  width: 80%;
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background-color: white;
  border-radius: 8px;
}

.card {
  background: #fff;
  margin-bottom: 20px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  border-radius: 4px;
}

.card-header {
  font-size: 1.2em;
  margin-bottom: 15px;
}

.card-body {
  font-size: 1em;
}

button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 15px;
  margin: 5px;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

input,
textarea {
  width: 100%;
  padding: 10px;
  margin: 5px 0;
  border-radius: 4px;
  border: 1px solid #ddd;
  box-sizing: border-box;
}

input:focus,
textarea:focus {
  outline: none;
  border-color: #007bff;
}
.menu {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.menu button {
  margin: 0 10px;
  /* Add more styles as needed */
}
</style>