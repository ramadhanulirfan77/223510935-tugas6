<template>
  <div id="app">
    <div class="container">
      <div class="input-section">
        <input type="text" v-model="newArticle.title" placeholder="Judul artikel">
        <textarea v-model="newArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="saveData">Simpan</button>
      </div>
      <div class="control-buttons">
        <button @click="loadData">Load Data</button>
      </div>
      <div v-if="dataLoaded" class="article-list">
        <div v-if="loading" class="loader"></div>
        <div v-else class="article-container">
          <div v-for="article in articles" :key="article.id" class="article-item">
            <h3>{{ article.title }}</h3>
            <p>{{ article.content }}</p>
            <div class="article-buttons">
              <button @click="editArticle(article)">Edit</button>
              <button @click="deleteArticle(article.id)">Hapus</button>
            </div>
          </div>
        </div>
      </div>
      <div v-if="editMode" class="edit-section">
        <input type="text" v-model="editedArticle.title" placeholder="Judul artikel">
        <textarea v-model="editedArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="updateArticle">Update</button>
        <button @click="cancelEdit">Batal</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      articles: [],
      dataLoaded: false,
      loading: false,
      newArticle: {
        title: '',
        content: ''
      },
      editedArticle: {
        id: '',
        title: '',
        content: ''
      },
      editMode: false
    };
  },
  methods: {
    loadData() {
      this.loading = true;
      axios.get('http://localhost:3000/articles')
        .then(response => {
          this.articles = response.data;
          this.dataLoaded = true;
          this.loading = false;
        })
        .catch(error => {
          console.error("There was an error loading the data!", error);
          this.loading = false;
        });
    },
    saveData() {
      axios.post('http://localhost:3000/articles', this.newArticle)
        .then(response => {
          console.log("Data saved successfully!", response.data);
          this.newArticle.title = '';
          this.newArticle.content = '';
          this.loadData(); // Reload data after saving
        })
        .catch(error => {
          console.error("There was an error saving the data!", error);
        });
    },
    deleteArticle(articleId) {
      axios.delete(`http://localhost:3000/articles/${articleId}`)
        .then(response => {
          console.log("Article deleted successfully!", response.data);
          this.loadData(); // Reload data after deleting
        })
        .catch(error => {
          console.error("There was an error deleting the article!", error);
        });
    },
    editArticle(article) {
      this.editMode = true;
      this.editedArticle.id = article.id;
      this.editedArticle.title = article.title;
      this.editedArticle.content = article.content;
    },
    updateArticle() {
      axios.put(`http://localhost:3000/articles/${this.editedArticle.id}`, this.editedArticle)
        .then(response => {
          console.log("Article updated successfully!", response.data);
          this.editMode = false;
          this.editedArticle.id = '';
          this.editedArticle.title = '';
          this.editedArticle.content = '';
          this.loadData(); // Reload data after updating
        })
        .catch(error => {
          console.error("There was an error updating the article!", error);
        });
    },
    cancelEdit() {
      this.editMode = false;
      this.editedArticle.id = '';
      this.editedArticle.title = '';
      this.editedArticle.content = '';
    }
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');

body {
  font-family: 'Orbitron', sans-serif;
  background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
  color: #e0e0e0;
  margin: 0;
  padding: 0;
  overflow-x: hidden;
}

.container {
  max-width: 900px;
  width: 500px;
  margin: 20px auto;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.37);
}

.input-section, .edit-section {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 20px;
}

.control-buttons, .article-buttons {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-bottom: 20px;
}

h3 {
  margin: 0;
  color: #ff4081;
}

p {
  margin: 5px 0;
  color: #b3b3b3;
}

input[type="text"],
textarea {
  width: 90%;
  padding: 20px;
  margin: 5px;
  border: none;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.2);
  color: #e0e0e0;
  outline: none;
  transition: background 0.3s ease-in-out;
}

input[type="text"]:focus,
textarea:focus {
  background: rgba(255, 255, 255, 0.3);
}

button {
  padding: 10px 20px;
  background: #6200ea;
  color: #ffffff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s ease-in-out;
}

button:hover {
  background: #3700b3;
}

.loader {
  border: 6px solid rgba(255, 255, 255, 0.2);
  border-top: 6px solid #ff4081;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
  margin: 20px auto;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.article-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.article-item {
  background: rgba(255, 255, 255, 0.1);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
}

.article-item h3 {
  margin-bottom: 10px;
}

.article-item p {
  margin-bottom: 10px;
}

@media screen and (max-width: 768px) {
  .container {
    padding: 15px;
  }

  input[type="text"],
  textarea,
  button {
    width: 100%;
  }
}
</style>
