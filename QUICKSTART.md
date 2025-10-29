# Vault 快速开始指南

本文档将引导你快速上手 HashiCorp Vault，从初始化、解封到存储和管理密钥。

## 前提条件

确保已经在 Lazycat 上一键部署完成 Vault 服务。

## 快速开始流程

首次使用 Vault 需要按以下顺序操作：

1. **访问 Web UI** → 看到初始化页面
2. **初始化 Vault** → 配置 Key shares 和 Key threshold，获得 root token 和解封密钥
3. **⚠️ 立即保存** → root token 和所有解封密钥（只显示一次！）
4. **解封 Vault** → 输入解封密钥（每次重启都需要）
5. **登录** → 使用 root token 登录
6. **开始使用** → 创建密钥引擎和存储密钥

下面是详细步骤：

---

## 通过 Web UI 使用

Web UI 是最直观的使用方式，适合初学者和日常管理。

### 1. 访问 Web UI

打开浏览器访问你的 Vault 地址（例如：**https://vault.{box-name}.heiyu.space** 或 Lazycat 提供的访问地址）

### 2. 初始化 Vault

首次访问时，你会看到初始化页面：**"Let's set up the initial set of root keys that you will need in case of an emergency."**

#### 配置解封密钥

1. **Key shares（密钥份额）**：设置将主密钥分割成多少份
   - 默认值：`5`
   - 建议：个人使用可以设置为 `1`，团队使用建议 `3-5`

2. **Key threshold（密钥阈值）**：设置解封 Vault 需要多少份密钥
   - 默认值：`3`
   - 建议：个人使用设置为 `1`，团队使用建议为 shares 的一半以上

3. 点击 **Initialize** 按钮

#### 保存重要信息

初始化完成后，系统会显示：

- **Initial root token**：root 令牌（格式类似 `hvs.xxxxxxxxxxxxxxxxxxxxxxxx`）
- **Key 1, Key 2, ... Key N**：解封密钥（根据你设置的 shares 数量）

⚠️ **非常重要**：
- **立即保存这些信息到安全的地方**（密码管理器、加密文件等）
- 这些信息只会显示一次，丢失后无法恢复
- 没有这些密钥，你将永远无法访问 Vault 中的数据
- root token 用于登录，Key 用于解封

点击 **Download keys** 下载 JSON 文件作为备份（建议加密保存）

### 3. 解封 Vault (Unseal)

初始化后，Vault 处于"密封"状态。点击 **Continue to Unseal** 进入解封页面。

系统会提示：

> **Vault is sealed**
>
> Unseal Vault by entering portions of the unseal key. This can be done via multiple mechanisms on multiple computers. Once all portions are entered, the root key will be decrypted and Vault will unseal.

#### 解封步骤

1. 在 **Unseal Key Portion** 输入框中输入一个解封密钥
2. 点击 **Unseal** 按钮
3. 如果你的 threshold > 1，重复以上步骤，输入其他密钥
4. 当输入的密钥数量达到阈值时，Vault 会自动解封

> 💡 **提示**：
> - 如果你设置 threshold = 1，只需输入一次密钥即可解封
> - 页面会显示进度：例如 "Unseal Progress: 1/3"
> - 每次 Vault 重启后都需要重新解封

### 4. 登录 Vault

解封成功后，会自动跳转到登录页面：**Sign in to Vault**

1. **Method（方法）**：选择 `Token`
2. **Token**：输入前面保存的 **Initial root token**
3. 点击 **Sign In** 按钮

