# Vault for Lazycat Platform

<div align="center">
  <h3>HashiCorp Vault 密钥管理系统</h3>
  <p>在 Lazycat 平台上一键部署企业级密钥管理服务</p>
</div>

---

## 项目简介

本项目将 HashiCorp Vault 移植到 Lazycat 平台,使用户能够轻松部署和使用这一强大的密钥管理系统。HashiCorp Vault 是一个用于安全存储和访问密钥、密码、证书、API 密钥等敏感信息的开源工具,提供统一的接口来访问任何密钥,同时提供严格的访问控制和详细的审计日志。

通过 Lazycat 平台,您无需手动配置 Docker、环境变量或复杂的部署流程,只需一键即可完成 Vault 的部署,专注于密钥管理本身。

## 主要功能

### 🔐 安全密钥存储
- 加密存储密码、API 密钥、证书等敏感信息
- 提供版本控制的 Key-Value 密钥引擎
- 支持多种密钥引擎(AWS、数据库、SSH 等)

### 🔑 身份验证与授权
- 支持多种身份验证方法(Token、LDAP、GitHub、云服务商等)
- 细粒度的访问控制策略(ACL)
- 动态密钥生成,自动轮换过期密钥

### 📊 审计与监控
- 详细的审计日志记录所有访问行为
- 实时监控密钥使用情况
- 支持日志导出到外部系统

### 🌐 灵活的访问方式
- 直观的 Web 用户界面
- RESTful API 接口
- 命令行工具(vault CLI)
- 多语言客户端库

### 🔄 高可用与扩展
- 支持集群部署模式
- 数据持久化存储
- 自动故障转移

## 在 Lazycat 平台上使用

### 一键部署

在 Lazycat 平台上,您只需:

1. 在应用商店中找到 "Vault 密钥管理" 应用
2. 点击安装,选择合适的盒子(box)
3. 等待部署完成,访问应用 URL

应用部署后,访问地址为: `https://vault.{box-name}.heiyu.space`

### 首次使用

**重要提示:** Vault 在首次启动时需要进行初始化和解封操作,这是 Vault 安全机制的核心!

#### 通过 Web UI 初始化(推荐)

1. 访问应用的 Web 界面
2. 点击 "Initialize" 按钮
3. 配置密钥分片数量(Key Shares)和解封阈值(Key Threshold)
   - 默认为 5 个密钥分片,需要 3 个分片才能解封
4. 点击 "Initialize" 完成初始化
5. **关键步骤:** 立即下载或复制保存所有 Unseal Keys 和 Root Token!
   - 这些信息一旦丢失无法恢复
   - 建议将 Unseal Keys 分散保管在不同的安全位置
6. 使用所需数量的 Unseal Keys 依次解封 Vault
7. 使用 Root Token 登录系统

### 日常使用

#### Web UI 操作

1. 使用 Root Token 或其他认证方式登录
2. 启用需要的密钥引擎(例如 KV v2)
3. 创建和管理密钥
4. 配置访问策略和认证方法
5. 查看审计日志

#### API 访问

Vault 提供完整的 RESTful API,您可以通过编程方式访问:

```bash
# 设置环境变量
export VAULT_ADDR='https://vault.{box-name}.heiyu.space'
export VAULT_TOKEN='your-token'

# 写入密钥
curl -H "X-Vault-Token: $VAULT_TOKEN" \
  -X POST \
  -d '{"data":{"password":"secret"}}' \
  $VAULT_ADDR/v1/secret/data/myapp

# 读取密钥
curl -H "X-Vault-Token: $VAULT_TOKEN" \
  $VAULT_ADDR/v1/secret/data/myapp
```

## 安全注意事项

### 🔑 Unseal Keys 管理
- 将 Unseal Keys 分散保管在安全的地方
- 需要达到设定的阈值数量才能解封 Vault
- 不要将所有 Keys 存储在同一位置

### 🎫 Root Token 保护
- Root Token 具有完全权限,请妥善保管
- 生产环境建议创建受限权限的 Token 用于日常使用
- 定期轮换 Token

### 🔒 生产环境配置
- 当前配置为开发/测试环境,禁用了 TLS
- 生产环境请务必启用 TLS 加密
- 考虑使用更高级的存储后端(如 Consul)

### 💾 数据备份
- 定期备份 Vault 数据和配置
- 确保 Unseal Keys 的备份
- 测试恢复流程

### 📝 审计日志
- 建议启用审计日志功能
- 记录所有访问行为便于安全审计
- 定期审查日志

## 常见问题

### Vault 处于 sealed 状态?

Vault 每次重启后会自动进入 sealed(密封)状态,这是正常的安全机制。请访问 Web UI,输入所需数量的 Unseal Keys 进行解封。

### 忘记 Root Token?

如果忘记 Root Token,可以使用 Unseal Keys 生成新的 Root Token。具体操作请参考 [Vault 官方文档 - Generate Root Tokens](https://www.vaultproject.io/docs/commands/operator/generate-root)。

### 如何轮换密钥?

Vault 支持密钥轮换功能。对于 KV v2 引擎,每次更新密钥都会创建新版本。您也可以配置动态密钥引擎实现自动轮换。

### 如何集成到我的应用?

Vault 提供多种集成方式:
- 使用 Vault Agent 自动获取和更新密钥
- 使用各语言的 SDK(Go、Python、Node.js 等)
- 通过 REST API 直接访问
- 使用 Kubernetes 集成(Vault Injector)

## 致谢

本项目基于以下优秀的开源项目:

- **HashiCorp Vault**: HashiCorp 公司开发和维护的企业级密钥管理系统
- **Vault 社区**: 为 Vault 项目做出贡献的所有开发者和用户
- **Lazycat 平台**: 提供便捷的应用部署和管理基础设施

感谢开源社区的无私贡献,使得这样强大的密钥管理系统能够被更多人轻松使用。

## 版权说明

### HashiCorp Vault

- **版权:** HashiCorp, Inc.
- **协议:** Business Source License 1.1 (BSL 1.1)
- 原始项目: [hashicorp/vault](https://github.com/hashicorp/vault)

详细的许可证信息请查看项目中的 [LICENSE](./LICENSE) 文件。

**注意:** Vault 1.15.0 及更高版本使用 BSL 1.1 许可证。该许可证允许在大多数情况下免费使用,但对商业竞争性产品有特定限制。四年后将自动转换为 MPL 2.0 许可证。

### Lazycat 平台打包配置

- **版权:** Copyright (c) 2025 Lazycat Apps
- **协议:** MIT License

本仓库中的打包、配置和部署脚本使用 MIT 许可证发布。

## 相关链接

### 官方资源

- **Vault 官方网站:** https://www.vaultproject.io/
- **Vault 官方文档:** https://www.vaultproject.io/docs
- **Vault API 文档:** https://www.vaultproject.io/api-docs
- **HashiCorp GitHub:** https://github.com/hashicorp/vault
- **许可证 FAQ:** https://www.hashicorp.com/license-faq

### Lazycat 平台

- **本项目仓库:** https://github.com/lazycatapps/vault
- **Lazycat 平台:** https://lazycat.cloud

### 学习资源

- [Vault 入门教程](https://learn.hashicorp.com/vault)
- [Vault 最佳实践](https://www.vaultproject.io/docs/internals/security)
- [Vault 集成示例](https://github.com/hashicorp/vault-examples)

---

<div align="center">
  <p>如有问题或建议,欢迎提交 Issue 或 Pull Request</p>
  <p>Made with ❤️ by Lazycat Apps</p>
</div>
