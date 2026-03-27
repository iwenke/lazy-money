# 懒人记账 - 打包配置说明

## 📦 Android 签名证书信息

### 证书文件
- **文件名**: `lazy-money.keystore`
- **位置**: `/Users/wenke/Documents/wk/ai/lazy-money/lazy-money.keystore`
- **大小**: 2.2 KB

### 证书参数
```
别名 (keyAlias): lazy-money
密钥库密码 (storePassword): android123
密钥密码 (keyPassword): android123
有效期: 100年 (36500天)
算法: RSA 2048位
```

### 证书信息
```
CN=LazyMoney
OU=Dev
O=LazyMoney
L=Beijing
ST=Beijing
C=CN
```

## 🔐 Base64 编码的证书

证书的 Base64 编码已保存在：
- **文件**: `lazy-money.keystore.base64`
- **大小**: 2.9 KB

用于 CI/CD 或云打包时使用。

## 📱 HBuilderX 云打包配置

### 方法一：使用 DCloud 公用证书（快速测试）

1. 打开 HBuilderX
2. 菜单：发行 → 原生 App-云打包
3. 选择 **Android**
4. 应用信息：
   - **应用名称**: 懒人记账
   - **应用包名**: com.lazy.money
   - **应用版本名称**: 1.0.0
   - **应用版本号**: 1
5. 证书选择：**使用 DCloud 公用证书**
6. 点击 **打包**

**注意**: 公用证书打包的 APK 无法发布到应用商店，仅用于测试。

### 方法二：使用自有证书（正式发布）

1. 打开 HBuilderX
2. 菜单：发行 → 原生 App-云打包
3. 选择 **Android**
4. 应用信息同上
5. 证书选择：**使用自有证书**
6. 填写证书信息：
   ```
   证书别名: lazy-money
   证书私钥密码: android123
   证书文件: 选择 lazy-money.keystore 文件
   ```
7. 点击 **打包**

## 🔧 本地打包配置

如果使用 HBuilderX 本地打包，需要配置 `manifest.json`：

```json
{
  "app-plus": {
    "distribute": {
      "android": {
        "packagename": "com.lazy.money",
        "keystore": "/path/to/lazy-money.keystore",
        "password": "android123",
        "aliasname": "lazy-money",
        "keypassword": "android123"
      }
    }
  }
}
```

## 🌐 其他平台打包

### iOS 打包

需要：
1. Apple 开发者账号 ($99/年)
2. 在 Apple Developer 创建 App ID
3. 创建证书和描述文件
4. 在 HBuilderX 中配置

### H5 发布

```bash
npm run build:h5
```

生成的文件在 `unpackage/dist/build/h5/`，上传到服务器即可。

### 微信小程序

```bash
npm run build:mp-weixin
```

生成的文件在 `unpackage/dist/build/mp-weixin/`，使用微信开发者工具上传。

## 🔒 证书安全

**重要提示**：
- ✅ `lazy-money.keystore` 已添加到 `.gitignore`，不会提交到 Git
- ✅ 证书密码不要公开分享
- ✅ 正式发布时建议使用更复杂的密码
- ✅ 备份好证书文件，丢失后无法更新应用

## 📋 签名验证

打包完成后，验证 APK 签名：

```bash
# 查看 APK 签名信息
jarsigner -verify -verbose -certs your-app.apk

# 或使用 apksigner
apksigner verify --print-certs your-app.apk
```

## 🚀 自动化打包（GitHub Actions）

如果需要 CI/CD 自动打包，可以使用 HBuilderX CLI：

```yaml
name: Build APK

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'
      - name: Install dependencies
        run: npm install
      - name: Build
        run: npm run build:app
```

**注意**: uni-app 的原生打包通常需要 HBuilderX 或 DCloud 云打包服务。

## 📞 技术支持

如有问题：
- uni-app 官方文档: https://uniapp.dcloud.net.cn/
- DCloud 社区: https://ask.dcloud.net.cn/
- HBuilderX 帮助文档: https://hx.dcloud.net.cn/

## 📝 版本记录

- **v1.0.0** (2026-03-27)
  - 初始版本
  - 生成 Android 签名证书
  - 配置打包参数
