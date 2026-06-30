# 50 设计蛋白 Tunnel 3D 分析报告 — 分享指南

本目录提供 **3 种分享方式**,按"零依赖 → 公网链接"排序。**推荐先 A 轨(零依赖),再 B 轨(公网)**。

---

## 🥇 方式 A1:单文件离线版(对方双击即用,**首选**)

**文件**:`report_offline.html`(34.8 MB)
**特点**:所有 PDB + 3Dmol.js + 数据全部内嵌,**对方不需要任何网络或 Python 服务**

### 分享步骤

1. **压缩成 zip**:
   ```bash
   # Windows 资源管理器:右键 → 发送到 → 压缩(zipped)文件夹
   # 或 WSL:
   cd "/mnt/d/OneDrive/carnosine synthase/top5/analysis_tunnel_3d"
   zip report_offline.zip report_offline.html
   ```
2. **发送 zip**(34.8 MB,微信/钉钉/QQ 都能传):
   - 微信:文件传输助手 → 选 zip → 发送
   - 钉钉:发送文件 → 选 zip
   - 邮件:附件,34 MB 在 QQ 邮箱/网易邮箱都支持
   - U 盘:直接拷
3. **对方使用**:
   - 解压 zip
   - 双击 `report_offline.html`
   - 在默认浏览器(Edge/Chrome/Firefox)中打开
   - **5 个 tab 全部可用,3D 交互正常**

### 局限

- 文件 34.8 MB(微信/邮件可传,但大)
- 浏览器需支持现代 WebGL(2015 年后所有主流浏览器都支持)

---

## 🥈 方式 A2:完整目录包(对方本地起服务)

**文件**:整个 `analysis_tunnel_3d/` 目录(约 30 MB)
**特点**:用 `report.html`(83 KB,更轻量),需要对方本地起 HTTP 服务

### 分享步骤

1. **打包整个目录**:
   ```bash
   cd "/mnt/d/OneDrive/carnosine synthase/top5/analysis_tunnel_3d"
   # Windows:右键文件夹 → 压缩
   # WSL:
   zip -r report_full.zip . -x "*.pyc" "__pycache__/*"
   ```
2. **发送 zip**
3. **对方使用**(给他们的说明):
   ```
   解压到任意目录,然后:
   
   # 方式 1:Python(几乎所有电脑都有)
   cd 解压目录
   python -m http.server 9120
   
   # 方式 2:Node.js
   npx http-server -p 9120
   
   # 浏览器打开
   http://localhost:9120/report.html
   ```

### 优点 vs 局限

- ✅ 报告 HTML 本身只有 83 KB(更轻量)
- ✅ 可以改 HTML 后实时刷新
- ❌ 对方需要装 Python 或 Node(几乎都有)
- ❌ 多一步操作

---

## 🥉 方式 B:公网链接(任何人都能访问)

适合发文章、给企业或外部审稿人。需要部署到云端。

### 方案 B1:GitHub Pages(免费,推荐)

1. **创建 GitHub 仓库**(我可以帮你):
   - 仓库名:`carnosine-tunnel-report`
   - 设为 **Public**(GitHub Pages 公开访问要求)
2. **上传整个目录**:
   - 用 `git push` 或 GitHub Desktop
3. **启用 GitHub Pages**:
   - Settings → Pages → Source: `main` branch → Save
4. **访问 URL**:
   ```
   https://<你的 GitHub 用户名>.github.io/carnosine-tunnel-report/report.html
   ```

### 方案 B2:Netlify Drop(零配置)

1. 打开 https://app.netlify.com/drop
2. **拖拽**整个 `analysis_tunnel_3d/` 目录
3. 几秒后给你一个 `https://xxx.netlify.app/report.html` 链接
4. **可立即分享**

### 方案 B3:Vercel(类似 Netlify)

```bash
cd "/mnt/d/OneDrive/carnosine synthase/top5/analysis_tunnel_3d"
npx vercel
# 跟着提示走
```

### 方案 B4:自己的服务器

如果有公司服务器或自己的域名,直接上传到 nginx/apache 目录即可。

---

## 📋 方式选择决策树

```
问:对方会用什么设备看?
├─ Windows 电脑 / 同事 → 方式 A1(单文件双击)
├─ 任何设备(手机/平板) → 方式 B(公网链接)
└─ 团队多人协作 → 方式 A2(完整目录)+ 内网 HTTP 服务

问:对方会装 Python 吗?
├─ 不会 → 方式 A1 或 B
└─ 会 → 方式 A2(更灵活)

问:是否要长期公开?
├─ 是(论文/官网) → 方式 B1 GitHub Pages
└─ 否(临时分享) → 方式 A1
```

---

## 🎁 现成的产物(在 analysis_tunnel_3d/ 目录)

| 文件 | 大小 | 用途 |
|---|---|---|
| `report.html` | 83 KB | 主报告(需 HTTP 服务) |
| `report_offline.html` | 34.8 MB | **单文件离线版(首选)** |
| `index.json` | 17 KB | 数据索引 |
| `web_pdbs/` | 11.2 MB | 51 个 PDB(3Dmol.js 用) |
| `web_tunnels/` | 0.6 MB | 51 蛋白 tunnel 球链坐标 |
| `web_sites.json` | 71 KB | 51 蛋白 8 活性位点坐标 |
| `renders/` | 12.3 MB | 51 张静态 3D 缩略图 |
| `per_fold/fold_01..50/` | 50×5 文件 | 每蛋白: 3 .pml + 1 csv + 1 json |
| `top4/` | 12 个 .pml | Top 4 候选 PyMOL 脚本 |
| `HOW_TO_SHARE.md` | 本文件 | 分享指南 |
| `README.md` | 6 KB | 报告使用说明 |

**总大小**:~30 MB(可整体压缩成一个 zip 给对方)

---

## 🔧 我可以帮你做什么

1. **打包成 zip**:告诉我目标目录,我直接生成
2. **部署到 GitHub Pages**:需要你提供 GitHub 账号(我可以生成 token,你粘贴)
3. **部署到 Netlify**:需要 Netlify 账号,或我帮你用临时账号
4. **写自定义分享说明**:针对你具体分享对象(导师/合作者/企业)

---

## ⚠️ 注意事项

- 报告包含 50 个 AlphaFold 设计的**预测结构**,**非实验数据**
- 适合作为**结构筛选/讨论**用,不能替代湿实验
- 如需在论文中使用,建议引用 CAVER 3.0:
  > Chovancova E, et al. (2012) CAVER 3.0: A Tool for the Analysis of Transport Pathways
  > in Dynamic Protein Structures. PLoS Comput Biol 8: e1002708.
