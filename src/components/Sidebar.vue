<template>
  <div class="sidebar">
    <!-- mobile才顯示叉叉 -->
    <button class="close-btn" v-if="isMobile" @click="$emit('close')">✖</button>

    <h2 class="sidebar-title">Demo Todo List</h2>
    <ul ref="listRef">
      <li
        v-for="(todo, index) in todos"
        :key="todo.id"
        :class="{ active: todo.id === selectedId }"
        @click="$emit('select', todo.id)"
        class="todo-item"
        :ref="(el) => setItemRef(el, todo.id)"
      >
        {{ index + 1 }}. {{ todo.title }}
      </li>
      <!-- 固定一個三角形，透過 transform 移動 -->
      <transition name="triangle-move">
        <span
          v-if="triangleY !== null"
          class="triangle"
          :style="{ transform: `translateY(${triangleY}px)` }"
        ></span>
      </transition>
    </ul>

    <button class="add-btn" @click="$emit('add')" :disabled="todos.length >= 10">Add Item</button>

    <div class="random-image-box">
      <img :src="imageUrl" alt="隨機圖片" class="random-img" @click="updateImage" />
    </div>
  </div>
</template>

<script>
import { ref, watch, nextTick, onMounted, onBeforeUnmount } from 'vue'

export default {
  props: {
    todos: Array,
    selectedId: Number,
  },
  emits: ['select', 'add', 'close'],
  setup(props) {
    const listRef = ref(null)
    const itemRefs = ref({})
    const triangleY = ref(null)

    // --------- 判斷是否mobile ---------
    const isMobile = ref(window.innerWidth <= 768)
    const handleResize = () => {
      isMobile.value = window.innerWidth <= 768
    }
    onMounted(() => window.addEventListener('resize', handleResize))
    onBeforeUnmount(() => window.removeEventListener('resize', handleResize))

    // --------- 隨機圖片 ---------
    const imageUrl = ref('https://picsum.photos/300/200')
    const updateImage = () => {
      imageUrl.value = `https://picsum.photos/300/200?random=${Date.now()}`
    }

    // --------- 三角形移動 ---------
    const setItemRef = (el, id) => {
      if (el) itemRefs.value[id] = el
    }
    watch(
      () => props.selectedId,
      async (newId) => {
        await nextTick()
        const el = itemRefs.value[newId]
        if (el) {
          triangleY.value = el.offsetTop + el.offsetHeight / 2 - 8
        }
      },
      { immediate: true },
    )
    return { listRef, setItemRef, triangleY, imageUrl, updateImage, isMobile }
  },
}
</script>

<style scoped>
.sidebar {
  min-width: 250px;
  width: 250px;
  background: #aeffc7;
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.sidebar-title {
  margin: 20px;
}

.sidebar ul {
  list-style: none;
  padding: 0;
  margin: 0;
  position: relative;
}

.sidebar li {
  cursor: pointer;
  padding: 10px 20px;
  position: relative;
}

.sidebar li:hover {
  background: #81f8b1;
  transition: background 0.3s ease;
}

.sidebar li.active {
  font-weight: bold;
  color: #000;
}

.triangle {
  position: absolute;
  right: 0;
  width: 0;
  height: 0;
  top: -10px;
  border-top: 18px solid transparent;
  border-bottom: 18px solid transparent;
  border-right: 20px solid white;
  transition: transform 0.3s ease;
}

button:disabled {
  background: #e7ffe9;
  cursor: not-allowed;
  color: #ebebeb;
}

.add-btn {
  background: #e7ffe9;
  border-radius: 10px;
  border: none;
  width: 80%;
  padding: 10px 15px;
  cursor: pointer;
  margin: 20px;
  transition: background 0.3s ease;
}

.add-btn:hover {
  background: #81f8b1;
}

/* --------- 隨機圖片區塊 --------- */
.random-image-box {
  margin: 20px;
  margin-top: auto;
  text-align: center;
}

.random-img {
  width: 100%;
  cursor: pointer;
  border-radius: 8px;
  transition: transform 0.2s ease;
}

.random-img:hover {
  transform: scale(1.03);
}

.close-btn {
  display: none;
}

@media (max-width: 768px) {
  .close-btn {
    display: block;
    position: absolute;
    top: 16px;
    right: 10px;
    border: none;
    background: transparent;
    font-size: 24px;
    cursor: pointer;
  }
}
</style>
