# GitHub Secrets 配置指南

## 🔐 需要配置的 Secrets

访问：https://github.com/iwenke/lazy-money/settings/secrets/actions

点击 "New repository secret" 添加以下 4 个 Secrets：

---

## 1️⃣ KEYSTORE_BASE64

**Name**: `KEYSTORE_BASE64`

**Value**:
```
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
```

---

## 2️⃣ KEYSTORE_PASSWORD

**Name**: `KEYSTORE_PASSWORD`

**Value**:
```
android123
```

---

## 3️⃣ KEY_PASSWORD

**Name**: `KEY_PASSWORD`

**Value**:
```
android123
```

---

## 4️⃣ KEY_ALIAS

**Name**: `KEY_ALIAS`

**Value**:
```
lazy-money
```

---

## ✅ 配置完成后

所有 4 个 Secrets 添加完成后，你会看到：

```
KEYSTORE_BASE64    ••••••••
KEYSTORE_PASSWORD  ••••••••
KEY_PASSWORD       ••••••••
KEY_ALIAS          ••••••••
```

## 📝 注意事项

1. **KEYSTORE_BASE64** 是整个证书文件的 Base64 编码
2. **密码** 都是 `android123`（可以在下次打包时修改）
3. **别名** 是 `lazy-money`
4. 这些 Secrets 只在 GitHub Actions 中使用

## ⚠️ 重要提醒

虽然配置了 GitHub Secrets，但由于 uni-app 的限制：

- ❌ **无法在 GitHub Actions 中直接打包原生 APK**
- ✅ **可以在 GitHub Actions 中构建 H5 版本**
- ✅ **推荐使用 HBuilderX 进行云打包**

这些 Secrets 主要用于：
1. 记录证书信息
2. 未来可能的自动化需求
3. 与其他 CI/CD 工具集成

## 🚀 实际打包方式

请参考 `QUICK_START.md` 使用 HBuilderX 进行打包。

---

**配置时间**: 2026-03-27
**证书有效期**: 100年
