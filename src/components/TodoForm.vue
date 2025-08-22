<template>
  <div class="form">
    <button class="delete-btn" @click="$emit('delete', todo.id)">🗑️</button>
    <div class="form-header">
      <div class="form-group">
        <label>Title</label>
        <input class="item-title" v-model="todo.title" type="text" />
      </div>

      <div class="form-group">
        <label>Date</label>
        <div class="date-group">
          <input class="date-input" v-model="todo.dateStart" type="date" @change="validateDates" />
          <span>~</span>
          <input class="date-input" v-model="todo.dateEnd" type="date" @change="validateDates" />
        </div>
      </div>
    </div>

    <div v-if="dateError" class="error-msg">{{ dateError }}</div>

    <div class="form-image-section">
      <!-- 左欄 -->
      <div class="form-left">
        <label>Image</label>
        <input
          :key="fileInputKey"
          type="file"
          @change="handleFileUpload"
          placeholder="Upload Image"
          class="file-upload-btn"
          accept="image/*"
        />
        <p>or</p>
        <input class="image-url" v-model="todo.image" placeholder="輸入圖片網址" />
      </div>

      <!-- 右欄 -->
      <div class="form-right">
        <div class="preview">
          <img v-if="todo.image" :src="todo.image" alt="preview" />
        </div>
      </div>
    </div>

    <div class="form-section">
      <label>Content</label>
      <textarea v-model="todo.content" placeholder="content..." maxlength="200"></textarea>
      <div class="counter">{{ todo.content.length }}/200</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    modelValue: Object,
  },
  emits: ['update:modelValue', 'delete'],
  data() {
    return {
      dateError: '',
      fileInputKey: 0, // 強制重新渲染文件輸入框
    }
  },
  computed: {
    todo: {
      get() {
        return this.modelValue
      },
      set(val) {
        this.$emit('update:modelValue', val)
      },
    },
  },
  watch: {
    'modelValue.id'(newId, oldId) {
      // 強制重新渲染文件輸入框
      if (newId !== oldId) {
        this.fileInputKey++
      }
    },
  },
  methods: {
    handleFileUpload(event) {
      const file = event.target.files[0]
      if (file) {
        this.todo.image = URL.createObjectURL(file)
      }
    },
    validateDates() {
      const start = this.todo.dateStart ? new Date(this.todo.dateStart) : null
      const end = this.todo.dateEnd ? new Date(this.todo.dateEnd) : null

      this.dateError = ''

      if (start && end) {
        if (start.getTime() === end.getTime()) {
          this.dateError = '起始日期與結束日期不能相同'
        } else if (start > end) {
          this.dateError = '起始日期不得大於結束日期'
        } else if (end < start) {
          this.dateError = '結束日期不得小於起始日期'
        }
      }
    },
  },
}
</script>

<style scoped>
.form {
  flex: 1;
  padding: 20px;
}

/* ----------- 第一區塊 ----------- */
.form-header {
  display: flex;
  align-items: flex-start;
  /* gap: 20px; */
}

.form-group {
  display: flex;
  flex-direction: column;
  flex: 1;
}
.form-group .item-title {
  width: 90%;
  padding: 8px 10px;
}

.form-group label {
  font-weight: bold;
  margin-bottom: 5px;
}

.form-group input {
  padding: 6px 10px;
  border-radius: 6px;
  border: none;
  background: #ebebeb;
}

.date-group {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.delete-btn {
  position: relative;
  margin-left: auto;
  background: none;
  border: none;
  cursor: pointer;
  font-size: 20px;
  float: right;
}

.form-section {
  margin-top: 20px;
  display: flex;
  flex-direction: column;
  position: relative;
}

.form-section label {
  margin-bottom: 5px;
  font-weight: bold;
}
.date-input {
  width: 40%;
}

/* ----------- 第二區塊 ----------- */
.form-image-section {
  display: flex;
  align-items: flex-start;
  margin-top: 20px;
}

.form-left {
  display: flex;
  flex-direction: column;
  gap: 10px;
  flex: 1;
}

.form-left label {
  font-weight: bold;
  margin-bottom: 5px;
}

.form-left p {
  text-align: center;
  margin: 0;
  font-weight: bold;
}

.form-right {
  flex: 1;
}

.preview {
  width: 100%;
  height: 107px;
  background-color: #ebebeb;
  border-radius: 6px;
  overflow: hidden;
  display: flex;
  justify-content: center;
  /* align-items: flex-end; */
  margin-top: 32px;
}

.preview img {
  max-width: 100%;
  max-height: 100%;
  object-fit: cover;
  border-radius: 8px;
}
.file-upload-btn {
  background-color: #e6f7e6;
  display: inline-block;
  text-align: center;
  padding: 8px 10px;
  color: #000;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  width: 90%;
  transition: background 0.3s ease;
}
.image-url {
  width: 90%;
  padding: 8px 10px !important;
}

.file-upload-btn:hover {
  background-color: #81f8b1;
}

.file-upload-btn::file-selector-button {
  display: none;
}

/* ----------- 第三區塊----------- */

.form-section textarea {
  width: 100%;
  padding: 10px;
  padding-bottom: 25px;
  border: none;
  outline: none;
  box-sizing: border-box;
  resize: vertical;
  min-height: 107px;
}

.counter {
  position: absolute;
  bottom: 0px;
  right: 0px;
  font-size: 12px;
  color: #666;
  background-color: #e6f7e6;
  padding: 6px 10px 6px 16px;
  pointer-events: none;
  clip-path: polygon(
    12px 0%,
    calc(100%) 0%,
    100% 6px,
    100% calc(100% - 6px),
    calc(100% - 6px) 100%,
    0% 100%
  );
}

/* ----------- content----------- */
textarea,
input[type='text'],
.image-url {
  /* width: 100%; */
  padding: 6px 10px;
  border-radius: 6px;
  border: none;
  background: #ebebeb;
}

/* ----------- 小工具 ----------- */

.delete-btn {
  position: absolute;
  margin-left: auto;
  background: none;
  border: none;
  cursor: pointer;
  font-size: 20px;
  right: 10px;
  top: 5px;
}

.error-msg {
  color: red;
  font-size: 14px;
  margin-top: 5px;
  text-align: right;
}
</style>
