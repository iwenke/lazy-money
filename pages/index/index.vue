<template>
  <view class="container">
    <!-- 账本头部 -->
    <view class="header">
      <view class="book-info" @click="goToBooks">
        <text class="book-icon">{{ currentBook.icon }}</text>
        <text class="book-name">{{ currentBook.name }}</text>
        <text class="arrow">▼</text>
      </view>
      <view class="balance">
        <text class="balance-label">余额</text>
        <text class="balance-amount">¥{{ currentBook.balance.toFixed(2) }}</text>
      </view>
    </view>

    <!-- 统计卡片 -->
    <view class="stats-card">
      <view class="stat-item">
        <text class="stat-label">本月支出</text>
        <text class="stat-value expense">¥{{ monthExpense.toFixed(2) }}</text>
      </view>
      <view class="stat-divider"></view>
      <view class="stat-item">
        <text class="stat-label">本月收入</text>
        <text class="stat-value income">¥{{ monthIncome.toFixed(2) }}</text>
      </view>
    </view>

    <!-- 交易记录 -->
    <view class="transactions">
      <view class="section-header">
        <text class="section-title">最近记录</text>
        <text class="view-all" @click="goToBooks">查看全部 ›</text>
      </view>
      <view v-if="transactions.length === 0" class="empty">
        <text class="empty-icon">📝</text>
        <text class="empty-text">还没有记录，点击下方按钮开始记账</text>
      </view>
      <view v-else class="transaction-list">
        <view v-for="item in transactions" :key="item.id" class="transaction-item">
          <view class="transaction-icon-wrapper">
            <text class="transaction-icon">{{ getCategoryIcon(item.category, item.type) }}</text>
          </view>
          <view class="transaction-left">
            <text class="transaction-category">{{ item.category }}</text>
            <text class="transaction-note" v-if="item.note">{{ item.note }}</text>
            <text class="transaction-date">{{ formatDate(item.date) }}</text>
          </view>
          <text :class="['transaction-amount', item.type === 0 ? 'expense' : 'income']">
            {{ item.type === 0 ? '-' : '+' }}¥{{ item.amount.toFixed(2) }}
          </text>
        </view>
      </view>
    </view>

    <!-- 添加按钮 -->
    <view class="add-button" @click="goToAdd">
      <text class="add-icon">+</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      currentBook: {
        id: 0,
        name: '默认账本',
        icon: '💰',
        color: '#4CAF50',
        balance: 0
      },
      transactions: [],
      monthExpense: 0,
      monthIncome: 0
    }
  },
  onShow() {
    this.loadData()
  },
  methods: {
    loadData() {
      // 加载当前账本
      const bookId = uni.getStorageSync('current_book_id')
      const books = uni.getStorageSync('account_books') || []
      const book = books.find(b => b.id === bookId)
      if (book) {
        this.currentBook = book
      }

      // 加载交易记录
      const allTransactions = uni.getStorageSync('transactions') || []
      this.transactions = allTransactions
        .filter(t => t.accountBookId === this.currentBook.id)
        .sort((a, b) => new Date(b.date) - new Date(a.date))
        .slice(0, 20)

      // 计算本月收支
      this.calculateMonthStats()
    },
    calculateMonthStats() {
      const now = new Date()
      const year = now.getFullYear()
      const month = now.getMonth()

      const allTransactions = uni.getStorageSync('transactions') || []
      const monthTransactions = allTransactions.filter(t => {
        const date = new Date(t.date)
        return t.accountBookId === this.currentBook.id &&
               date.getFullYear() === year &&
               date.getMonth() === month
      })

      this.monthExpense = monthTransactions
        .filter(t => t.type === 0)
        .reduce((sum, t) => sum + t.amount, 0)

      this.monthIncome = monthTransactions
        .filter(t => t.type === 1)
        .reduce((sum, t) => sum + t.amount, 0)
    },
    formatDate(dateStr) {
      const date = new Date(dateStr)
      const today = new Date()
      const yesterday = new Date(today)
      yesterday.setDate(yesterday.getDate() - 1)

      if (date.toDateString() === today.toDateString()) {
        return '今天'
      } else if (date.toDateString() === yesterday.toDateString()) {
        return '昨天'
      } else {
        return `${date.getMonth() + 1}月${date.getDate()}日`
      }
    },
    goToAdd() {
      uni.navigateTo({
        url: '/pages/add/add'
      })
    },
    goToBooks() {
      uni.navigateTo({
        url: '/pages/books/books'
      })
    },
    getCategoryIcon(category, type) {
      const expenseIcons = {
        '餐饮': '🍽️',
        '购物': '🛍️',
        '交通': '🚗',
        '娱乐': '🎮',
        '医疗': '💊',
        '住房': '🏠',
        '教育': '📚',
        'AA收款': '👥',
        '红包': '🧧',
        '转账': '💸',
        '其他': '📦'
      }
      const incomeIcons = {
        '工资': '💰',
        '奖金': '🎁',
        '兼职': '💼',
        '理财': '📈',
        '红包': '🧧',
        '转账': '💸',
        '其他': '💵'
      }
      if (type === 0) {
        return expenseIcons[category] || '💳'
      } else {
        return incomeIcons[category] || '💰'
      }
    }
  }
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: 100rpx;
}

