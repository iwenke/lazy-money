<script>
export default {
  onLaunch: function() {
    console.log('App Launch')
    // 初始化数据库
    this.initDatabase()
  },
  onShow: function() {
    console.log('App Show')
  },
  onHide: function() {
    console.log('App Hide')
  },
  methods: {
    initDatabase() {
      // 初始化本地存储
      const books = uni.getStorageSync('account_books')
      if (!books) {
        // 创建默认账本
        const defaultBook = {
          id: Date.now(),
          name: '默认账本',
          icon: '💰',
          color: '#4CAF50',
          balance: 0,
          createdAt: new Date().toISOString()
        }
        uni.setStorageSync('account_books', [defaultBook])
        uni.setStorageSync('current_book_id', defaultBook.id)
      }

      const transactions = uni.getStorageSync('transactions')
      if (!transactions) {
        uni.setStorageSync('transactions', [])
      }
    }
  }
}
</script>

<style>
/*每个页面公共css */
page {
  background-color: #f5f5f5;
}
</style>
