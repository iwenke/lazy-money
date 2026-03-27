# 懒人记账

一个简单易用的跨平台记账应用，基于 uni-app 开发，支持 Android、iOS、H5、微信小程序和 HarmonyOS。

## 功能特点

- ✅ 多账本管理
- ✅ 收支记录
- ✅ 分类统计
- ✅ 月度报表
- ✅ 数据本地存储
- ✅ 界面简洁美观

## 技术栈

- **框架**: uni-app (Vue 3)
- **存储**: uni.storage (本地存储)
- **UI**: 原生组件 + 自定义样式

## 支持平台

- 📱 Android
- 📱 iOS
- 🌐 H5
- 📱 微信小程序
- 📱 HarmonyOS

## 快速开始

### 环境要求

- Node.js 14+
- HBuilderX 或 CLI 工具

### 安装依赖

```bash
npm install
```

### 开发运行

```bash
# H5
npm run dev:h5

# 微信小程序
npm run dev:mp-weixin

# App (需要在 HBuilderX 中运行)
npm run dev:app
```

### 打包构建

```bash
# H5
npm run build:h5

# 微信小程序
npm run build:mp-weixin

# App
npm run build:app
```

## 使用 HBuilderX 打包 App

1. 使用 HBuilderX 打开项目
2. 点击菜单：运行 → 运行到手机或模拟器 → 运行到 Android/iOS
3. 打包：发行 → 原生 App-云打包

## 项目结构

```
lazy-money/
├── pages/              # 页面
│   ├── index/         # 首页
│   ├── add/           # 记账页
│   ├── statistics/    # 统计页
│   └── books/         # 账本管理
├── static/            # 静态资源
├── App.vue            # 应用入口
├── main.js            # 入口文件
├── manifest.json      # 应用配置
├── pages.json         # 页面配置
└── package.json       # 依赖配置
```

## 数据存储

应用使用 `uni.storage` 进行本地数据存储，主要存储：

- `account_books`: 账本列表
- `current_book_id`: 当前选中的账本 ID
- `transactions`: 所有交易记录

## 功能说明

### 首页
- 显示当前账本余额
- 显示本月收支统计
- 显示最近交易记录
- 快速记账入口

### 记账
- 支出/收入切换
- 金额输入
- 分类选择（餐饮、交通、购物等）
- 备注和日期

### 统计
- 月度收支总览
- 支出分类统计
- 收入分类统计
- 百分比展示

### 账本管理
- 创建多个账本
- 自定义图标和颜色
- 切换账本
- 查看账本余额

## 开发计划

- [ ] 数据导出/导入
- [ ] 预算管理
- [ ] 图表可视化
- [ ] 云端同步
- [ ] 多语言支持

## License

MIT

## 作者

Claude & User
