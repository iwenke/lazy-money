# HBuilderX 打包详细教程

## 📥 第一步：下载和安装 HBuilderX

### 1.1 下载 HBuilderX

访问官网：https://www.dcloud.io/hbuilderx.html

**选择版本**：
- ✅ **App 开发版**（推荐）- 包含 App 打包功能
- ❌ 标准版 - 不包含 App 打包

**下载地址**：
- Mac: https://download1.dcloud.net.cn/download/HBuilderX.3.99.2023112510.dmg
- Windows: https://download1.dcloud.net.cn/download/HBuilderX.3.99.2023112510.zip

### 1.2 安装

**Mac**：
1. 打开下载的 `.dmg` 文件
2. 拖动 HBuilderX 到应用程序文件夹
3. 打开 HBuilderX

**Windows**：
1. 解压下载的 `.zip` 文件
2. 双击 `HBuilderX.exe` 运行
3. 无需安装，直接使用

### 1.3 首次启动配置

1. 打开 HBuilderX
2. 可能需要登录 DCloud 账号
3. 如果没有账号，点击"注册"创建一个（免费）

---

## 📂 第二步：导入项目

### 2.1 克隆项目到本地

```bash
# 打开终端（Mac）或命令提示符（Windows）
cd ~/Documents  # 或你想要的目录

# 克隆项目
git clone https://github.com/iwenke/lazy-money.git

# 进入项目目录
cd lazy-money
```

### 2.2 在 HBuilderX 中导入项目

**方法一：拖拽导入**
1. 打开 HBuilderX
2. 直接拖拽 `lazy-money` 文件夹到 HBuilderX 窗口

**方法二：菜单导入**
1. HBuilderX 菜单：**文件** → **导入** → **从本地目录导入**
2. 选择 `lazy-money` 文件夹
3. 点击"选择"

### 2.3 验证项目导入成功

左侧项目列表应该显示：
```
lazy-money
├── pages/
├── static/
├── App.vue
├── main.js
├── manifest.json
├── pages.json
└── ...
```

---

## ⚙️ 第三步：配置应用信息

### 3.1 打开 manifest.json

1. 在项目列表中找到 `manifest.json`
2. 双击打开
3. 会看到可视化配置界面

### 3.2 基础配置

点击左侧 **"应用基本信息"** 标签：

```
应用名称: 懒人记账
应用描述: 简单易用的跨平台记账应用
应用版本名称: 1.0.0
应用版本号: 1
```

### 3.3 App 图标配置（可选）

点击左侧 **"App图标配置"** 标签：

1. 点击"自动生成所有图标并替换"
2. 选择一张 1024x1024 的图片
3. 或使用默认图标

### 3.4 App 启动页配置（可选）

点击左侧 **"App启动页配置"** 标签：

- 可以自定义启动页
- 或使用默认配置

### 3.5 保存配置

点击 **Ctrl+S** (Mac: Cmd+S) 保存

---

## 🔐 第四步：配置签名证书

### 4.1 准备证书文件

证书文件已生成，位置：
```
/Users/wenke/Documents/wk/ai/lazy-money/lazy-money.keystore
```

**如果找不到证书文件**，可以重新生成：

```bash
cd /Users/wenke/Documents/wk/ai/lazy-money

keytool -genkey -alias lazy-money -keyalg RSA -keysize 2048 \
  -validity 36500 -keystore lazy-money.keystore \
  -storepass android123 -keypass android123 \
  -dname "CN=LazyMoney, OU=Dev, O=LazyMoney, L=Beijing, ST=Beijing, C=CN"
```

### 4.2 证书信息

记住以下信息（打包时需要）：

```
证书文件: lazy-money.keystore
证书别名: lazy-money
证书密码: android123
私钥密码: android123
```

---

## 📦 第五步：云打包 APK

### 5.1 开始打包

1. 在 HBuilderX 中，确保 `lazy-money` 项目已选中
2. 点击菜单：**发行** → **原生 App-云打包**

### 5.2 选择平台

在弹出的对话框中：

```
☑️ Android
☐ iOS (需要 Mac 和 Apple 开发者账号)
```

### 5.3 配置打包参数

#### 应用信息
```
应用名称: 懒人记账
应用包名: com.lazy.money
应用版本名称: 1.0.0
应用版本号: 1
```

#### 证书选择

**方式一：使用 DCloud 公用证书（快速测试）**
```
○ 使用 DCloud 公用证书
```

**优点**：
- ✅ 快速
- ✅ 无需配置

**缺点**：
- ❌ 无法发布到应用商店
- ❌ 无法覆盖安装更新

**方式二：使用自有证书（正式发布）**⭐ 推荐
```
● 使用自有证书

证书别名: lazy-money
证书私钥密码: android123
证书文件: [点击选择] → 选择 lazy-money.keystore
```

### 5.4 其他选项

```
☑️ 打正式包
☐ 打测试包
☐ 广告联盟
☐ 统计
```

### 5.5 开始打包

1. 确认所有信息无误
2. 点击 **"打包"** 按钮
3. 等待打包开始

---

## ⏳ 第六步：等待打包完成

### 6.1 打包进度

打包开始后，HBuilderX 底部会显示：

```
正在云端打包...
排队中... (可能需要等待)
正在编译...
正在签名...
```

### 6.2 打包时间

- **首次打包**：通常需要 **5-10 分钟**
- **后续打包**：通常需要 **3-5 分钟**

### 6.3 打包状态