![登录界面](https://www.vaultproject.io/img/vault-ui-login.png)

### 5. 启用密钥引擎

登录成功后，首次使用需要启用 KV（Key-Value）密钥引擎：

1. 点击顶部菜单的 **Secrets**
2. 如果还没有 `secret/` 路径，点击 **Enable new engine**
3. 选择 **KV** 类型
4. **Version** 选择 `2`
5. **Path** 输入 `secret`
6. 点击 **Enable Engine**

### 6. 创建第一个密钥

#### 方法 A：通过界面创建

1. 点击 **secret/** 进入密钥引擎
2. 点击 **Create secret +** 按钮
3. **Path for this secret** 输入路径，例如：`myapp/config`
4. 添加密钥数据（点击 **+ Add** 添加更多字段）：
   - Key: `username`, Value: `admin`
   - Key: `password`, Value: `supersecret123`
   - Key: `database_url`, Value: `postgresql://localhost:5432/mydb`
5. 点击 **Save** 保存

#### 方法 B：使用 JSON 编辑器

1. 在创建密钥页面，切换到 **JSON** 标签
2. 输入 JSON 格式的数据：
   ```json
   {
     "username": "admin",
     "password": "supersecret123",
     "database_url": "postgresql://localhost:5432/mydb",
     "api_key": "abc123xyz"
   }
   ```
3. 点击 **Save**

### 7. 查看密钥

1. 在 **secret/** 列表中，点击你刚创建的密钥路径（如 `myapp/config`）
2. 默认情况下，敏感值会被隐藏，点击 👁️ 图标可以显示
3. 可以看到：
   - **Secret Data**：密钥的键值对
   - **Metadata**：版本信息、创建时间等

### 8. 编辑密钥

1. 在密钥详情页，点击右上角的 **Create new version +**
2. 修改现有值或添加新字段
3. 点击 **Save**
4. 每次保存都会创建一个新版本，旧版本会被保留

### 9. 查看历史版本

1. 在密钥详情页，点击 **Version** 标签
2. 可以看到所有历史版本
3. 点击任意版本号可以查看该版本的数据
4. 可以选择删除或恢复特定版本

### 10. 复制密钥值

在密钥详情页：
- 点击每个值右侧的 📋 图标可以快速复制到剪贴板
- 无需手动选择和复制

### 11. 删除密钥

⚠️ **注意**：删除操作需谨慎

1. 在密钥详情页，点击右上角的 **Delete** 下拉菜单
2. 选择删除选项：
   - **Delete latest version**：删除最新版本
   - **Delete all versions**：删除所有版本（软删除）
   - **Destroy all versions**：永久销毁（不可恢复）

### 12. 搜索密钥

在 **Secrets** 页面顶部有搜索框，可以快速搜索密钥路径。

## 最佳实践

### 1. 安全性

- ✅ **不要在代码中硬编码 token**
- ✅ **使用最小权限原则**：不要让所有应用都使用 root token
- ✅ **定期轮换密钥**：利用版本控制功能
- ✅ **启用审计日志**：追踪所有访问
- ⚠️ **生产环境必须启用 TLS**

### 2. 命名规范

- 使用清晰的层级结构：`secret/<app>/<env>/<component>`
- 使用小写和连字符：`my-app` 而不是 `MyApp` 或 `my_app`
- 保持路径简洁但有意义

### 3. 版本管理

- 更新前先读取当前值，确保不丢失其他字段
- 在 Web UI 中使用 **Create new version** 功能更新密钥
- 定期清理旧版本（根据保留策略）

### 4. 备份

定期备份 Vault 数据：

**通过 Lazycat 备份**：
- 在 Lazycat 平台上使用数据备份功能
- 或者按照 Lazycat 提供的备份指南操作
- Lazycat 会自动备份 Vault 的数据存储（`/vault/file`）

---

## 故障排查

### 问题 1：无法连接到 Vault

**检查服务状态**：
- 在 Lazycat 平台上检查 Vault 服务状态
- 查看应用的健康检查状态
- 确认应用已正常启动并通过健康检查

### 问题 2：Vault 显示 "Sealed"

**解决方法**：
- Vault 在重启后会自动密封，这是正常的安全机制
- 按照第 3 步的解封步骤操作
- 在 Web UI 中输入解封密钥（根据你设置的 threshold 数量）
- 观察解封进度，直到显示 "Unsealed"

### 问题 3：认证失败

**解决方法**：
1. 确认输入的 Root Token 正确（检查是否有多余的空格）
2. 确认 Vault 已经解封（处于 Unsealed 状态）
3. 如果是首次使用，确认使用的是初始化时保存的 Initial Root Token

### 问题 4：密钥路径不存在

**解决方法**：
1. 在 Web UI 中，进入 **Secrets** 页面
2. 检查是否已启用 `secret/` 密钥引擎
3. 如果没有，参考第 5 步启用 KV 密钥引擎
4. 确认输入的密钥路径格式正确（如 `secret/myapp/config`）

### 问题 5：权限被拒绝

**解决方法**：
1. 确认当前使用的 Token 有足够权限
2. 如果是 Root Token，应该有完全权限
3. 如果使用的是其他 Token，需要检查该 Token 的策略（Policy）设置
4. 在 Web UI 的 **Policies** 页面可以查看和管理访问策略

### 问题 6：忘记解封密钥或 root token

⚠️ **非常抱歉，无法恢复**：
- Vault 的安全设计使得没有密钥就无法访问数据
- 这是 Vault 的核心安全特性
- 只能重新初始化 Vault（会丢失所有数据）

---

## 进阶功能

完成基础操作后，可以探索：

1. **策略（Policies）** - 精细的访问控制
2. **AppRole 认证** - 应用程序认证
3. **动态密钥** - 自动生成和轮换数据库凭证
4. **Transit 加密** - 加密即服务
5. **自动解封** - 使用云 KMS 自动解封
6. **审计设备** - 记录所有操作日志

参考官方文档：https://www.vaultproject.io/docs

---

## 清理和重置

### 重新初始化 Vault（谨慎！）

⚠️ **警告**：此操作会删除所有数据，无法恢复！

如果需要完全重置 Vault：

1. 在 Lazycat 平台上删除当前的 Vault 实例
2. 重新部署一个新的 Vault 实例
3. 按照本文档从第 2 步开始重新初始化

### 仅重启服务

如果只是想重启 Vault 服务（数据不会丢失）：

1. 在 Lazycat 平台上重启 Vault 服务
2. 重启后需要重新解封（第 3 步）
3. 解封后使用相同的 root token 登录

---

## 获取帮助

### 资源链接

- 📚 [官方文档](https://www.vaultproject.io/docs)
- 🎓 [学习教程](https://learn.hashicorp.com/vault)
- 💬 [社区论坛](https://discuss.hashicorp.com/c/vault)
- 🐛 [GitHub Issues](https://github.com/hashicorp/vault/issues)
