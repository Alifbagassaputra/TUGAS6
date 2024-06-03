<template>
  <div class="container">
    <form @submit.prevent="save" class="form-container">
      <h2>{{ form.id ? 'Edit Article' : 'New Article' }}</h2>
      <input type="text" v-model="form.title" placeholder="Title" class="form-input" /><br />
      <textarea v-model="form.content" placeholder="Content" class="form-input"></textarea><br />
      <div class="button-group">
        <button type="submit" class="button">{{ form.id ? 'Update' : 'Save' }}</button>
        <button type="button" @click="resetForm" class="button cancel">Cancel</button>
      </div>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-card">
        <div class="article-content">
          <h3>{{ article.title }}</h3>
          <p>{{ article.content }}</p>
        </div>
        <div class="article-actions">
          <button @click="edit(article)" class="button edit">Edit</button>
          <button @click="remove(article.id)" class="button delete">Delete</button>
        </div>
      </li>
    </ul>
    <button @click="load" class="button load">Load Articles</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      }
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        // Update existing article
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
        }
      } else {
        // Add new article
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load(); // Load articles when component is mounted
  }
};
</script>

<style scoped>
.container {
  width: 80%;
  max-width: 800px;
  margin: auto;
  font-family: Arial, sans-serif;
  background-color: #93aec1;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
  background-color: #9dbdba;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-container h2 {
  margin-bottom: 20px;
  color: #333;
}

.form-input {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.button-group {
  display: flex;
  justify-content: flex-end;
}

.button {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  background-color: #007BFF;
  color: white;
  cursor: pointer;
  margin-right: 10px;
  margin-bottom: 10px;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
}

.button.cancel {
  background-color: #20282f;
}

.button.delete {
  background-color: #dc3545;
}

.button.load {
  background-color: #28a745;
}

.article-list {
  list-style: none;
  padding: 0;
}

.article-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 20px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #9dbdba;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s;
}

.article-card:hover {
  transform: scale(1.02);
}

.article-content h3 {
  margin: 0 0 10px 0;
  color: #333;
}

.article-content p {
  margin: 0 0 10px 0;
  white-space: pre-wrap;
  color: #666;
}

.article-actions {
  display: flex;
  justify-content: flex-end;
  width: 100%;
}
</style>
