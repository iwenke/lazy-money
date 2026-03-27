# 懒人记账 - 快速开始

## 🚀 5分钟快速打包 APK

### 准备工作

1. **下载 HBuilderX**
   - 访问: https://www.dcloud.io/hbuilderx.html
   - 下载 "App 开发版"
   - 安装并打开

2. **导入项目**
   - 文件 → 导入 → 从本地目录导入
   - 选择: `/Users/wenke/Documents/wk/ai/lazy-money`

### 打包 APK（测试版）

**最快方式 - 使用公用证书**：

1. 菜单：**发行** → **原生 App-云打包**
2. 勾选：**Android**
3. 填写信息：
   ```
   应用名称: 懒人记账
   应用包名: com.lazy.money
   应用版本: 1.0.0
   版本号: 1
   ```
4. 证书：选择 **使用 DCloud 公用证书**
5. 点击 **打包**
6. 等待 5-10 分钟
7. 下载 APK 并安装到手机

**注意**: 公用证书仅用于测试，无法发布到应用商店。

### 打包 APK（正式版）

**使用自有证书**：

1. 菜单：**发行** → **原生 App-云打包**
2. 勾选：**Android**
3. 填写信息（同上）
4. 证书：选择 **使用自有证书**
5. 填写证书信息：
   ```
   证书别名: lazy-money
   证书私钥密码: android123
   证书文件: 选择 lazy-money.keystore
   ```
6. 点击 **打包**
7. 等待完成，下载 APK

## 📱 安装到手机

### 方法一：数据线传输
```bash
# 如果安装了 adb
adb install path/to/your-app.apk
```

### 方法二：扫码下载
1. 打包完成后，HBuilderX 会显示二维码
2. 手机扫码直接下载安装

### 方法三：文件传输
1. 通过微信/QQ 发送 APK 到手机
2. 手机上打开文件管理器
3. 找到 APK 文件并安装

## 🔧 常见问题

### Q: 打包失败怎么办？
A:
1. 检查网络连接
2. 查看 HBuilderX 控制台错误信息
3. 确认 manifest.json 配置正确
4. 尝试重新打包

### Q: 安装时提示"解析包时出现问题"？
A:
1. 确认手机系统版本（需要 Android 5.0+）
2. 检查 APK 是否下载完整
3. 尝试重新下载

### Q: 手机提示"未知来源"无法安装？
A:
1. 打开手机"设置"
2. 找到"安全"或"应用管理"
3. 允许"安装未知来源应用"

### Q: 更新时提示签名不一致？
A:
- 必须使用相同的证书打包
- 或先卸载旧版本再安装新版本

## 📦 证书信息

已为你生成好签名证书：

```
证书文件: lazy-money.keystore
证书别名: lazy-money
密钥库密码: android123
密钥密码: android123
```

**重要**:
- ✅ 证书文件在项目根目录
- ✅ 打包时需要选择这个文件
- ✅ 请妥善保管，丢失后无法更新应用

## 🌐 其他平台

### 微信小程序
```bash
npm install
npm run build:mp-weixin
```
在微信开发者工具中打开 `unpackage/dist/build/mp-weixin`

### H5 网页版
```bash
npm install
npm run build:h5
```
生成的文件在 `unpackage/dist/build/h5`，上传到服务器即可

### iOS
需要 Mac 电脑和 Apple 开发者账号（$99/年）

## 📚 详细文档

- **BUILD.md** - 完整打包配置说明
- **SECRETS.md** - 证书密钥详细信息（不提交到 Git）
- **USAGE.md** - 详细使用教程
- **README.md** - 项目介绍

## 🎯 下一步

1. ✅ 打包测试版 APK
2. ✅ 安装到手机测试
3. ✅ 确认功能正常
4. ✅ 使用自有证书打包正式版
5. ✅ 发布到应用商店

## 💡 提示

- 第一次打包可能需要较长时间
- 建议先用公用证书快速测试
- 确认无误后再用自有证书打正式包
- 记得保存好证书文件和密码

## 📞 获取帮助

- uni-app 文档: https://uniapp.dcloud.net.cn/
- DCloud 社区: https://ask.dcloud.net.cn/
- HBuilderX 帮助: https://hx.dcloud.net.cn/

---

**祝你打包顺利！** 🎉
