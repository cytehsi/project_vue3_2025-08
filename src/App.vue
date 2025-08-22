<template>
  <div class="app-container">
    <!-- mobile漢堡按鈕 -->
    <div class="mobile-menu">
      <button class="mobile-menu-btn" @click="toggleMobileMenu" v-show="isMobile">☰</button>
    </div>

    <!-- mobile灰色遮罩 -->
    <div v-if="isMobile && showMobileMenu" class="mobile-overlay" @click="closeMobileMenu"></div>

    <!-- Sidebar -->
    <Sidebar
      :todos="todos"
      :selectedId="selectedId"
      :class="{ 'mobile-open': showMobileMenu }"
      @select="handleSelect"
      @add="handleAdd"
      @close="closeMobileMenu"
    />

    <!-- TodoForm -->
    <TodoForm
      v-if="currentTodo"
      v-model="todos[selectedIndex]"
      :class="{ 'mobile-blur': isMobile && showMobileMenu }"
      @delete="handleDelete"
    />
  </div>
</template>

<script>
import Sidebar from './components/Sidebar.vue'
import TodoForm from './components/TodoForm.vue'

export default {
  components: { Sidebar, TodoForm },
  data() {
    return {
      todos: [{ id: 1, title: 'Item title', dateStart: '', dateEnd: '', image: '', content: '' }],
      selectedId: 1,
      showMobileMenu: false,
      isMobile: false,
    }
  },
  computed: {
    selectedIndex() {
      return this.todos.findIndex((t) => t.id === this.selectedId)
    },
    currentTodo() {
      return this.todos[this.selectedIndex]
    },
  },
  mounted() {
    this.checkMobile()
    window.addEventListener('resize', this.checkMobile)
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkMobile)
  },
  methods: {
    handleSelect(id) {
      this.selectedId = id
      this.closeMobileMenu() // 選擇項目後關閉mobile選單
    },
    handleAdd() {
      if (this.todos.length >= 10) return
      const newId = Date.now()
      this.todos.push({
        id: newId,
        title: '',
        dateStart: '',
        dateEnd: '',
        image: '',
        content: '',
      })
      this.selectedId = newId
      this.closeMobileMenu()
    },
    handleDelete(id) {
      this.todos = this.todos.filter((t) => t.id !== id)
      this.selectedId = this.todos.length ? this.todos[0].id : null
    },
    checkMobile() {
      this.isMobile = window.innerWidth <= 768
      if (!this.isMobile) {
        this.showMobileMenu = false
      }
    },
    toggleMobileMenu() {
      this.showMobileMenu = !this.showMobileMenu
    },
    closeMobileMenu() {
      this.showMobileMenu = false
    },
  },
}
</script>

<style>
.app-container {
  display: flex;
  height: 100vh;
  font-family: 'Montserrat', sans-serif;
  position: relative;
}

/* mobile灰色遮罩 */
.mobile-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

/* mobile內容模糊效果 */
.mobile-blur {
  filter: blur(2px);
  pointer-events: none;
}

@media (max-width: 768px) {
  .app-container {
    flex-direction: column;
  }
  .mobile-menu {
    display: flex;
    width: 100%;
    position: relative;
    padding: 10px 20px;
  }

  /* mobile漢堡按鈕 */
  .mobile-menu-btn {
    border: none;
    font-size: 24px;
    cursor: pointer;
    background-color: transparent;
    font-weight: bold;
    padding: 0;
    transition: color 0.3s ease;
  }

  .mobile-menu-btn:hover {
    color: #81f8b1;
  }
  /*mobile Sidebar 樣式 */
  .sidebar {
    position: fixed;
    left: -100%;
    top: 0;
    width: 70% !important;
    min-width: 250px !important;
    height: 100vh;
    z-index: 1000;
    transition: left 0.3s ease;
    box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  }

  .sidebar.mobile-open {
    left: 0;
  }

  .form {
    min-width: 300px;
  }

  .form-header {
    flex-direction: column;
    gap: 15px;
  }

  .form-group {
    width: 100%;
  }
  .file-upload-btn {
    width: auto !important;
  }

  .form-group .item-title {
    width: auto !important;
  }

  .date-group {
    justify-content: flex-start;
    gap: 10px;
  }

  .date-input {
    width: auto;
  }

  /* mobile圖片區塊調整 */
  .form-image-section {
    flex-direction: column;
    gap: 15px;
  }

  .form-left,
  .form-right {
    width: 100%;
  }

  .image-url {
    width: auto !important;
  }

  .preview {
    margin-top: 0;
    height: 200px;
  }

  .delete-btn {
    position: fixed;
    top: 12px !important;
    right: 10px;

    color: white;
    border-radius: 6px;
  }

  .form-section textarea {
    min-height: 120px;
  }
}

@media (min-width: 769px) {
  .mobile-menu-btn {
    display: none;
  }

  .sidebar {
    position: static;
  }
}
</style>