.header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  padding: 40rpx 30rpx 50rpx;
  color: white;
}

.book-info {
  display: flex;
  align-items: center;
  margin-bottom: 30rpx;
}

.book-icon {
  font-size: 48rpx;
  margin-right: 20rpx;
}

.book-name {
  font-size: 32rpx;
  font-weight: bold;
  flex: 1;
}

.arrow {
  font-size: 24rpx;
}

.balance {
  display: flex;
  flex-direction: column;
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

.stats-card {
  background: white;
  margin: -30rpx 30rpx 30rpx;
  border-radius: 20rpx;
  padding: 40rpx;
  display: flex;
  box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.08);
}

.stat-item {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.stat-divider {
  width: 2rpx;
  background-color: #f0f0f0;
}

.stat-label {
  font-size: 28rpx;
  color: #999;
  margin-bottom: 15rpx;
}

.stat-value {
  font-size: 40rpx;
  font-weight: bold;
}

.stat-value.expense {
  color: #ff4757;
}

.stat-value.income {
  color: #2ed573;
}

.transactions {
  padding: 0 30rpx;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.view-all {
  font-size: 26rpx;
  color: #999;
}

.empty {
  background: white;
  border-radius: 20rpx;
  padding: 80rpx 40rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.empty-icon {
  font-size: 100rpx;
  margin-bottom: 20rpx;
}

.empty-text {
  font-size: 28rpx;
  color: #999;
}

.transaction-list {
  background: white;
  border-radius: 20rpx;
  overflow: hidden;
}

.transaction-item {
  display: flex;
  align-items: center;
  padding: 25rpx 30rpx;
  border-bottom: 1rpx solid #f5f5f5;
}

.transaction-item:last-child {
  border-bottom: none;
}

.transaction-icon-wrapper {
  width: 80rpx;
  height: 80rpx;
  background: linear-gradient(135deg, #f5f7fa 0%, #e8ecf1 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 20rpx;
}

.transaction-icon {
  font-size: 40rpx;
}

.transaction-left {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.transaction-category {
  font-size: 32rpx;
  color: #333;
  margin-bottom: 8rpx;
}

.transaction-note {
  font-size: 24rpx;
  color: #999;
  margin-bottom: 8rpx;
}

.transaction-date {
  font-size: 24rpx;
  color: #ccc;
}

.transaction-amount {
  font-size: 36rpx;
  font-weight: bold;
}

.transaction-amount.expense {
  color: #ff4757;
}

.transaction-amount.income {
  color: #2ed573;
}

.add-button {
  position: fixed;
  right: 40rpx;
  bottom: 120rpx;
  width: 120rpx;
  height: 120rpx;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8rpx 24rpx rgba(102, 126, 234, 0.4);
}

.add-icon {
  font-size: 60rpx;
  color: white;
  font-weight: 300;
}
</style>
