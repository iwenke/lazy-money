<template>
  <view class="container">
    <!-- 类型切换 -->
    <view class="type-tabs">
      <view
        :class="['tab', type === 0 ? 'active expense' : '']"
        @click="type = 0"
      >
        支出
      </view>
      <view
        :class="['tab', type === 1 ? 'active income' : '']"
        @click="type = 1"
      >
        收入
      </view>
    </view>

    <!-- 金额输入 -->
    <view class="amount-section">
      <text class="currency">¥</text>
      <input
        class="amount-input"
        type="digit"
        v-model="amount"
        placeholder="0.00"
        :focus="true"
      />
    </view>

    <!-- 分类选择 -->
    <view class="category-section">
      <view class="section-title">选择分类</view>
      <view class="category-grid">
        <view
          v-for="cat in currentCategories"
          :key="cat"
          :class="['category-item', category === cat ? 'active' : '']"
          @click="category = cat"
        >
          <text>{{ cat }}</text>
        </view>
      </view>
    </view>

    <!-- 备注 -->
    <view class="note-section">
      <view class="section-title">备注</view>
      <input
        class="note-input"
        v-model="note"
        placeholder="添加备注（可选）"
      />
    </view>

    <!-- 日期 -->
    <view class="date-section">
      <view class="section-title">日期</view>
      <picker mode="date" :value="date" @change="onDateChange">
        <view class="date-picker">
          {{ formatDate(date) }}
        </view>
      </picker>
    </view>

    <!-- 保存按钮 -->
    <view class="save-button" @click="saveTransaction">
      <text>保存</text>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      type: 0, // 0: 支出, 1: 收入
      amount: '',
      category: '',
      note: '',
      date: new Date().toISOString().split('T')[0],
      expenseCategories: ['餐饮', '交通', '购物', '娱乐', '医疗', '教育', '住房', 'AA收款', '红包', '转账', '其他'],
      incomeCategories: ['工资', '奖金', '兼职', '投资', '红包', '转账', '其他']
    }
  },
  computed: {
    currentCategories() {
      return this.type === 0 ? this.expenseCategories : this.incomeCategories
    }
  },
  watch: {
    type() {
      // 切换类型时重置分类
      this.category = ''
    }
  },
  methods: {
    onDateChange(e) {
      this.date = e.detail.value
    },
    formatDate(dateStr) {
      const date = new Date(dateStr)
      return `${date.getFullYear()}年${date.getMonth() + 1}月${date.getDate()}日`
    },
    saveTransaction() {
      if (!this.amount || parseFloat(this.amount) <= 0) {
        uni.showToast({
          title: '请输入金额',
          icon: 'none'
        })
        return
      }

      if (!this.category) {
        uni.showToast({
          title: '请选择分类',
          icon: 'none'
        })
        return
      }

      // 获取当前账本
      const bookId = uni.getStorageSync('current_book_id')
      const books = uni.getStorageSync('account_books') || []
      const bookIndex = books.findIndex(b => b.id === bookId)

      if (bookIndex === -1) {
        uni.showToast({
          title: '账本不存在',
          icon: 'none'
        })
        return
      }

      // 创建交易记录
      const transaction = {
        id: Date.now(),
        accountBookId: bookId,
        type: this.type,
        amount: parseFloat(this.amount),
        category: this.category,
        note: this.note,
        date: this.date,
        createdAt: new Date().toISOString()
      }

      // 保存交易记录
      const transactions = uni.getStorageSync('transactions') || []
      transactions.push(transaction)
      uni.setStorageSync('transactions', transactions)

      // 更新账本余额
      if (this.type === 0) {
        books[bookIndex].balance -= transaction.amount
      } else {
        books[bookIndex].balance += transaction.amount
      }
      uni.setStorageSync('account_books', books)

      uni.showToast({
        title: '保存成功',
        icon: 'success'
      })

      setTimeout(() => {
        uni.navigateBack()
      }, 1000)
    }
  }
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  background-color: #f5f5f5;
}

.type-tabs {
  display: flex;
  background: white;
  padding: 20rpx;
}

.tab {
  flex: 1;
  text-align: center;
  padding: 20rpx;
  font-size: 32rpx;
  color: #999;
  border-radius: 10rpx;
}

.tab.active {
  color: white;
  font-weight: bold;
}

.tab.active.expense {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
}

.tab.active.income {
  background: linear-gradient(135deg, #2ed573 0%, #1dd1a1 100%);
}

.amount-section {
  background: white;
  padding: 60rpx 40rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 20rpx;
}

.currency {
  font-size: 60rpx;
  color: #333;
  margin-right: 20rpx;
}

.amount-input {
  font-size: 80rpx;
  font-weight: bold;
  color: #333;
  text-align: center;
}

.category-section,
.note-section,
.date-section {
  background: white;
  padding: 30rpx;
  margin-top: 20rpx;
}

.section-title {
  font-size: 28rpx;
  color: #999;
  margin-bottom: 20rpx;
}

.category-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20rpx;
}

.category-item {
  padding: 20rpx;
  text-align: center;
  background: #f5f5f5;
  border-radius: 10rpx;
  font-size: 28rpx;
}

.category-item.active {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

.note-input {
  padding: 20rpx;
  background: #f5f5f5;
  border-radius: 10rpx;
  font-size: 28rpx;
}

.date-picker {
  padding: 20rpx;
  background: #f5f5f5;
  border-radius: 10rpx;
  font-size: 28rpx;
}

.save-button {
  margin: 60rpx 30rpx;
  padding: 30rpx;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  text-align: center;
  border-radius: 20rpx;
  font-size: 32rpx;
  font-weight: bold;
}
</style>
