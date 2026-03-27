# 懒人记账 - 使用说明

## 如何在 HBuilderX 中运行和打包

### 1. 安装 HBuilderX

下载地址：https://www.dcloud.io/hbuilderx.html

推荐下载"App 开发版"

### 2. 导入项目

1. 打开 HBuilderX
2. 文件 → 导入 → 从本地目录导入
3. 选择 `lazy-money` 文件夹

### 3. 运行到手机（开发调试）

#### Android

1. 手机开启开发者模式和 USB 调试
2. 用数据线连接电脑
3. HBuilderX 菜单：运行 → 运行到手机或模拟器 → 运行到 Android App 基座

#### iOS

1. 需要 Mac 电脑和 Xcode
2. HBuilderX 菜单：运行 → 运行到手机或模拟器 → 运行到 iOS 模拟器

### 4. 云打包（正式发布）

#### Android APK

1. HBuilderX 菜单：发行 → 原生 App-云打包
2. 选择"Android"
3. 填写应用信息：
   - 应用名称：懒人记账
   - 应用包名：com.lazy.money
   - 应用版本名称：1.0.0
   - 应用版本号：1
4. 选择"使用 DCloud 公用证书"（测试用）或"使用自有证书"（正式发布）
5. 点击"打包"
6. 等待 5-10 分钟
7. 打包完成后下载 APK

#### iOS IPA

1. 需要 Apple 开发者账号（$99/年）
2. HBuilderX 菜单：发行 → 原生 App-云打包
3. 选择"iOS"
4. 填写应用信息和证书
5. 打包完成后下载 IPA

### 5. HarmonyOS 支持

uni-app 对 HarmonyOS 的支持：

1. HBuilderX 3.6.9+ 版本开始支持 HarmonyOS
2. 打包时选择"Android"即可，HarmonyOS 会自动兼容
3. 或使用华为 DevEco Studio 进一步优化

### 6. 本地打包（高级）

如果需要本地打包（不使用云打包）：

1. 安装 Android Studio 或 Xcode
2. HBuilderX 菜单：发行 → 原生 App-本地打包
3. 生成本地打包资源
4. 在 Android Studio/Xcode 中打开并编译

## 常见问题

### Q: 云打包失败怎么办？

A:
1. 检查网络连接
2. 检查 manifest.json 配置是否正确
3. 查看 HBuilderX 控制台的错误信息
4. 尝试使用 DCloud 公用证书

### Q: 如何生成自己的签名证书？

A:
```bash
# Android
keytool -genkey -alias lazy-money -keyalg RSA -keysize 2048 -validity 36500 -keystore lazy-money.keystore
```

### Q: 如何在微信小程序中运行？

A:
1. HBuilderX 菜单：运行 → 运行到小程序模拟器 → 微信开发者工具
2. 需要先安装微信开发者工具
3. 在微信开发者工具中预览和调试

### Q: 如何发布到 H5？

A:
```bash
npm run build:h5
```
生成的文件在 `unpackage/dist/build/h5` 目录，上传到服务器即可。

### Q: 数据会丢失吗？

A:
数据存储在本地，卸载应用会清空数据。未来版本会支持数据导出和云端备份。

## 技术支持

如有问题，请访问：
- uni-app 官方文档：https://uniapp.dcloud.net.cn/
- DCloud 社区：https://ask.dcloud.net.cn/

## 更新日志

### v1.0.0 (2026-03-27)
- ✅ 初始版本发布
- ✅ 支持多账本管理
- ✅ 支持收支记录
- ✅ 支持分类统计
- ✅ 支持跨平台运行
