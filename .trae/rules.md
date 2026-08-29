---
name: rules
description: 项目级 AI 协作规则模板 —— 每个项目根目录一份，由 /init 按所选技术栈生成/裁剪
---

# 项目级 AI 规则（Project Rules）

> **位置**：每个项目根目录一份 `rules.md`（建议同时设为工具的 always-on 项目规则）
> **作用**：告诉 AI「这个项目**怎么写代码、怎么交付**」，与各阶段 Skill 配合
> **用法**：标【按栈填写】的小节由 `/init` 根据选定语言/框架填充；其余为通用规则，可直接生效
> **修改权限**：可改，但每次改动要 commit 并说明理由

---

## 〇、文档与产物落盘规范（全局，always-on，最高优先级）

> 这一节对所有阶段生效，防止 AI「在对话里打印就当交付」。

- ✅ 所有阶段产物（PRD / ARCH / SDD / TEST / REVIEW / OVERVIEW / FEATURE / DEPLOY）**必须真正写入项目 `docs/` 目录**，不是只打印在对话里
- ✅ 写完必须**校验文件存在且非空**，并报告路径（如 `📄 已写入 docs/PRD.md`）
- ✅ 代码产物写入源码目录；自测脚本必须**真实运行**并贴出输出
- ❌ 无文件写入权限时**停下并提示用户开启**，禁止用「已生成 / 已完成」蒙混
- ❌ 上一阶段产物未落盘，**不得进入下一阶段**

---

## 一、命名规范

### 通用（所有项目）
- 命名见名知意，禁止用实现所有者前缀替代领域命名
- 不堆叠拼音缩写；缩写仅用公认词（id / url / api 等）

### 语言代码命名【按栈填写】
- 文件名：{{如 snake_case.py / kebab-case.ts / PascalCase.cs}}
- 类型 / 类名：{{}}
- 函数 / 变量：{{}}
- 常量：{{}}
- 私有成员：{{}}

### 数据存储【按存储类型填写】
- 表 / 集合名：{{snake_case + 业务前缀，如 t_user / sys_user}}
- 字段名：{{}}
- 索引名：`idx_<表>_<字段>` / `uk_<表>_<字段>`（唯一）
- 外键 / 关系：`fk_<子表>_<父表>`

### 接口 / API
- HTTP：路径全小写、连字符分隔、资源用复数（`/api/users`）；操作语义清晰（`/api/users/{id}/activate`）
- RPC / CLI / 库：改用对应命名约定（方法名 / 子命令 / 导出符号）

---

## 二、错误处理偏好（通用）

### 必须做
- ✅ 所有资源（连接 / 句柄 / 文件）**确定性释放**（try/finally 或语言等价机制）
- ✅ 业务异常捕获后返回**可读的业务错误**，不要直接抛内部错误给调用方
- ✅ 每个 catch / except 块要 **log 错误**
- ✅ 用户输入错误返回**友好提示**，不暴露技术细节

### 禁止做
- ❌ 静默吞异常（空 catch / `except: pass`）
- ❌ 把底层错误信息（如数据库报错原文、堆栈）直接返回给调用方
- ❌ 用「捕获一切」掩盖具体错误而不区分

---

## 三、注释与文档

- **注释语言**：{{中文 / 英文，按项目}}；关键算法必须有 1-2 行说明
- **函数说明**：每个对外函数加一行「做什么 / 入参 / 返回值」
- **TODO 标记**：不允许裸 TODO，必须写 `TODO(优先级): 说明`，如 `TODO(P1): 加批量导入接口`

---

## 四、测试覆盖偏好

### 必须测试
- ✅ 所有 P0 接口至少有 1 个正常 + 1 个异常用例
- ✅ 数据约束（唯一 / 外键 / 级联）必须有对应测试

### 可豁免（按项目，写明就豁免；/review 以此为准）
- {{如：小型工具的边界值测试 / 单文件 < 100 行的工具单测}}
- {{如：原型阶段的某些非功能测试}}