HBuilderX 控制台会显示：
```
[INFO] 开始云端打包...
[INFO] 正在上传项目...
[INFO] 上传完成，开始编译...
[INFO] 编译中...
[INFO] 打包成功！
```

### 6.4 打包完成

打包成功后会弹出提示框：
```
打包成功！
[下载] [查看详情]
```

---

## 📥 第七步：下载 APK

### 7.1 下载方式

**方式一：直接下载**
1. 点击提示框中的 **"下载"** 按钮
2. 选择保存位置
3. 等待下载完成

**方式二：从控制台下载**
1. 在 HBuilderX 控制台中找到下载链接
2. 点击链接或复制到浏览器
3. 下载 APK 文件

**方式三：扫码下载**
1. 打包成功后会显示二维码
2. 用手机扫码
3. 直接下载到手机

### 7.2 APK 文件位置

下载后的 APK 通常在：
- Mac: `~/Downloads/`
- Windows: `C:\Users\你的用户名\Downloads\`

文件名类似：
```
__UNI__lazy_money_20260327.apk
```

---

## 📱 第八步：安装到手机

### 8.1 传输 APK 到手机

**方法一：数据线传输**
```bash
# 如果安装了 adb
adb install ~/Downloads/__UNI__lazy_money_*.apk
```

**方法二：微信/QQ 传输**
1. 通过微信或 QQ 发送 APK 到手机
2. 在手机上下载文件

**方法三：扫码下载**
- 直接扫打包完成后的二维码

**方法四：云盘传输**
- 上传到网盘
- 手机下载

### 8.2 安装 APK

1. 在手机上找到 APK 文件
2. 点击安装
3. 如果提示"未知来源"：
   - 进入设置 → 安全
   - 允许"安装未知来源应用"
4. 安装完成

### 8.3 测试应用

1. 打开"懒人记账"应用
2. 测试各项功能：
   - ✅ 创建账本
   - ✅ 添加记录
   - ✅ 查看统计
   - ✅ 切换账本

---

## 🔄 第九步：更新应用

### 9.1 修改版本号

每次更新时，需要修改版本号：

1. 打开 `manifest.json`
2. 修改：
   ```
   应用版本名称: 1.0.1 (从 1.0.0 改为 1.0.1)
   应用版本号: 2 (从 1 改为 2)
   ```

### 9.2 重新打包

1. 按照上面的步骤重新打包
2. **重要**：必须使用**相同的证书**
3. 否则无法覆盖安装

### 9.3 覆盖安装

如果使用相同证书：
- ✅ 可以直接覆盖安装
- ✅ 数据不会丢失

如果证书不同：
- ❌ 需要先卸载旧版本
- ❌ 数据会丢失

---

## ❗ 常见问题

### Q1: 打包失败怎么办？

**A: 检查以下几点**
1. 网络连接是否正常
2. DCloud 账号是否登录
3. manifest.json 配置是否正确
4. 查看控制台的错误信息

### Q2: 证书密码忘记了怎么办？

**A: 重新生成证书**
```bash
keytool -genkey -alias lazy-money-new -keyalg RSA -keysize 2048 \
  -validity 36500 -keystore lazy-money-new.keystore
```
注意：新证书无法覆盖安装旧版本

### Q3: 打包要收费吗？

**A: 免费**
- DCloud 云打包服务免费
- 无需付费

### Q4: 安装时提示"解析包时出现问题"？

**A: 可能原因**
1. APK 下载不完整 - 重新下载
2. 手机系统版本过低 - 需要 Android 5.0+
3. APK 损坏 - 重新打包

### Q5: 提示签名不一致？

**A: 证书不同**
- 必须使用相同的证书打包
- 或先卸载旧版本

### Q6: 如何发布到应用商店？

**A: 使用自有证书打包**
1. 必须使用自有证书（不能用公用证书）
2. 注册应用商店开发者账号
3. 上传 APK
4. 填写应用信息
5. 提交审核

常见应用商店：
- 华为应用市场（AppGallery）
- 小米应用商店
- OPPO 软件商店
- vivo 应用商店
- 应用宝（腾讯）

---

## 📋 打包检查清单

打包前检查：
- [ ] 项目代码无错误
- [ ] manifest.json 配置正确
- [ ] 版本号已更新（如果是更新版本）
- [ ] 证书文件准备好
- [ ] 证书密码记住了
- [ ] HBuilderX 已登录

打包后检查：
- [ ] APK 下载成功
- [ ] 文件大小正常（通常 10-30 MB）
- [ ] 安装测试通过
- [ ] 功能测试通过

---

## 🎯 快速参考

### 证书信息
```
文件: lazy-money.keystore
别名: lazy-money
密码: android123
```

### 应用信息
```
名称: 懒人记账
包名: com.lazy.money
版本: 1.0.0
```

### 关键步骤
```
1. 导入项目到 HBuilderX
2. 配置 manifest.json
3. 发行 → 原生 App-云打包
4. 选择 Android + 自有证书
5. 等待 5-10 分钟
6. 下载 APK
7. 安装测试
```

---

## 📞 获取帮助

**官方资源**：
- HBuilderX 文档: https://hx.dcloud.net.cn/
- uni-app 文档: https://uniapp.dcloud.net.cn/
- DCloud 社区: https://ask.dcloud.net.cn/

**视频教程**：
- B站搜索: "HBuilderX 打包教程"
- YouTube: "uni-app build apk"

---

**准备好了吗？开始打包吧！** 🚀
