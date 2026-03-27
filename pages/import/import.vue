<template>
  <view class="container">
    <!-- 导入说明 -->
    <view class="intro-card">
      <text class="intro-title">💡 如何导入账单</text>
      <view class="intro-steps">
        <text class="intro-step">1. 微信 → 我 → 服务 → 钱包 → 账单</text>
        <text class="intro-step">2. 点击右上角"常见问题" → 下载账单</text>
        <text class="intro-step">3. 选择时间范围，下载CSV文件</text>
        <text class="intro-step">4. 返回本页面，选择下载的文件导入</text>
      </view>
    </view>

    <!-- 导入选项 -->
    <view class="import-section">
      <view class="section-title">选择导入方式</view>

      <view class="import-card" @click="importWechat">
        <view class="import-icon">💬</view>
        <view class="import-info">
          <text class="import-name">微信账单</text>
          <text class="import-desc">导入微信支付记录</text>
        </view>
        <text class="import-arrow">›</text>
      </view>

      <view class="import-card" @click="importAlipay">
        <view class="import-icon">💰</view>
        <view class="import-info">
          <text class="import-name">支付宝账单</text>
          <text class="import-desc">导入支付宝交易记录</text>
        </view>
        <text class="import-arrow">›</text>
      </view>
    </view>

    <!-- 导入历史 -->
    <view class="history-section" v-if="importHistory.length > 0">
      <view class="section-title">导入历史</view>
      <view class="history-list">
        <view v-for="item in importHistory" :key="item.id" class="history-item">
          <view class="history-left">
            <text class="history-type">{{ item.type }}</text>
            <text class="history-date">{{ formatDate(item.date) }}</text>
          </view>
          <view class="history-right">
            <text class="history-count">{{ item.count }}条记录</text>
            <text class="history-amount">¥{{ item.amount.toFixed(2) }}</text>
          </view>
        </view>
      </view>
    </view>

    <!-- 预览弹窗 -->
    <view v-if="showPreview" class="preview-modal" @click="closePreview">
      <view class="preview-content" @click.stop>
        <view class="preview-header">
          <text class="preview-title">确认导入</text>
          <text class="preview-close" @click="closePreview">✕</text>
        </view>

        <view class="preview-summary">
          <text class="summary-text">共 {{ previewRecords.length }} 条记录</text>
          <text class="summary-amount">总金额 ¥{{ totalAmount.toFixed(2) }}</text>
        </view>

        <view class="preview-list">
          <view v-for="(record, index) in previewRecords.slice(0, 5)" :key="index" class="preview-item">
            <view class="preview-item-left">
              <text class="preview-category">{{ record.category }}</text>
              <text class="preview-merchant">{{ record.merchant }}</text>
              <text class="preview-time">{{ record.date }}</text>
            </view>
            <text :class="['preview-amount', record.type === 0 ? 'expense' : 'income']">
              {{ record.type === 0 ? '-' : '+' }}¥{{ record.amount.toFixed(2) }}
            </text>
          </view>
          <text v-if="previewRecords.length > 5" class="preview-more">
            还有 {{ previewRecords.length - 5 }} 条记录...
          </text>
        </view>

        <view class="preview-actions">
          <button class="btn-cancel" @click="closePreview">取消</button>
          <button class="btn-confirm" @click="confirmImport">确认导入</button>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      importHistory: [],
      showPreview: false,
      previewRecords: [],
      currentFile: null,
      currentType: ''
    }
  },
  computed: {
    totalAmount() {
      return this.previewRecords.reduce((sum, record) => {
        return sum + (record.type === 0 ? record.amount : -record.amount)
      }, 0)
    }
  },
  onShow() {
    this.loadHistory()
  },
  methods: {
    loadHistory() {
      this.importHistory = uni.getStorageSync('import_history') || []
    },
    importWechat() {
      this.currentType = '微信账单'
      this.chooseFile()
    },
    importAlipay() {
      this.currentType = '支付宝账单'
      uni.showToast({
        title: '支付宝账单功能开发中',
        icon: 'none'
      })
    },
    chooseFile() {
      // uni-app 文件选择
      uni.chooseFile({
        count: 1,
        extension: ['.csv'],
        success: (res) => {
          const tempFilePath = res.tempFilePaths[0]
          this.currentFile = tempFilePath
          this.readFile(tempFilePath)
        },
        fail: (err) => {
          console.log('文件选择失败', err)
          // H5环境使用input file
          this.chooseFileH5()
        }
      })
    },
    chooseFileH5() {
      // 创建文件选择输入
      const input = document.createElement('input')
      input.type = 'file'
      input.accept = '.csv'
      input.onchange = (e) => {
        const file = e.target.files[0]
        if (file) {
          this.readFileH5(file)
        }
      }
      input.click()
    },
    readFileH5(file) {
      const reader = new FileReader()
      reader.onload = (e) => {
        const content = e.target.result
        this.parseCSV(content)
      }
      reader.readAsText(file, 'utf-8')
    },
    readFile(filePath) {
      uni.getFileSystemManager().readFile({
        filePath: filePath,
        encoding: 'utf-8',
        success: (res) => {
          this.parseCSV(res.data)
        },
        fail: (err) => {
          uni.showToast({
            title: '文件读取失败',
            icon: 'none'
          })
        }
      })
    },
    parseCSV(content) {
      try {
        const records = this.parseWechatCSV(content)
        if (records.length === 0) {
          uni.showToast({
            title: '未找到有效记录',
            icon: 'none'
          })
          return
        }
        this.previewRecords = records
        this.showPreview = true
      } catch (err) {
        console.error('CSV解析失败', err)
        uni.showToast({
          title: 'CSV格式错误',
          icon: 'none'
        })
      }
    },
    parseWechatCSV(content) {
      const lines = content.split('\n')
      const records = []

      // 跳过标题行，从第二行开始
      for (let i = 1; i < lines.length; i++) {
        const line = lines[i].trim()
        if (!line) continue

        // 解析CSV行
        const fields = this.parseCSVLine(line)
        if (fields.length < 6) continue

        // 微信账单格式：交易时间,交易类型,交易对方,商品,收/支,金额(元),支付方式,当前状态...
        const dateTime = fields[0]
        const transType = fields[1]
        const merchant = fields[2]
        const product = fields[3]
        const inOut = fields[4]
        const amountStr = fields[5]

        // 只导入支付成功的记录
        if (fields[7] && fields[7].indexOf('成功') === -1) continue

        // 跳过转账、红包等
        if (transType.indexOf('转账') >= 0 || transType.indexOf('红包') >= 0) continue

        const amount = parseFloat(amountStr.replace('¥', ''))
        if (isNaN(amount) || amount <= 0) continue

        const type = inOut.indexOf('支出') >= 0 ? 0 : 1
        const category = this.matchCategory(merchant, product, type)

        records.push({
          date: dateTime,
          type: type,
          category: category,
          merchant: merchant || product,
          amount: amount,
          note: product || ''
        })
      }

      return records
    },
    parseCSVLine(line) {
      const fields = []
      let field = ''
      let inQuotes = false

      for (let i = 0; i < line.length; i++) {
        const char = line[i]

        if (char === '"') {
          inQuotes = !inQuotes
        } else if (char === ',' && !inQuotes) {
          fields.push(field.trim())
          field = ''
        } else {
          field += char
        }
      }
      fields.push(field.trim())

      return fields
    },
    matchCategory(merchant, product, type) {
      const text = (merchant + product).toLowerCase()

      if (type === 0) {
        // 支出分类
        if (text.match(/美团|饿了么|外卖|餐饮|食堂|麦当劳|肯德基|星巴克|咖啡|奶茶/)) return '餐饮'
        if (text.match(/淘宝|京东|拼多多|商场|超市|便利店|购物|盒马/)) return '购物'
        if (text.match(/滴滴|打车|出租|地铁|公交|加油|停车|中国石化|中国石油/)) return '交通'
        if (text.match(/电影|KTV|游戏|娱乐|健身|运动/)) return '娱乐'
        if (text.match(/医院|药店|医疗|体检/)) return '医疗'
        if (text.match(/房租|物业|水电|燃气/)) return '住房'
        if (text.match(/培训|课程|书店|教育/)) return '教育'
        return '其他'
      } else {
        // 收入分类
        if (text.match(/工资|薪资|报酬/)) return '工资'
        if (text.match(/奖金|提成|年终奖/)) return '奖金'
        if (text.match(/兼职|外快/)) return '兼职'
        if (text.match(/理财|利息|分红|投资/)) return '理财'
        return '其他'
      }
    },
    closePreview() {
      this.showPreview = false
    },
    confirmImport() {
      uni.showLoading({ title: '导入中...' })

      const bookId = uni.getStorageSync('current_book_id') || 0
      const allTransactions = uni.getStorageSync('transactions') || []

      // 添加记录
      this.previewRecords.forEach(record => {
        allTransactions.push({
          id: Date.now() + Math.random(),
          accountBookId: bookId,
          type: record.type,
          category: record.category,
          amount: record.amount,
          date: record.date,
          note: record.note || record.merchant
        })
      })

      uni.setStorageSync('transactions', allTransactions)

      // 更新账本余额
      this.updateBookBalance(bookId)

      // 保存导入历史
      this.saveImportHistory()

      uni.hideLoading()

      uni.showToast({
        title: `成功导入${this.previewRecords.length}条记录`,
        icon: 'success'
      })

      this.showPreview = false
      this.loadHistory()

      // 延迟跳转到首页
      setTimeout(() => {
        uni.switchTab({
          url: '/pages/index/index'
        })
      }, 1500)
    },
    updateBookBalance(bookId) {
      const books = uni.getStorageSync('account_books') || []
      const book = books.find(b => b.id === bookId)
      if (book) {
        const allTransactions = uni.getStorageSync('transactions') || []
        const bookTransactions = allTransactions.filter(t => t.accountBookId === bookId)

        const income = bookTransactions.filter(t => t.type === 1).reduce((sum, t) => sum + t.amount, 0)
        const expense = bookTransactions.filter(t => t.type === 0).reduce((sum, t) => sum + t.amount, 0)

        book.balance = income - expense
        uni.setStorageSync('account_books', books)
      }
    },
    saveImportHistory() {
      const history = {
        id: Date.now(),
        type: this.currentType,
        date: new Date().toISOString(),
        count: this.previewRecords.length,
        amount: Math.abs(this.totalAmount)
      }

      this.importHistory.unshift(history)
      if (this.importHistory.length > 10) {
        this.importHistory = this.importHistory.slice(0, 10)
      }

      uni.setStorageSync('import_history', this.importHistory)
    },
    formatDate(dateStr) {
      const date = new Date(dateStr)
      return `${date.getMonth() + 1}月${date.getDate()}日 ${date.getHours()}:${String(date.getMinutes()).padStart(2, '0')}`
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

.intro-card {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  margin: 30rpx;
  padding: 40rpx;
  border-radius: 20rpx;
  color: white;
}

.intro-title {
  font-size: 36rpx;
  font-weight: bold;
  margin-bottom: 20rpx;
  display: block;
}

.intro-steps {
  display: flex;
  flex-direction: column;
  gap: 12rpx;
}

.intro-step {
  font-size: 26rpx;
  opacity: 0.9;
  line-height: 40rpx;
}

.import-section {
  padding: 0 30rpx;
  margin-top: 30rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 20rpx;
}

.import-card {
  background: white;
  border-radius: 20rpx;
  padding: 30rpx;
  margin-bottom: 20rpx;
  display: flex;
  align-items: center;
  box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.08);
}

.import-icon {
  font-size: 60rpx;
  margin-right: 25rpx;
}

.import-info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.import-name {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 8rpx;
}

.import-desc {
  font-size: 26rpx;
  color: #999;
}

.import-arrow {
  font-size: 48rpx;
  color: #ccc;
  font-weight: 300;
}

.history-section {
  padding: 0 30rpx;
  margin-top: 40rpx;
}

.history-list {
  background: white;
  border-radius: 20rpx;
  overflow: hidden;
}

.history-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 30rpx;
  border-bottom: 1rpx solid #f5f5f5;
}

