# LLM Python Code Sandbox

| Logo                                       | Description   |
|--------------------------------------------|---------------|
| <img src="./asset/logo.png" width="500px"> |这是一个适合LLM使用的Python代码沙箱HTTP服务，可以创建独立的Python执行环境，并支持代码执行、文件上传下载等功能。集成了MCP支持。
|


## 功能特性

1. **创建沙箱**：初始化一个Jupyter kernel并返回唯一标识ID
2. **执行代码**：在指定ID的Jupyter kernel中运行代码，并返回stdout、stderr、error、traceback和图片等各类二进制文件
3. **文件操作**：允许向沙箱工作目录上传文件，以及下载沙箱中的文件
4. **关闭沙箱**：安全关闭沙箱并清理资源
5. **沙箱隔离**：每个沙箱拥有独立的Python虚拟环境和工作目录，避免包冲突
6. **自动清理**：沙箱创建后24小时自动关闭并清理资源，同时服务每小时进行一次过期沙箱清理
7. **虚拟环境镜像**：服务启动时自动创建包含常用包的基础虚拟环境镜像，创建新沙箱时通过复制镜像快速初始化，显著提升创建速度
8. **MCP支持**：集成FastAPI-MCP，使服务可以被AI模型直接调用

## 启动

### 安装依赖
在项目目录下执行以下命令安装依赖：
```bash
cd sandbox
pip install -r requirements.txt
```

### 启动服务
执行以下命令启动沙箱服务：
```bash
python main.py
```

服务将在 http://0.0.0.0:8000 上启动

## 使用Client调用
用户可以直接使用Client调用服务沙箱服务
```python
# 创建客户端实例
client = SandboxClient()

# 创建沙箱
sandbox_id = client.create_sandbox()

# 执行代码
client.execute_code("print('Hello, Sandbox!')")

# 在沙箱的虚拟环境中安装所需的Python包
client.install_package("numpy")

# 查看生成的文件
files = client.list_files()

# 下载生成的CSV文件
csv_file = next((f for f in files if f['path'].endswith('.csv')), None)
client.download_file(csv_file['path'])

# 上传本地文件到沙箱
client.upload_file('test_upload.txt')

# 关闭沙箱
client.close_sandbox()

# 检查所有沙箱是否已关闭
client.list_all_sandboxes()
```

## 使用MCP调用

该服务已集成FastAPI-MCP，使AI模型能够直接调用API。

### MCP端点
服务启动后，MCP端点将在 `/mcp` 路径下可用。

### 连接AI客户端
支持MCP协议的AI客户端（如Claude桌面应用、Cursor编辑器等）可以通过服务URL连接到MCP端点，例如：
`http://localhost:8000/mcp`

连接后，AI将能够理解并调用所有可用的API端点。

## API 端点

### 1. 创建沙箱

```
POST /sandbox/create
```

**返回**：
```json
{
  "sandbox_id": "<unique-id>"
}
```

### 2. 执行代码

```
POST /sandbox/{sandbox_id}/execute
```

**请求体**：
```
<your-python-code>
```

**返回**：
```json
{
  "stdout": "<standard-output>",
  "stderr": "<standard-error>",
  "error": "<error-type>",
  "traceback": "<error-traceback>"
}
```

### 3. 上传文件

```
POST /sandbox/{sandbox_id}/upload
```

**表单数据**：
- `file`: 要上传的文件
- `file_path`: (可选) 目标文件路径

**返回**：
```json
{
  "file_path": "<relative-path-to-file>"
}
```

### 4. 获取文件列表

```
GET /sandbox/{sandbox_id}/files
```

**返回**：
```json
[
  {
    "path": "<file-path>",
    "size": <file-size>
  },
  ...
]
```

### 5. 下载文件

```
GET /sandbox/{sandbox_id}/download/{file_path}
```

**返回**：文件内容

### 6. 关闭沙箱

```
POST /sandbox/{sandbox_id}/close
```

**返回**：
```json
{
  "status": "success",
  "message": "Sandbox closed"
}
```

### 7. 查看所有沙箱（调试用）

```
GET /sandboxes
```

**返回**：所有活跃沙箱的信息

### 8. 健康检查

```
GET /health
```

**返回**：
```json
{
  "status": "healthy"
}
```

## 注意事项

- 沙箱会占用系统资源，请确保在不使用时关闭沙箱
- 文件操作有安全限制，只能访问沙箱工作目录内的文件
- 默认情况下，服务监听所有网络接口(0.0.0.0)，在生产环境中请根据需要调整

-------
Tips：README系AI生成，如有错漏请提issue~