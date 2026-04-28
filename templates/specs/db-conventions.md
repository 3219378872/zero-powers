# 数据库约定

> 本文件记录项目特有的数据库管理规范，由 /zero-init-specs 生成。
> go-zero 通用最佳实践不在此重复，参见 zero-powers 插件内置规则。
> 可随时编辑，使用 /structure-review 验证。

## 规则

### 迁移文件命名
- **判定标准**：迁移文件是否遵循 `{timestamp}_{description}.sql` 格式
- **违规示例**：`migrations/add_user.sql`（缺少时间戳前缀）
- **修复方式**：重命名为 `migrations/20260428120000_add_user.sql`

### 自定义 Model 扩展位置
- **判定标准**：自定义方法是否写在 `custom*Model` 上而非修改生成的 `default*Model`
- **违规示例**：在 `defaultUsersModel` 中添加 `FindByEmail` 方法
- **修复方式**：将自定义方法迁移到 `customUsersModel`

### 分库分表策略
- **判定标准**：分库分表规则是否记录在此并与代码实现一致
- **违规示例**：代码中使用 hash 分表但未在规范中声明
- **修复方式**：补充分表策略描述，或调整代码匹配规范
