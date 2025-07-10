# 🗂️ 大文件处理指南

## 📊 文件大小限制说明

### 当前配置
- **Web界面上传限制**: 500MB
- **单个文件最大**: 500MB
- **多文件总计**: 500MB

### 为什么设置这个限制？
1. **内存管理**: 避免服务器内存溢出
2. **处理效率**: 确保符号化过程在合理时间内完成
3. **用户体验**: 避免长时间等待和超时

## 🚀 处理大dSYM文件的策略

### 1. 压缩优化
```bash
# 压缩dSYM文件夹为ZIP格式
zip -r MyApp.dSYM.zip MyApp.dSYM/

# 查看压缩效果
ls -lh MyApp.dSYM*
```

### 2. 文件清理
dSYM文件夹中可能包含不必要的文件，可以清理：
```bash
# 进入dSYM文件夹
cd MyApp.dSYM/Contents/Resources/DWARF/

# 只保留主要的符号文件
ls -la
```

### 3. 分批处理
如果有多个dSYM文件，可以分批上传：
```bash
# 使用命令行工具分别处理
python3 cli.py -i crash.ips -d App1.dSYM -o results1/
python3 cli.py -i crash.ips -d App2.dSYM -o results2/
```

## 🛠️ 命令行工具处理大文件

### 优势
- 无文件大小限制
- 更高效的内存使用
- 详细的处理进度显示

### 使用方法
```bash
# 基本用法
python3 cli.py -i large_crash.ips -d large_app.dSYM -o results/ -v

# 批量处理
python3 cli.py -i "crashes/*.ips" -d "dsyms/*.dSYM" -o results/ -v
```

## 📈 性能优化建议

### 1. 系统资源
- **内存**: 建议至少8GB可用内存
- **磁盘空间**: 确保有足够的临时空间（至少是文件大小的3倍）
- **CPU**: 符号化是CPU密集型操作

### 2. 文件准备
```bash
# 检查dSYM文件完整性
file MyApp.dSYM/Contents/Resources/DWARF/MyApp

# 验证dSYM文件UUID
dwarfdump --uuid MyApp.dSYM
```

### 3. 网络优化
- 使用稳定的网络连接
- 避免在网络高峰期上传大文件
- 考虑使用有线连接替代WiFi

## 🔍 故障排除

### 文件上传失败
```bash
# 检查文件大小
ls -lh your_file.zip

# 如果超过500MB，尝试压缩
zip -9 compressed_dsym.zip original.dSYM/
```

### 内存不足错误
```bash
# 监控内存使用
top -p $(pgrep -f "python3 app.py")

# 如果内存不足，使用命令行工具
python3 cli.py -i crash.ips -d large.dSYM -o results/
```

### 处理超时
```bash
# 增加超时时间（修改config.py）
SYMBOLICATECRASH_TIMEOUT = 300  # 5分钟

# 或使用命令行工具
python3 cli.py -i crash.ips -d large.dSYM -o results/ -v
```

## 💡 最佳实践

### 1. 文件组织
```
dsyms/
├── MyApp_v1.0.dSYM/
├── MyApp_v1.1.dSYM/
└── compressed/
    ├── MyApp_v1.0.zip
    └── MyApp_v1.1.zip
```

### 2. 版本管理
- 为每个版本的dSYM文件建立清晰的命名规则
- 使用版本号和构建号标识文件
- 定期清理旧版本的符号文件

### 3. 自动化处理
```bash
#!/bin/bash
# 自动处理脚本示例

CRASH_DIR="crashes"
DSYM_DIR="dsyms"
OUTPUT_DIR="results"

for crash_file in $CRASH_DIR/*.ips; do
    echo "处理: $crash_file"
    python3 cli.py -i "$crash_file" -d "$DSYM_DIR" -o "$OUTPUT_DIR" -v
done
```

## 🔧 高级配置

### 修改文件大小限制
如果您需要处理更大的文件，可以修改配置：

```python
# config.py
class Config:
    # 增加到1GB
    MAX_CONTENT_LENGTH = 1024 * 1024 * 1024  # 1GB
```

```javascript
// templates/index.html
function validateFileSize(file, maxSizeMB = 1024) {
    // 对应修改前端验证
}
```

### 服务器优化
```bash
# 增加nginx上传限制（如果使用nginx）
client_max_body_size 1G;

# 增加uwsgi缓冲区（如果使用uwsgi）
buffer-size = 65536
```

## 📞 技术支持

如果您遇到大文件处理问题：

1. **首先尝试**: 使用命令行工具 `python3 cli.py`
2. **检查资源**: 确保有足够的内存和磁盘空间
3. **文件压缩**: 尝试压缩dSYM文件
4. **分批处理**: 将大任务分解为小任务

---

**记住**: 对于超大文件（>500MB），强烈推荐使用命令行工具，它提供更好的性能和错误处理能力。 