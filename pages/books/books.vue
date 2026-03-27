<template>
  <view class="container">
    <view class="book-list">
      <view
        v-for="book in books"
        :key="book.id"
        :class="['book-item', book.id === currentBookId ? 'active' : '']"
        @click="selectBook(book)"
      >
        <view class="book-icon" :style="{ backgroundColor: book.color }">
          <text>{{ book.icon }}</text>
        </view>
        <view class="book-info">
          <text class="book-name">{{ book.name }}</text>
          <text class="book-balance">余额: ¥{{ book.balance.toFixed(2) }}</text>
        </view>
        <text v-if="book.id === currentBookId" class="check-icon">✓</text>
      </view>
    </view>

    <view class="add-book-button" @click="showAddDialog">
      <text>+ 创建新账本</text>
    </view>

    <!-- 创建账本弹窗 -->
    <view v-if="showDialog" class="dialog-mask" @click="closeDialog">
      <view class="dialog" @click.stop>
        <view class="dialog-title">创建账本</view>

        <view class="form-item">
          <text class="form-label">账本名称</text>
          <input
            class="form-input"
            v-model="newBook.name"
            placeholder="例如：旅游基金"
          />
        </view>

        <view class="form-item">
          <text class="form-label">选择图标</text>
          <view class="icon-grid">
            <view
              v-for="icon in icons"
              :key="icon"
              :class="['icon-item', newBook.icon === icon ? 'active' : '']"
              @click="newBook.icon = icon"
            >
              <text>{{ icon }}</text>
            </view>
          </view>
        </view>

        <view class="form-item">
          <text class="form-label">选择颜色</text>
          <view class="color-grid">
            <view
              v-for="color in colors"
              :key="color"
              :class="['color-item', newBook.color === color ? 'active' : '']"
              :style="{ backgroundColor: color }"
              @click="newBook.color = color"
            ></view>
          </view>
        </view>

        <view class="dialog-buttons">
          <view class="dialog-button cancel" @click="closeDialog">取消</view>
          <view class="dialog-button confirm" @click="createBook">创建</view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      books: [],
      currentBookId: 0,
      showDialog: false,
      newBook: {
        name: '',
        icon: '💰',
        color: '#4CAF50'
      },
      icons: ['💰', '🏦', '💳', '🎯', '🏠', '✈️', '🎓', '💼', '🎁', '📱'],
      colors: ['#4CAF50', '#2196F3', '#9C27B0', '#FF9800', '#F44336', '#E91E63']
    }
  },
  onLoad() {
    this.loadBooks()
  },
  methods: {
    loadBooks() {
      this.books = uni.getStorageSync('account_books') || []
      this.currentBookId = uni.getStorageSync('current_book_id')
    },
    selectBook(book) {
      uni.setStorageSync('current_book_id', book.id)
      uni.showToast({
        title: '已切换账本',
        icon: 'success'
      })
      setTimeout(() => {
        uni.navigateBack()
      }, 1000)
    },
    showAddDialog() {
      this.showDialog = true
      this.newBook = {
        name: '',
        icon: '💰',
        color: '#4CAF50'
      }
    },
    closeDialog() {
      this.showDialog = false
    },
    createBook() {
      if (!this.newBook.name) {
        uni.showToast({
          title: '请输入账本名称',
          icon: 'none'
        })
        return
      }

      const book = {
        id: Date.now(),
        name: this.newBook.name,
        icon: this.newBook.icon,
        color: this.newBook.color,
        balance: 0,
        createdAt: new Date().toISOString()
      }

      this.books.push(book)
      uni.setStorageSync('account_books', this.books)

      uni.showToast({
        title: '创建成功',
        icon: 'success'
      })

      this.closeDialog()
    }
  }
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding: 30rpx;
}

.book-list {
  display: flex;
  flex-direction: column;
  gap: 20rpx;
}

.book-item {
  background: white;
  border-radius: 20rpx;
  padding: 30rpx;
  display: flex;
  align-items: center;
  box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
}

.book-item.active {
  border: 2rpx solid #667eea;
}

.book-icon {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 50rpx;
  margin-right: 20rpx;
}

.book-info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.book-name {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
}

.book-balance {
  font-size: 28rpx;
  color: #666;
}

.check-icon {
  font-size: 40rpx;
  color: #667eea;
}

.add-book-button {
  margin-top: 40rpx;
  padding: 30rpx;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  text-align: center;
  border-radius: 20rpx;
  font-size: 32rpx;
  font-weight: bold;
}

.dialog-mask {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}

.dialog {
  width: 600rpx;
  background: white;
  border-radius: 20rpx;
  padding: 40rpx;
}

.dialog-title {
  font-size: 36rpx;
  font-weight: bold;
  text-align: center;
  margin-bottom: 40rpx;
}

.form-item {
  margin-bottom: 30rpx;
}

.form-label {
  font-size: 28rpx;
  color: #666;
  margin-bottom: 15rpx;
  display: block;
}

.form-input {
  padding: 20rpx;
  background: #f5f5f5;
  border-radius: 10rpx;
  font-size: 28rpx;
}

.icon-grid {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 15rpx;
}

.icon-item {
  padding: 15rpx;
  text-align: center;
  background: #f5f5f5;
  border-radius: 10rpx;
  font-size: 40rpx;
}

.icon-item.active {
  background: #667eea;
}

.color-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 15rpx;
}

.color-item {
  width: 60rpx;
  height: 60rpx;
  border-radius: 10rpx;
}

.color-item.active {
  border: 3rpx solid #333;
}

.dialog-buttons {
  display: flex;
  gap: 20rpx;
  margin-top: 40rpx;
}

.dialog-button {
  flex: 1;
  padding: 25rpx;
  text-align: center;
  border-radius: 10rpx;
  font-size: 30rpx;
}

.dialog-button.cancel {
  background: #f5f5f5;
  color: #666;
}

.dialog-button.confirm {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}
</style>