---

## 五、依赖管理

- ✅ 所有第三方依赖进依赖清单（**【按栈填写】**：`requirements.txt` / `package.json` / `go.mod` / `pom.xml` / `Cargo.toml` …）
- ✅ 按项目策略锁版本（锁主版本或锁定 lockfile），避免破坏性升级
- ✅ 安装新依赖后立即更新依赖清单
- ❌ 使用未登记在依赖清单中的库

---

## 六、Git 提交规范

### commit message 格式
```
{{emoji}} {{阶段}}：{{一句话描述}}

{{可选：详细说明}}
```

### 阶段 emoji 对照
| 阶段 | emoji |
|---|---|
| /init | 🎯 |
| /prd | 📋 |
| /hld | 🏗️ |
| /sdd | 📐 |
| /impl | 💻 |
| /review | 🔍 |
| /retro | 📚 |
| /deploy | 🚀 |
| 修复 bug | 🐛 |
| 重构 | ♻️ |
| 文档 | 📝 |

### 示例
```bash
git commit -m "📋 /prd：完成图书管理系统 PRD"
git commit -m "💻 /impl：实现 8 个接口，自测通过"
git commit -m "🐛 修复：注入风险（P0-001）"
```

---

## 七、迭代纪律（通用）

- **铁律**：每阶段最多迭代 **3 次**，第 4 次还卡住 → 升级人工决策
- 各阶段的专属回退路径见对应 Skill 的「迭代与回退路径」一节
- 跨阶段冲突以上游为准：PRD > ARCH > SDD > 实现；发现下游与上游不符，回退修上游

---

## 八、本项目专属约定

> 在这里写本项目**独有**的规则，不要重复上面的通用规则

### 项目名约定
- 中文名：{{项目名}}
- 英文标识符：{{project_slug}}
- 包名 / 命名空间：{{package}}

### 技术栈特殊约束
- {{如：必须用某框架的某特性 / 必须用某驱动}}

### 业务规则
- {{如：成绩不能为负、不能超过 100}}

---

## 九、踩坑记录

### Git 推送报错：代理 + Remote URL 反引号问题

**日期**：2026-08-29

**现象**：

```bash
$ git push -u origin main
fatal: unable to access '`https://github.com/originwsc/ESPS3.git/`': Failed to connect to 127.0.0.1 port 7890 after 4 ms: Couldn't connect to server
```

**根因**：

1. **Remote URL 被反引号包裹**：执行 `git remote add origin \`https://github.com/...\`` 时，shell 把反引号 `` ` `` 也当成了 URL 的一部分，导致实际 URL 为 `` `https://github.com/originwsc/ESPS3.git/` ``。
2. **Git 代理配置了多个重复值**：`http.proxy` 和 `https.proxy` 被重复设置了多次，且有一次设置格式错误（`127.0.0.1:` 缺少端口号），导致 git 无法解析代理。

**解决步骤**：

```bash
# 1. 修复 remote URL（去掉反引号）
git remote set-url origin https://github.com/originwsc/ESPS3.git

# 2. 修复 http.proxy（替换所有重复值）
git config --global --replace-all http.proxy http://127.0.0.1:7897

# 3. 修复 https.proxy（替换所有重复值）
git config --global --replace-all https.proxy http://127.0.0.1:7897

# 4. 推送到远程
git push -u origin main
```

**教训**：

- `git remote add` 时 URL **不要用反引号包裹**，直接写裸 URL 即可。
- 修改 git 配置时如果提示 `has multiple values`，要用 `--replace-all` 而不是直接 `=` 赋值。
- 代理配置格式必须是 `http://host:port`，不能缺少协议头 `http://` 或端口号。

---

## 十、规则变更日志

| 版本 | 日期 | 变更 | 作者 |
|---|---|---|---|
| v1.0 | {{日期}} | 初稿（由 /init 生成） | {{作者}} |