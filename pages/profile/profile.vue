<template>
  <view class="container">
    <!-- 用户信息区域 -->
    <view class="user-section">
      <view class="user-info">
        <image class="avatar" src="/static/avatar.png" mode="aspectFill"></image>
        <view class="user-details">
          <text class="username">{{ username }}</text>
          <text class="user-id">ID: {{ userId }}</text>
        </view>
      </view>

      <!-- 总余额显示 -->
      <view class="total-balance">
        <text class="balance-label">总余额</text>
        <text class="balance-amount">¥{{ totalBalance.toFixed(2) }}</text>
      </view>
    </view>

    <!-- 功能菜单 -->
    <view class="menu-section">
      <view class="menu-item" @click="goToImport">
        <view class="menu-left">
          <text class="menu-icon">📥</text>
          <text class="menu-text">账单导入</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>

      <view class="menu-item" @click="goToBills">
        <view class="menu-left">
          <text class="menu-icon">📋</text>
          <text class="menu-text">账单</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>

      <view class="menu-item" @click="goToBudget">
        <view class="menu-left">
          <text class="menu-icon">💰</text>
          <text class="menu-text">预算</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>

      <view class="menu-item" @click="goToAssets">
        <view class="menu-left">
          <text class="menu-icon">💳</text>
          <text class="menu-text">资产</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>

      <view class="menu-item" @click="goToBooks">
        <view class="menu-left">
          <text class="menu-icon">📖</text>
          <text class="menu-text">账本</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>
    </view>

    <!-- 设置菜单 -->
    <view class="menu-section">
      <view class="menu-item" @click="goToSettings">
        <view class="menu-left">
          <text class="menu-icon">⚙️</text>
          <text class="menu-text">设置</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>

      <view class="menu-item" @click="goToAbout">
        <view class="menu-left">
          <text class="menu-icon">ℹ️</text>
          <text class="menu-text">关于</text>
        </view>
        <text class="menu-arrow">›</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      username: '懒人用户',
      userId: '10001',
      totalBalance: 0
    }
  },
  onShow() {
    this.loadUserData()
  },
  methods: {
    loadUserData() {
      // 加载用户信息
      const savedUsername = uni.getStorageSync('username')
      if (savedUsername) {
        this.username = savedUsername
      }

      // 计算总余额
      const books = uni.getStorageSync('account_books') || []
      this.totalBalance = books.reduce((sum, book) => sum + (book.balance || 0), 0)
    },
    goToImport() {
      uni.navigateTo({
        url: '/pages/import/import'
      })
    },
    goToBills() {
      uni.navigateTo({
        url: '/pages/index/index'
      })
    },
    goToBudget() {
      uni.showToast({
        title: '预算功能开发中',
        icon: 'none'
      })
    },
    goToAssets() {
      uni.showToast({
        title: '资产功能开发中',
        icon: 'none'
      })
    },
    goToBooks() {
      uni.navigateTo({
        url: '/pages/books/books'
      })
    },
    goToSettings() {
      uni.showToast({
        title: '设置功能开发中',
        icon: 'none'
      })
    },
    goToAbout() {
      uni.showModal({
        title: '关于懒人记账',
        content: '版本: 1.0.0\n简单易用的记账应用',
        showCancel: false
      })
    }
  }
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: 40rpx;
}

.user-section {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 60rpx 30rpx 40rpx;
  color: white;
}

.user-info {
  display: flex;
  align-items: center;
  margin-bottom: 40rpx;
}

.avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  background-color: rgba(255, 255, 255, 0.3);
  margin-right: 30rpx;
}

.user-details {
  display: flex;
  flex-direction: column;
}

.username {
  font-size: 40rpx;
  font-weight: bold;
  margin-bottom: 10rpx;
}

.user-id {
  font-size: 26rpx;
  opacity: 0.8;
}

.total-balance {
  background: rgba(255, 255, 255, 0.2);
  border-radius: 20rpx;
  padding: 30rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.balance-label {
  font-size: 28rpx;
  opacity: 0.9;
  margin-bottom: 10rpx;
}

.balance-amount {
  font-size: 56rpx;
  font-weight: bold;
}

.menu-section {
  background: white;
  margin: 20rpx 30rpx;
  border-radius: 20rpx;
  overflow: hidden;
}

.menu-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 30rpx;
  border-bottom: 1rpx solid #f5f5f5;
}

.menu-item:last-child {
  border-bottom: none;
}

.menu-left {
  display: flex;
  align-items: center;
}

.menu-icon {
  font-size: 44rpx;
  margin-right: 20rpx;
}

.menu-text {
  font-size: 32rpx;
  color: #333;
}

.menu-arrow {
  font-size: 48rpx;
  color: #ccc;
  font-weight: 300;
}
</style>
