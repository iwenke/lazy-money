# GitHub Actions 自动构建配置

## 📋 概述

本项目配置了 GitHub Actions 自动构建，但需要注意：

**⚠️ uni-app 限制**：
- uni-app 的原生 APP 打包依赖 HBuilderX 或 DCloud 云打包服务
- GitHub Actions 无法直接构建原生 APK/IPA
- 只能构建 H5 版本

## 🌐 H5 自动构建（已配置）

每次推送到 main 分支时，会自动构建 H5 版本。

### 查看构建结果

1. 访问：https://github.com/iwenke/lazy-money/actions
2. 点击最新的 "Build H5" workflow
3. 下载 "h5-dist" artifact
4. 解压后部署到服务器

### 自动部署到 GitHub Pages（可选）

如果想启用自动部署：

1. 仓库设置 → Pages → Source
2. 选择 "GitHub Actions"
3. 推送代码后自动部署
4. 访问：https://iwenke.github.io/lazy-money/

## 📱 Android APK 打包方案

由于 uni-app 的限制，推荐以下方案：

### 方案一：HBuilderX 云打包（推荐）⭐

**最简单、最稳定的方案**

1. 克隆仓库到本地：
   ```bash
   git clone https://github.com/iwenke/lazy-money.git
   ```

2. 使用 HBuilderX 打开项目

3. 菜单：发行 → 原生 App-云打包

4. 配置证书信息：
   ```
   证书别名: lazy-money
   密码: android123
   证书文件: lazy-money.keystore
   ```

5. 等待 5-10 分钟，下载 APK

### 方案二：本地打包后上传

1. 本地使用 HBuilderX 打包
2. 创建 GitHub Release
3. 上传 APK 文件

```bash
# 使用 GitHub CLI
gh release create v1.0.0 path/to/your-app.apk
```

### 方案三：DCloud 开放平台 API（企业）

如果有 DCloud 企业账号，可以使用云打包 API：

```yaml
- name: DCloud Cloud Build
  run: |
    curl -X POST https://ide.dcloud.net.cn/build/app \
      -H "Authorization: Bearer ${{ secrets.DCLOUD_TOKEN }}" \
      -d @build-config.json
```

**注意**：需要企业账号和 API Token。

## 🔐 GitHub Secrets 配置

虽然无法在 GitHub Actions 中直接打包 APK，但可以配置 Secrets 供其他用途：

### 必需的 Secrets

访问：https://github.com/iwenke/lazy-money/settings/secrets/actions

添加以下 Secrets：

1. **KEYSTORE_BASE64**
   ```bash
   # 在本地执行，获取 Base64 编码
   base64 -i lazy-money.keystore
   ```
   复制输出内容，粘贴到 GitHub Secret

2. **KEYSTORE_PASSWORD**
   ```
   android123
   ```

3. **KEY_PASSWORD**
   ```
   android123
   ```

4. **KEY_ALIAS**
   ```
   lazy-money
   ```

### 可选的 Secrets

5. **DCLOUD_TOKEN** (如果使用 DCloud API)
   - 从 DCloud 开放平台获取

## 📦 推荐的发布流程

### 开发流程

```
1. 本地开发
   ↓
2. 提交代码到 GitHub
   ↓
3. GitHub Actions 自动构建 H5
   ↓
4. 本地使用 HBuilderX 打包 APK
   ↓
5. 创建 GitHub Release 并上传 APK
```

### 自动化脚本

创建 Release 并上传 APK：

```bash
#!/bin/bash
# release.sh

VERSION="v1.0.0"
APK_PATH="path/to/lazy-money-release.apk"

# 创建 Release
gh release create $VERSION $APK_PATH \
  --title "懒人记账 $VERSION" \
  --notes "
## 更新内容
- 修复了一些 bug
- 优化了性能

## 下载
- Android APK: lazy-money-release.apk

## 安装说明
1. 下载 APK 文件
2. 允许安装未知来源应用
3. 安装并打开
"
```

## 🔄 持续集成建议

### 可以自动化的部分

✅ **H5 构建和部署**
- 自动构建 H5 版本
- 自动部署到 GitHub Pages
- 自动运行测试

✅ **代码质量检查**
- ESLint 检查
- 代码格式化
- 单元测试

✅ **版本管理**
- 自动生成 Changelog
- 自动打 Tag

### 无法自动化的部分

❌ **原生 APP 打包**
- 需要手动使用 HBuilderX
- 或使用 DCloud 云打包服务

## 📝 工作流说明

### 1. build-h5.yml
- **触发**: 推送到 main 分支
- **功能**: 构建 H5 版本
- **输出**: h5-dist artifact
- **可选**: 部署到 GitHub Pages

### 2. cloud-build.yml
- **触发**: 手动触发
- **功能**: 显示云打包说明
- **输出**: 构建信息文件

### 3. build-apk.yml (预留)
- **状态**: 暂不可用
- **原因**: uni-app 限制
- **替代**: 使用 HBuilderX

## 🎯 最佳实践

1. **开发阶段**：
   - 使用 H5 预览（`npm run dev:h5`）
   - 快速迭代和测试

2. **测试阶段**：
   - 使用 HBuilderX 打包测试版
   - 使用 DCloud 公用证书

3. **发布阶段**：
   - 使用自有证书打包正式版
   - 创建 GitHub Release
   - 上传 APK 到应用商店

## 🔗 相关链接

- **HBuilderX 下载**: https://www.dcloud.io/hbuilderx.html
- **DCloud 开放平台**: https://dev.dcloud.net.cn/
- **uni-app 文档**: https://uniapp.dcloud.net.cn/
- **GitHub Actions 文档**: https://docs.github.com/actions

## 💡 提示

如果你需要完全自动化的 APK 构建，考虑：

1. **使用 React Native** 或 **Flutter**
   - 更好的 CI/CD 支持
   - 可在 GitHub Actions 中直接构建

2. **使用 Cordova/Capacitor**
   - 基于 Web 技术
   - 支持自动化构建

3. **付费使用 DCloud 企业服务**
   - 提供 API 接口
   - 支持自动化云打包

---

**当前推荐方案**：
- ✅ 使用 GitHub Actions 构建 H5
- ✅ 使用 HBuilderX 手动打包 APK
- ✅ 使用 GitHub Release 分发 APK
