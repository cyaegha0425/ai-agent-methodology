# Git仓库索引

## 仓库列表

| 仓库名 | GitHub地址 | 本地路径 | 说明 |
|--------|------------|----------|------|
| production-assistant | https://github.com/cyaegha0425/production-assistant | ./知识库/生产助手/ | 阀门产品组装模板管理 |
| skills | https://github.com/cyaegha0425/skills | ./skills/ | AI Skills集合 |
| ai-agent-methodology | https://github.com/cyaegha0425/ai-agent-methodology | ./知识库/ | AI协作驱动开发方法论+知识库 |

---

## Skill索引

| Skill名 | 路径 | 说明 |
|---------|------|------|
| assembly_template_generator | ./skills/assembly_template_generator/ | 组装模板生成器 |
| doc_number_generator | ./skills/doc_number_generator/ | ISO文档编号生成器 |

---

## 仓库所有者

- **用户名**：cyaegha0425
- **Token**：已记录在 TOOLS.md

---

## 使用规范

1. 新建仓库 → 先用API创建 → 本地git init → push
2. 仓库结构变更 → 及时更新本索引
3. 其他AI调用 → 读本索引了解有哪些仓库

---

## 文字处理规则（组装模板）

**⚠️ 原文复制不改写**
- 模板名称、规格描述、物料编码等**必须原文复制**用户提供的内容
- **可以加空格**拆分语义单元，如：
  - `慧飞金穗S14G` → `慧飞 金穗S1 4G`（品牌/系列/通讯方式）
  - `FP,双头球阀` → `FP, 双头球阀`（逗号后加空格）
  - 括号包围物料编码：`(A02100110-0831)`
- **严禁**修改任何文字、字母、数字、符号
- 不确定的地方直接问用户确认，**不能猜**
