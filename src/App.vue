<template>
  <div id="app">
    <header>
      <h1>兴趣交流平台</h1>
      <nav>
        <button @click="showRegisterForm" v-if="!isLoggedIn">注册</button>
        <button @click="showLoginForm" v-if="!isLoggedIn">登录</button>
        <button @click="showNewPostForm" v-if="isLoggedIn">发帖</button>
        <button @click="logout" v-if="isLoggedIn">登出</button>
      </nav>
    </header>
    
    <main>
      <section>
        <h2>热门兴趣</h2>
        <ul>
          <li v-for="interest in interests" :key="interest">{{ interest }}</li>
        </ul>
      </section>
      
      <section>
        <h2>最新帖子</h2>
        <ul>
          <li v-for="post in posts" :key="post.id">{{ post.title }}</li>
        </ul>
      </section>
    </main>
    
    <footer>
      <p>联系我们</p>
    </footer>
    
    <!-- 注册表单 -->
    <div v-if="showRegister" class="modal">
      <div class="modal-content">
        <h2>注册</h2>
        <input v-model="registerUsername" placeholder="用户名">
        <input v-model="registerPassword" type="password" placeholder="密码">
        <button @click="register">注册</button>
        <button @click="showRegister = false">关闭</button>
      </div>
    </div>

    <!-- 登录表单 -->
    <div v-if="showLogin" class="modal">
      <div class="modal-content">
        <h2>登录</h2>
        <input v-model="loginUsername" placeholder="用户名">
        <input v-model="loginPassword" type="password" placeholder="密码">
        <button @click="login">登录</button>
        <button @click="showLogin = false">关闭</button>
      </div>
    </div>

    <!-- 发帖表单 -->
    <div v-if="showNewPost" class="modal">
      <div class="modal-content">
        <h2>发布新帖子</h2>
        <input v-model="newPostTitle" placeholder="标题">
        <textarea v-model="newPostContent" placeholder="内容"></textarea>
        <button @click="createPost">发布</button>
        <button @click="showNewPost = false">关闭</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      interests: ['阅读', '旅行', '音乐', '烹饪', '摄影'],
      posts: [],
      isLoggedIn: false,
      showRegister: false,
      showLogin: false,
      showNewPost: false,
      registerUsername: '',
      registerPassword: '',
      loginUsername: '',
      loginPassword: '',
      newPostTitle: '',
      newPostContent: ''
    }
  },
  methods: {
    showRegisterForm() {
      this.showRegister = true;
    },
    showLoginForm() {
      this.showLogin = true;
    },
    showNewPostForm() {
      this.showNewPost = true;
    },
    async register() {
      try {
        const response = await axios.post('http://localhost:3000/api/register', {
          username: this.registerUsername,
          password: this.registerPassword
        });
        alert(response.data.message);
        this.showRegister = false;
      } catch (error) {
        console.error('注册错误:', error);
        alert('注册失败');
      }
    },
    async login() {
      try {
        const response = await axios.post('http://localhost:3000/api/login', {
          username: this.loginUsername,
          password: this.loginPassword
        });
        if (response.data.success) {
          this.isLoggedIn = true;
          this.showLogin = false;
          localStorage.setItem('token', response.data.token);
          alert('登录成功');
        } else {
          alert('登录失败');
        }
      } catch (error) {
        console.error('登录错误:', error);
        alert('登录出错');
      }
    },
    logout() {
      this.isLoggedIn = false;
      localStorage.removeItem('token');
    },
    async createPost() {
      try {
        const response = await axios.post('http://localhost:3000/api/posts', {
          title: this.newPostTitle,
          content: this.newPostContent
        }, {
          headers: { Authorization: `Bearer ${localStorage.getItem('token')}` }
        });
        alert(response.data.message);
        this.showNewPost = false;
        this.fetchPosts();
      } catch (error) {
        console.error('发帖错误:', error);
        alert('发帖失败');
      }
    },
    async fetchPosts() {
      try {
        const response = await axios.get('http://localhost:3000/api/posts');
        this.posts = response.data;
      } catch (error) {
        console.error('获取帖子错误:', error);
      }
    }
  },
  mounted() {
    this.fetchPosts();
    if (localStorage.getItem('token')) {
      this.isLoggedIn = true;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0,0,0);
  background-color: rgba(0,0,0,0.4);
}

.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}
</style>