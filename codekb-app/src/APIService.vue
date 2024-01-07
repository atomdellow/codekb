<script>
import axios from "axios";

// Create an Axios client with a base URL
const apiClient = axios.create({
  baseURL: "https://localhost:44346/api", // Replace with your actual API base URL
  headers: {
    "Content-Type": "application/json",
  },
});


// Export the method as part of the service
export default {
  getWikiEntries : () => {
  return apiClient.get("/WikiEntries"); // Adjust the endpoint as needed
},

// Define a method for creating a new WikiEntry
createWikiEntry : (wikiEntry) => {
  return apiClient.post("/WikiEntries", wikiEntry);
},

updateWikiEntry : (id, wikiEntry) => {
  return apiClient.put(`/WikiEntries/${id}`, wikiEntry);
},

deleteWikiEntry : (id) => {
  return apiClient.delete(`/WikiEntries/${id}`);
},
  // ... other methods can be added here as needed ...
};
</script>