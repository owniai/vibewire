# Stage {M}: {阶段名称}

> 里程碑：Milestone {N}: {里程碑名称}

## API Contract

本阶段涉及的函数/类签名、类型定义和模块间调用关系。所有执行者均以此为契约。

### {模块/文件名}

```python
# {path/to/module.py}

class ClassName:
    """简述职责"""
    def method_name(self, param: Type, param2: Type) -> ReturnType:
        """参数说明和返回值说明"""
        ...

def function_name(param: Type, param2: Type) -> ReturnType:
    """参数说明和返回值说明"""
    ...
```

**异常行为：** {描述异常/错误情况及处理方式}

**模块间调用：** {描述本模块被谁调用、调用了谁}

## 测试规格

**测试文件：** `tests/path/to/test.py`（按项目实际技术栈调整）

### 单元测试

#### 正常路径：{名称}

| 字段 | 内容 |
| --- | --- |
| 用例名 | `test_xxx` |
| 意图 | {验证什么行为} |
| 输入 | {具体输入值} |
| 预期输出 | {具体预期返回值/状态} |
| 涉及 API | `module.function_name` |

#### 异常路径：{名称}

| 字段 | 内容 |
| --- | --- |
| 用例名 | `test_xxx_error` |
| 意图 | {验证什么异常行为} |
| 输入 | {具体无效输入} |
| 预期输出 | {预期异常类型或返回值} |
| 涉及 API | `module.function_name` |

### 集成测试（如涉及多模块交互）

#### {名称}

| 字段 | 内容 |
| --- | --- |
| 用例名 | `test_xxx_integration` |
| 意图 | {验证跨模块协作行为} |
| 输入 | {具体输入值} |
| 预期输出 | {预期跨模块行为结果} |
| 涉及 API | `module_a.func_a`, `module_b.func_b` |

**Red 确认：** 运行 `pytest tests/path/test.py -v`，预期全部失败（测试正确加载，因功能未实现而失败）

## Task 1: {组件名称}

**文件：**

- 创建：`exact/path/to/file.py`
- 修改：`exact/path/to/existing.py:123-145`

**预期成果：** [描述完成后的状态，如"新文件已创建，模块导出已更新"]

**实现：**

[完整实现代码]

## Task 2: {组件名称}

**文件：**

- 修改：`exact/path/to/another.py:30-42`

**预期成果：** [描述完成后的状态，如"函数逻辑已更新，返回正确结果"]

**实现：**

[完整实现代码]

## Task 3: [公共库] {组件名称}

**文件：**

- 创建：`exact/path/to/new_module.py`
- 修改：`exact/path/to/config.py:10`

**预期成果：** [描述完成后的状态，如"新模块已创建并注册到配置中"]

**实现：**

[完整实现代码]