.history-item:last-child {
  border-bottom: none;
}

.history-left {
  display: flex;
  flex-direction: column;
}

.history-type {
  font-size: 30rpx;
  color: #333;
  margin-bottom: 8rpx;
}

.history-date {
  font-size: 24rpx;
  color: #999;
}

.history-right {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.history-count {
  font-size: 26rpx;
  color: #666;
  margin-bottom: 8rpx;
}

.history-amount {
  font-size: 30rpx;
  font-weight: bold;
  color: #667eea;
}

.preview-modal {
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

.preview-content {
  background: white;
  width: 90%;
  max-height: 80vh;
  border-radius: 20rpx;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.preview-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  border-bottom: 1rpx solid #f5f5f5;
}

.preview-title {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.preview-close {
  font-size: 40rpx;
  color: #999;
}

.preview-summary {
  padding: 30rpx;
  background: #f9f9f9;
  display: flex;
  justify-content: space-between;
}

.summary-text {
  font-size: 28rpx;
  color: #666;
}

.summary-amount {
  font-size: 28rpx;
  font-weight: bold;
  color: #667eea;
}

.preview-list {
  flex: 1;
  overflow-y: auto;
  padding: 20rpx 30rpx;
}

.preview-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20rpx 0;
  border-bottom: 1rpx solid #f5f5f5;
}

.preview-item:last-child {
  border-bottom: none;
}

.preview-item-left {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.preview-category {
  font-size: 30rpx;
  color: #333;
  margin-bottom: 6rpx;
}

.preview-merchant {
  font-size: 24rpx;
  color: #999;
  margin-bottom: 6rpx;
}

.preview-time {
  font-size: 22rpx;
  color: #ccc;
}

.preview-amount {
  font-size: 32rpx;
  font-weight: bold;
}

.preview-amount.expense {
  color: #ff4757;
}

.preview-amount.income {
  color: #2ed573;
}

.preview-more {
  text-align: center;
  padding: 20rpx;
  font-size: 26rpx;
  color: #999;
}

.preview-actions {
  display: flex;
  padding: 30rpx;
  gap: 20rpx;
  border-top: 1rpx solid #f5f5f5;
}

.btn-cancel,
.btn-confirm {
  flex: 1;
  height: 80rpx;
  line-height: 80rpx;
  text-align: center;
  border-radius: 40rpx;
  font-size: 30rpx;
}

.btn-cancel {
  background: #f5f5f5;
  color: #666;
}

.btn-confirm {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}
</style>
