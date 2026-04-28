# 配置管理

> 本文件记录项目特有的配置管理规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 多环境配置结构
- **判定标准**：配置文件是否按项目约定的环境分离方式组织
- **违规示例**：项目约定 `etc/{env}.yaml`，但新服务只有 `etc/config.yaml`
- **修复方式**：拆分为 `etc/dev.yaml`、`etc/staging.yaml`、`etc/prod.yaml`

### 敏感值管理
- **判定标准**：密钥、token 等是否通过约定方式注入（环境变量 / vault / sealed-secrets）
- **违规示例**：`etc/prod.yaml` 中明文写入数据库密码
- **修复方式**：改为 `${DB_PASSWORD}` 占位，通过环境变量或 secret manager 注入

### 配置分层策略
- **判定标准**：公共配置与服务专属配置是否按约定分层
- **违规示例**：每个服务重复定义相同的 Redis/etcd 连接信息
- **修复方式**：提取到公共配置层或使用配置继承机制
