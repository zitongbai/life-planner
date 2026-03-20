# 个人日程数据表格模板

## 字段定义（12个）

| 序号 | 字段名 | 类型 | type值 | 选项/配置 |
|------|--------|------|--------|-----------|
| 1 | 任务名称 | 文本 | 1 | 主字段（需从默认"文本"重命名） |
| 2 | 类型 | 单选 | 3 | Action, Project, Habit |
| 3 | 状态 | 单选 | 3 | 待办, 进行中, 已完成, 已取消 |
| 4 | 今日优先级 | 单选 | 3 | Critical, Progress, Optional, 无 |
| 5 | Area | 单选 | 3 | 科研, 学习, 健康, 党建, 生活 |
| 6 | 所属项目 | 关联 | 18 | 自关联，multiple=false |
| 7 | 截止日期 | 日期 | 5 | date_formatter="yyyy/MM/dd HH:mm" |
| 8 | 预计耗时（分钟） | 数字 | 2 | formatter="0" |
| 9 | 实际耗时（分钟） | 数字 | 2 | formatter="0" |
| 10 | 描述 | 文本 | 1 | - |
| 11 | 创建时间 | 创建时间 | 1001 | date_formatter="yyyy/MM/dd HH:mm" |
| 12 | 最后更新 | 修改时间 | 1002 | date_formatter="yyyy/MM/dd HH:mm" |

---

## 视图定义（4个）

| 序号 | 视图名称 | 视图类型 | 筛选条件 |
|------|----------|----------|----------|
| 1 | 全部任务 | grid | 无 |
| 2 | 项目视图 | grid | 类型 = Project |
| 3 | 习惯视图 | grid | 类型 = Habit |
| 4 | 按Area分组 | grid | 按 Area 字段分组 |

---

## 字段类型对照表

| type值 | 类型名称 | ui_type | 说明 |
|--------|----------|---------|------|
| 1 | 文本 | Text | 普通文本字段 |
| 2 | 数字 | Number | 数值字段 |
| 3 | 单选 | SingleSelect | 单选字段 |
| 5 | 日期 | DateTime | 日期时间字段 |
| 18 | 单向关联 | SingleLink | 关联到其他数据表 |
| 1001 | 创建时间 | CreatedTime | 自动记录创建时间 |
| 1002 | 修改时间 | ModifiedTime | 自动记录修改时间 |

---

## 字段创建 API 参数

### 单选字段（type=3）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "<字段名>",
  "type": 3,
  "property": {
    "options": [
      {"name": "选项1"},
      {"name": "选项2"}
    ]
  }
}
```

### 数字字段（type=2）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "<字段名>",
  "type": 2,
  "property": {
    "formatter": "0"
  }
}
```

### 日期字段（type=5）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "<字段名>",
  "type": 5,
  "property": {
    "date_formatter": "yyyy/MM/dd HH:mm"
  }
}
```

### 文本字段（type=1）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "<字段名>",
  "type": 1
}
```

### 关联字段（type=18）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "所属项目",
  "type": 18,
  "property": {
    "table_id": "<本表的table_id>",
    "multiple": false
  }
}
```

### 创建时间字段（type=1001）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "创建时间",
  "type": 1001,
  "property": {
    "date_formatter": "yyyy/MM/dd HH:mm"
  }
}
```

### 修改时间字段（type=1002）

```json
{
  "action": "create",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "field_name": "最后更新",
  "type": 1002,
  "property": {
    "date_formatter": "yyyy/MM/dd HH:mm"
  }
}
```

---

## 默认表清理

新创建的多维表格会自带以下默认内容，需要删除：

**默认字段**（需删除）：
- 文本（主字段，需重命名为"任务名称"）
- 单选
- 日期
- 附件

**默认视图**（需删除）：
- 表格

**空记录**（需删除）：
- 新建表格时飞书可能自动创建空记录（只有自动时间字段有值，任务名称为空）

**清理步骤**：

1. 获取字段列表：`feishu_bitable_app_table_field.list`
2. 重命名主字段：`feishu_bitable_app_table_field.update`
   - field_id: 主字段的 field_id
   - field_name: "任务名称"
3. 删除冗余字段：`feishu_bitable_app_table_field.delete`
   - 删除除主字段外的所有默认字段
4. 获取视图列表：`feishu_bitable_app_table_view.list`
5. 删除默认视图：`feishu_bitable_app_table_view.delete`
6. 删除空记录：
   - 获取记录列表：`feishu_bitable_app_table_record.list`
   - 筛选任务名称为空的记录
   - 批量删除：`feishu_bitable_app_table_record.batch_delete`

---

## 完整创建顺序

1. 创建多维表格 → 获取 app_token, default_table_id
2. 创建字段（按序号顺序）：
   - 类型、状态、今日优先级、Area（单选）
   - 截止日期（日期）
   - 预计耗时、实际耗时（数字）
   - 描述（文本）
   - 所属项目（关联，需要 default_table_id）
   - 创建时间、最后更新（自动时间）
3. 创建视图：
   - 全部任务
   - 项目视图
   - 习惯视图
   - 按Area分组
4. 自检与修复：
   - 重命名主字段
   - 删除默认字段
   - 删除默认视图
   - 删除空记录

---

## 删除空记录 API

**筛选空记录**：
```json
{
  "action": "list",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "filter": {
    "conjunction": "and",
    "conditions": [
      {
        "field_name": "任务名称",
        "operator": "isEmpty"
      }
    ]
  }
}
```

**批量删除**：
```json
{
  "action": "batch_delete",
  "app_token": "<app_token>",
  "table_id": "<table_id>",
  "record_ids": ["<record_id_1>", "<record_id_2>", ...]
}
```