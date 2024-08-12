<template>
  <div id="app">
    <header>
      <h1>兴趣交流平台</h1>
      <nav>
        <button @click="showRegisterForm">注册</button>
        <button @click="showNewPostForm">发帖</button>
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
    
    <!-- 注册表单和发帖表单的模态框 -->
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      interests: ['阅读', '旅行', '音乐', '烹饪', '摄影'],
      posts: []
    }
  },
  methods: {
    showRegisterForm() {
      // 显示注册表单
    },
    showNewPostForm() {
      // 显示发帖表单
    },
    fetchPosts() {
      // 从后端API获取帖子
      fetch('http://localhost:3000/api/posts')
        .then(response => response.json())
        .then(data => {
          this.posts = data;
        })
        .catch(error => console.error('Error:', error));
    }
  },
  mounted() {
    this.fetchPosts();
  }
}
</script>