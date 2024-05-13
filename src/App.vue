<template>
  <div>
    <header>
      <nav>
        <ul>
          <li @click="selectedTab = 'Todos'" :class="{ 'active': selectedTab === 'Todos' }">Todos</li>
          <li @click="selectedTab = 'Post'" :class="{ 'active': selectedTab === 'Post' }">Post</li>
        </ul>
      </nav>
    </header>
    <main>
      <div v-if="selectedTab === 'Todos'">
        <h1>Daftar Todos</h1>
        <input type="text" v-model="newTodoText" placeholder="Tambah kegiatan baru...">
        <button @click="addTodo">Tambah</button>
        <div v-for="(todo, index) in todos" :key="index" class="todo-item" :class="{ 'completed': todo.completed }">
          <input type="checkbox" v-model="todo.completed"> {{ todo.text }}
          <button @click="editTodo(index)">Edit</button>
          <button @click="deleteTodo(index)">Hapus</button>
          <input type="text" v-model="editedTodoText" v-if="todo.editing" @keyup.enter="updateTodo(index)" @blur="cancelEdit(index)">
        </div>
      </div>
      <div v-else-if="selectedTab === 'Post'">
        <h1>Daftar Postingan</h1>
        <label for="userSelect">Pilih Pengguna:</label>
        <select id="userSelect" v-model="selectedUser" @change="filterPosts">
          <option value="">Semua Pengguna</option>
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>
        <div v-for="post in filteredPosts" :key="post.id">
          <h3>{{ post.title }}</h3>
          <p>{{ post.body }}</p>
          <hr>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedTab: 'Todos', // Default tab yang dipilih
      newTodoText: '', // Text untuk kegiatan baru
      editedTodoText: '', // Text untuk mengedit todo
      todos: [], // Data todos dari API
      users: [], // Data users dari API
      selectedUser: '', // User yang dipilih untuk filter postingan
      posts: [], // Data postingan dari API
    };
  },
  mounted() {
    // Panggil method untuk mendapatkan data todos, users, dan posts
    this.getTodos();
    this.getUsers();
    this.getPosts();
  },
  computed: {
    filteredPosts() {
      if (!this.selectedUser) {
        return this.posts;
      }
      return this.posts.filter(post => post.userId === parseInt(this.selectedUser));
    }
  },
  methods: {
    // Method untuk mengambil data todos dari API
    async getTodos() {
      // Implementasikan kode untuk mengambil data todos dari API
    },
    // Method untuk mengambil data users dari API
    async getUsers() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        const data = await response.json();
        this.users = data;
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
    // Method untuk mengambil data posts dari API
    async getPosts() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        const data = await response.json();
        this.posts = data;
      } catch (error) {
        console.error('Error fetching posts:', error);
      }
    },
    // Method untuk menambahkan todo baru
    addTodo() {
      if (this.newTodoText.trim() !== '') {
        this.todos.push({ text: this.newTodoText, completed: false, editing: false });
        this.newTodoText = ''; // Reset input text setelah ditambahkan
      }
    },
    // Method untuk menghapus todo
    deleteTodo(index) {
      this.todos.splice(index, 1);
    },
    // Method untuk memulai edit todo
    editTodo(index) {
      this.todos[index].editing = true;
      this.editedTodoText = this.todos[index].text;
    },
    // Method untuk menyimpan perubahan edit todo
    updateTodo(index) {
      if (this.editedTodoText.trim() !== '') {
        this.todos[index].text = this.editedTodoText;
        this.todos[index].editing = false;
      }
    },
    // Method untuk membatalkan edit todo
    cancelEdit(index) {
      this.todos[index].editing = false;
      this.editedTodoText = '';
    },
    // Method untuk filter postingan berdasarkan user yang dipilih
    filterPosts() {
      // Tidak perlu implementasi di sini karena sudah dihandle oleh computed property
    },
  },
};
</script>

<style scoped>
/* Tambahkan gaya CSS sesuai kebutuhan Anda */
header {
  background-color: #333;
  color: #fff;
  padding: 10px;
}
nav ul {
  list-style-type: none;
  padding: 0;
}
nav ul li {
  display: inline;
  margin-right: 10px;
  cursor: pointer;
}
nav ul li.active {
  font-weight: bold;
}
.todo-item {
  margin-bottom: 10px;
}
.completed {
  text-decoration: line-through;
  color: #888;
}
</style>
