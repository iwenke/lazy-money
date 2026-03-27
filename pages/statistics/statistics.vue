<template>
  <view class="container">
    <!-- 月份选择 -->
    <view class="month-selector">
      <text @click="prevMonth">◀</text>
      <picker mode="date" fields="month" :value="currentMonth" @change="onMonthChange">
        <view class="month-text">{{ formatMonth(currentMonth) }}</view>
      </picker>
      <text @click="nextMonth">▶</text>
    </view>

    <!-- 收支统计 -->
    <view class="summary-card">
      <view class="summary-item">
        <text class="summary-label">支出</text>
        <text class="summary-value expense">¥{{ totalExpense.toFixed(2) }}</text>
      </view>
      <view class="summary-divider"></view>
      <view class="summary-item">
        <text class="summary-label">收入</text>
        <text class="summary-value income">¥{{ totalIncome.toFixed(2) }}</text>
      </view>
      <view class="summary-divider"></view>
      <view class="summary-item">
        <text class="summary-label">结余</text>
        <text class="summary-value">¥{{ (totalIncome - totalExpense).toFixed(2) }}</text>
      </view>
    </view>

    <!-- 支出分类统计 -->
    <view class="category-stats">
      <view class="section-title">
        <text>支出分类</text>
        <text class="section-subtitle">本月消费分析</text>
      </view>
      <view v-if="expenseByCategory.length === 0" class="empty">
        <text>本月暂无支出记录</text>
      </view>
      <view v-else class="category-list">
        <view v-for="item in expenseByCategory" :key="item.category" class="category-item">
          <view class="category-info">
            <view class="category-header">
              <text class="category-name">{{ item.category }}</text>
              <text class="category-amount">¥{{ item.amount.toFixed(2) }}</text>
            </view>
            <view class="category-bar-container">
              <view class="category-bar" :style="{ width: item.percent + '%' }"></view>
            </view>
            <view class="category-footer">
              <text class="category-count">{{ item.count }}笔</text>
              <text class="category-percent">{{ item.percent }}%</text>
            </view>
          </view>
        </view>
      </view>
    </view>

    <!-- 收入分类统计 -->
    <view class="category-stats">
      <view class="section-title">
        <text>收入分类</text>
        <text class="section-subtitle">本月收入分析</text>
      </view>
      <view v-if="incomeByCategory.length === 0" class="empty">
        <text>本月暂无收入记录</text>
      </view>
      <view v-else class="category-list">
        <view v-for="item in incomeByCategory" :key="item.category" class="category-item">
          <view class="category-info">
            <view class="category-header">
              <text class="category-name">{{ item.category }}</text>
              <text class="category-amount income">¥{{ item.amount.toFixed(2) }}</text>
            </view>
            <view class="category-bar-container">
              <view class="category-bar income-bar" :style="{ width: item.percent + '%' }"></view>
            </view>
            <view class="category-footer">
              <text class="category-count">{{ item.count }}笔</text>
              <text class="category-percent">{{ item.percent }}%</text>
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      currentMonth: new Date().toISOString().split('T')[0].substring(0, 7),
      totalExpense: 0,
      totalIncome: 0,
      expenseByCategory: [],
      incomeByCategory: []
    }
  },
  onShow() {
    this.loadStatistics()
  },
  methods: {
    formatMonth(monthStr) {
      const [year, month] = monthStr.split('-')
      return `${year}年${month}月`
    },
    onMonthChange(e) {
      this.currentMonth = e.detail.value
      this.loadStatistics()
    },
    prevMonth() {
      const date = new Date(this.currentMonth + '-01')
      date.setMonth(date.getMonth() - 1)
      this.currentMonth = date.toISOString().split('T')[0].substring(0, 7)
      this.loadStatistics()
    },
    nextMonth() {
      const date = new Date(this.currentMonth + '-01')
      date.setMonth(date.getMonth() + 1)
      this.currentMonth = date.toISOString().split('T')[0].substring(0, 7)
      this.loadStatistics()
    },
    loadStatistics() {
      const bookId = uni.getStorageSync('current_book_id')
      const allTransactions = uni.getStorageSync('transactions') || []

      // 筛选当前月份的交易
      const [year, month] = this.currentMonth.split('-')
      const monthTransactions = allTransactions.filter(t => {
        const date = new Date(t.date)
        return t.accountBookId === bookId &&
               date.getFullYear() === parseInt(year) &&
               date.getMonth() + 1 === parseInt(month)
      })

      // 计算总支出和总收入
      const expenses = monthTransactions.filter(t => t.type === 0)
      const incomes = monthTransactions.filter(t => t.type === 1)

      this.totalExpense = expenses.reduce((sum, t) => sum + t.amount, 0)
      this.totalIncome = incomes.reduce((sum, t) => sum + t.amount, 0)

      // 按分类统计支出
      this.expenseByCategory = this.groupByCategory(expenses, this.totalExpense)

      // 按分类统计收入
      this.incomeByCategory = this.groupByCategory(incomes, this.totalIncome)
    },
    groupByCategory(transactions, total) {
      const categoryMap = {}

      transactions.forEach(t => {
        if (!categoryMap[t.category]) {
          categoryMap[t.category] = {
            category: t.category,
            amount: 0,
            count: 0
          }
        }
        categoryMap[t.category].amount += t.amount
        categoryMap[t.category].count++
      })

      const result = Object.values(categoryMap)
        .map(item => ({
          ...item,
          percent: total > 0 ? ((item.amount / total) * 100).toFixed(1) : 0
        }))
        .sort((a, b) => b.amount - a.amount)

      return result
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

.month-selector {
  background: white;
  padding: 30rpx;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 32rpx;
}

.month-text {
  font-size: 36rpx;
  font-weight: bold;
}

.summary-card {
  background: white;
  margin: 20rpx 30rpx;
  padding: 40rpx;
  border-radius: 20rpx;
  display: flex;
  box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.08);
}

.summary-item {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.summary-divider {
  width: 2rpx;
  background-color: #f0f0f0;
}

.summary-label {
  font-size: 28rpx;
  color: #999;
  margin-bottom: 15rpx;
}

.summary-value {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.summary-value.expense {
  color: #ff4757;
}

.summary-value.income {
  color: #2ed573;
}

.category-stats {
  background: white;
  margin: 20rpx 30rpx;
  padding: 30rpx;
  border-radius: 20rpx;
}

.section-title {
  display: flex;
  flex-direction: column;
  margin-bottom: 25rpx;
}

.section-title text:first-child {
  font-size: 34rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 5rpx;
}

.section-subtitle {
  font-size: 24rpx;
  color: #999;
  font-weight: normal;
}

.empty {
  text-align: center;
  padding: 60rpx 0;
  color: #999;
  font-size: 28rpx;
}

.category-list {
  display: flex;
  flex-direction: column;
  gap: 20rpx;
}

.category-item {
  padding: 25rpx;
  background: #f9f9f9;
  border-radius: 15rpx;
}

.category-info {
  display: flex;
  flex-direction: column;
  width: 100%;
}

.category-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15rpx;
}

.category-name {
  font-size: 30rpx;
  color: #333;
  font-weight: 500;
}

.category-amount {
  font-size: 32rpx;
  font-weight: bold;
  color: #ff4757;
}

.category-amount.income {
  color: #2ed573;
}

.category-bar-container {
  width: 100%;
  height: 12rpx;
  background-color: #e8e8e8;
  border-radius: 6rpx;
  overflow: hidden;
  margin-bottom: 12rpx;
}

.category-bar {
  height: 100%;
  background: linear-gradient(90deg, #ff6b6b 0%, #ff4757 100%);
  border-radius: 6rpx;
  transition: width 0.3s ease;
}

.income-bar {
  background: linear-gradient(90deg, #51cf66 0%, #2ed573 100%);
}

.category-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.category-count {
  font-size: 24rpx;
  color: #999;
}

.category-percent {
  font-size: 26rpx;
  color: #666;
  font-weight: 500;
}
</style>
