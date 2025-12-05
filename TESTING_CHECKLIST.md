# 自定义链接尾缀功能测试清单

## 🎯 测试场景

### 1. ✅ 创建带自定义尾缀的剪贴板
**步骤：**
1. 访问 `http://localhost:8087` (Rust) 或 `http://localhost:3000` (Next.js)
2. 在"内容"框输入：`测试内容`
3. 在"链接尾缀"框输入：`my-test-link`
4. 点击"创建剪贴板"

**预期结果：**
- ✅ 创建成功
- ✅ Toast提示显示链接：`http://localhost:8087/s/my-test-link`
- ✅ 点击链接可以访问并查看内容

---

### 2. ✅ 创建不带尾缀的剪贴板（默认行为）
**步骤：**
1. 在"内容"框输入：`另一个测试`
2. **留空"链接尾缀"框**
3. 点击"创建剪贴板"

**预期结果：**
- ✅ 创建成功
- ✅ 生成随机UUID作为ID
- ✅ 链接格式：`http://localhost:8087/s/a1b2c3d4-...`

---

### 3. ✅ 尝试使用重复的尾缀
**步骤：**
1. 在"内容"框输入：`重复测试`
2. 在"链接尾缀"框输入：`my-test-link` (与场景1相同)
3. 点击"创建剪贴板"

**预期结果：**
- ❌ 创建失败
- ✅ Toast显示错误：`此链接后缀已被使用，请选择其他后缀`
- ✅ HTTP状态码：409 Conflict

---

### 4. ✅ 尾缀空格处理
**步骤：**
1. 在"内容"框输入：`空格测试`
2. 在"链接尾缀"框输入：`  space-test  ` (前后有空格)
3. 点击"创建剪贴板"

**预期结果：**
- ✅ 创建成功
- ✅ 自动trim空格，实际ID为：`space-test`
- ✅ 链接：`http://localhost:8087/s/space-test`

---

### 5. ✅ 文件上传带自定义尾缀
**步骤：**
1. 选择类型：`文件`
2. 上传一个图片文件
3. 在"链接尾缀"框输入：`my-image`
4. 点击"创建剪贴板"

**预期结果：**
- ✅ 创建成功
- ✅ 链接：`http://localhost:8087/s/my-image`
- ✅ 访问链接可以下载或查看文件

---

## 🔍 数据库验证

创建几个clipboard后，检查数据库：

```bash
sqlite3 data/clipboard.db "SELECT id, type, content FROM ClipboardItem;"
```

**预期结果：**
```
my-test-link|TEXT|测试内容
a1b2c3d4-...|TEXT|另一个测试
space-test|TEXT|空格测试
my-image|FILE|NULL
```

---

## 🐛 已知问题检查

- [ ] 前端表单渲染正常
- [ ] 输入框placeholder显示："自定义链接后缀，留空则自动生成"
- [ ] 错误提示正确显示在toast中
- [ ] Next.js API和Rust API行为一致
- [ ] 链接点击后能正确访问内容

---

## 📊 测试结果记录

| 场景 | Next.js (3000) | Rust (8087) | 备注 |
|------|----------------|-------------|------|
| 带尾缀创建 | ⬜ 通过 / ⬜ 失败 | ⬜ 通过 / ⬜ 失败 |  |
| 无尾缀创建 | ⬜ 通过 / ⬜ 失败 | ⬜ 通过 / ⬜ 失败 |  |
| 重复尾缀报错 | ⬜ 通过 / ⬜ 失败 | ⬜ 通过 / ⬜ 失败 |  |
| 空格处理 | ⬜ 通过 / ⬜ 失败 | ⬜ 通过 / ⬜ 失败 |  |
| 文件上传 | ⬜ 通过 / ⬜ 失败 | ⬜ 通过 / ⬜ 失败 |  |

---

## ⚡ 快速测试命令

```bash
# 测试API端点（使用curl）
# 创建带尾缀
curl -X POST http://localhost:8087/api/clipboard \
  -F "content=测试内容" \
  -F "type=TEXT" \
  -F "suffix=test-link"

# 创建不带尾缀
curl -X POST http://localhost:8087/api/clipboard \
  -F "content=另一个测试" \
  -F "type=TEXT"

# 尝试重复尾缀（应返回409）
curl -X POST http://localhost:8087/api/clipboard \
  -F "content=重复测试" \
  -F "type=TEXT" \
  -F "suffix=test-link"
```

---

**测试完成后，请在此记录任何发现的问题！** 🎉
