# 🚀 部署指南 - 烹饪图案生成器

## 📋 部署清单

### Vercel 面板配置
在 Vercel 部署时，请填写以下字段：

| 字段 | 值 | 说明 |
|------|-----|------|
| **Framework Preset** | `Other` | 纯静态HTML项目 |
| **Build Command** | `echo 'Static site - no build required'` | 无需构建 |
| **Output Directory** | `.` | 根目录 |
| **Install Command** | `npm install` | 安装依赖 |

### 本地验证命令

```bash
# 1. 安装依赖
npm install

# 2. 本地开发服务器
npm run dev
# 或
python3 -m http.server 8000

# 3. 访问应用
open http://localhost:8000/demo.html

# 4. 部署到 Vercel
npm run deploy
# 或
vercel --prod
```

## 🔧 常见问题解决

### 1. 刷新页面出现 404 错误
**问题**：直接访问 `/step2` 等路由时出现 404
**解决**：已配置 `vercel.json` 的 SPA 回退路由，所有未匹配的路由都会重定向到 `demo.html`

### 2. 页面白屏
**可能原因**：
- JavaScript 错误
- 资源加载失败
- 浏览器兼容性问题

**排查步骤**：
```bash
# 检查控制台错误
# 1. 打开浏览器开发者工具 (F12)
# 2. 查看 Console 标签页的错误信息
# 3. 查看 Network 标签页的资源加载状态

# 本地测试
npm run dev
# 访问 http://localhost:8000/demo.html
```

### 3. 图片资源 404
**问题**：图片素材无法加载
**解决**：
- 确保 `图片素材/` 目录存在
- 检查图片文件名是否正确
- 验证相对路径引用

### 4. 跨域问题
**问题**：本地开发时出现 CORS 错误
**解决**：
```bash
# 使用本地服务器而不是直接打开文件
npm run dev
# 或
python3 -m http.server 8000
```

### 4.1. 下载功能失败问题
**问题**：点击下载按钮出现"下载失败，请重试！"错误
**原因**：
1. 直接打开HTML文件（file://协议）导致跨域限制
2. 图片资源未正确加载
3. Canvas内容为空

**解决方案**：
```bash
# 1. 必须使用HTTP服务器访问
python3 -m http.server 8000
# 然后访问 http://localhost:8000/demo.html

# 2. 检查浏览器控制台错误信息
# 按F12打开开发者工具，查看Console标签页

# 3. 确保图片资源存在
ls 图片素材/
# 应该看到所有食材图片文件

# 4. 如果仍有问题，检查网络请求
# 在Network标签页查看图片是否成功加载
```

**常见错误信息**：
- `Canvas内容为空，无法生成图片` → 图片未正确加载
- `SecurityError: Failed to execute 'toDataURL'` → 跨域问题，需要使用HTTP服务器

### 5. 图案下载功能
**功能**：用户完成图案创作后可以下载PNG格式的图案文件
**实现**：使用Canvas API的`toDataURL()`方法生成高质量PNG图片
**特点**：
- 高分辨率输出（2倍原始尺寸）
- 自动生成带时间戳的文件名
- 浏览器自动下载，无需额外依赖

**重要提示**：下载功能必须在HTTP服务器环境下运行，不能直接打开HTML文件！

### 6. 嵌入 Framer 的 postMessage 自适应高度
**配置**：在 Framer 中嵌入时，需要处理高度自适应

```javascript
// 在 demo.html 中添加以下代码（如果尚未添加）
window.addEventListener('message', function(event) {
  if (event.data.type === 'resize') {
    // 调整页面高度
    document.body.style.height = event.data.height + 'px';
  }
});

// 向父窗口发送高度信息
function notifyHeight() {
  if (window.parent !== window) {
    window.parent.postMessage({
      type: 'height',
      height: document.body.scrollHeight
    }, '*');
  }
}

// 页面加载完成后通知高度
window.addEventListener('load', notifyHeight);
window.addEventListener('resize', notifyHeight);
```

## 📁 项目结构

```
├── demo.html              # 主应用入口（包含所有功能）
├── style.css              # 样式文件
├── package.json           # 项目配置
├── vercel.json            # Vercel 部署配置
├── README_Deploy.md       # 部署指南
├── .gitignore             # Git忽略文件
└── 图片素材/              # 静态图片资源
    ├── 米饭.png
    ├── 面条.png
    ├── 豆腐.png
    ├── 洋葱.png
    ├── 番茄.png
    ├── 白菜.png
    ├── 豆芽.png
    ├── 辣椒.png
    └── 鸡蛋.png
```

## 🌐 部署后的访问地址

部署成功后，您可以通过以下方式访问：

1. **Vercel 提供的域名**：`https://your-project-name.vercel.app`
2. **自定义域名**：在 Vercel 面板中配置

## 🔄 更新部署

```bash
# 1. 修改代码后
git add .
git commit -m "更新功能"

# 2. 推送到 GitHub
git push origin main

# 3. Vercel 会自动重新部署
# 或手动部署
vercel --prod
```

## 📱 移动端适配

项目已包含响应式设计，支持：
- 桌面端：完整功能
- 平板端：自适应布局
- 移动端：触摸友好的界面

## 🎨 自定义配置

### 修改主入口页面
如需修改默认入口页面，编辑 `vercel.json`：
```json
{
  "routes": [
    {
      "src": "/",
      "dest": "/your-main-page.html"
    }
  ]
}
```

### 添加自定义域名
1. 在 Vercel 面板中进入项目设置
2. 添加自定义域名
3. 配置 DNS 记录

## 🆘 技术支持

如遇到部署问题，请检查：
1. Vercel 部署日志
2. 浏览器控制台错误
3. 网络连接状态
4. 文件路径是否正确

---

**部署成功标志**：能够正常访问 `demo.html` 并看到烹饪图案生成器界面 🎉
