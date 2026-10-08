# 同类品种分析工具 · 前列腺癌版（离线网页版）

基于拜耳前列腺癌（AR 抑制剂）销售数据，对 7 个同类品种（**瑞维鲁胺/艾瑞恩（本品）**、醋酸阿比特龙×2、恩扎卢胺×2(普来坦/安可坦)、阿帕他胺、达罗他胺）做多维度同类品种分析：本品 vs 同类对比、患者去重 DOT、医院/科室/医生分布、品牌份额与趋势，以及多维视角筛选。

**纯前端离线运行，数据不出本机。** 工具通过"上传文件"读取你自己的底表，原始数据与任何敏感信息都不随代码分发。

> 本工具由皮科/风湿版（`competitor-analysis-tool/`）复制改造而来，是**独立分支**，字段口径与适应症体系已针对前列腺癌重写。

---

## 与皮科版的关键差异

| 项 | 皮科版 | 前列腺癌版（本工具） |
|---|---|---|
| 焦点品种（本品） | 乌帕替尼(瑞福) | 瑞维鲁胺(艾瑞恩) |
| 销量 / DOT 分子 | `转化后数量` | `数量`(盒/粒) |
| 去重 ID 列 | `oneid` | `ONEID`(全大写) |
| 适应症大类 | 皮科/消化/关节/其他 | 整体 / 前列腺癌（全部归前列腺癌） |
| 科室体系 | 皮肤科/风湿免疫科 | 泌尿外科 / 肿瘤科 |

---

## 文件结构

| 文件 | 作用 |
|---|---|
| `index.html` | 主程序（完整源码版），依赖同目录 3 个 JS 库，双击离线运行 |
| `single-file.html` | 已内联全部库的单文件版，**零依赖**，可直接当 GitHub Pages 首页 |
| `xlsx.full.min.js` | SheetJS，解析底表 Excel |
| `chart.umd.min.js` | Chart.js，绘制图表 |
| `indication_map_pc.js` | 前列腺癌适应症映射（IND_MAP，64 条，类别全=前列腺癌），网页运行必需 |
| `qrcode.min.js` | 分享版二维码生成 |
| `build_single_pc.js` | 把 `index.html` 打包成【离线全内联单文件版】（`single-file.html`） |
| `generate_pc_map.py` | 换新底表时抽取全部原始适应症写法、重生成 `indication_map_pc.js` |
| `validate_pc.py` | 校验字段解析 + 适应症 100% 覆盖 |
| `doctor_merge_map.json` | 医生名同音合并字典（四川 DTP 专用，对 PC 数据基本不命中，无害） |
| `serve.js` / `serve.bat` | 本地静态服务（可选） |
| `HANDOFF.md` | 项目交接文档（背景 / 文件地图 / 踩坑 / 命令速查） |

---

## 本地使用

1. 把本仓库克隆 / 下载到本地。
2. 双击 `index.html`（或 `single-file.html`）即可打开。
3. 在页面中上传前列腺癌底表 Excel（需包含列：销售时间、商品名称、适应症、数量、ONEID/会员号、医疗单位、处方科室、处方医生、开票抬头、收款方式等）。
4. 在「焦点品种」下拉选本品（瑞维鲁胺/艾瑞恩），即可看品牌份额、趋势、医院/医生覆盖、城市分布。

> 单文件版不需要任何配套文件，最方便分发；完整源码版便于二次开发。

---

## 部署到 GitHub Pages（已上线）

在线地址：**https://Enzoo-jpg.github.io/competitor-analysis-pc/**

更新部署（用户本机无 git CLI，统一走 **GitHub Desktop**）：

1. GitHub 网页建仓库 `competitor-analysis-pc`（Public，不勾 README）。
2. GitHub Desktop → File → Add local repository → 选 `github-deploy-pc/` → Add。
3. 点 **Publish branch**（仓库已存在则直接 Push origin）。
4. 网页仓库 → Settings → Pages → Source 选 `main` / `(root)` → Save。
5. 等 1~2 分钟访问上面网址。

> 改了 `index.html` 后，先跑 `node build_single_pc.js` 重生成 `single-file.html`，再把最新的 `single-file.html` 复制到 `github-deploy-pc/index.html` 后推送。

---

## 二次开发

- **重新生成单文件版**（改了 `index.html` 后必跑）：
  ```bash
  cd competitor-analysis-pc
  node build_single_pc.js
  ```
  产物为 `single-file.html`。

- **换新底表重生成适应症映射**：
  ```bash
  python generate_pc_map.py
  python validate_pc.py
  ```

---

## 数据隐私

- 本仓库**不含任何原始销售数据或患者明细**。
- 网页与导出均支持脱敏：医生名可设为"字母代号"或"保留姓氏"；适应症/医院/科室为聚合统计维度。
- 分析所需底表由使用者在本地自行上传，不上传任何服务器。

---

## 已知限制

- "保存到本地分享页"按钮依赖 `serve.js`（localhost 接口），纯静态托管（如 GitHub Pages）下该按钮不可用，但核心分析与导出不受影响。
- 适应症当前统一归「前列腺癌」大类（不分治疗线）。如需细分 mHSPC/mCRPC/nmCRPC/骨转移，需在源数据"适应症"列补写亚型后重跑 `generate_pc_map.py`。
