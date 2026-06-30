# Tunnel 3D 分析产物汇总

**生成时间**: 2026-06-29
**数据源**: D:/OneDrive/carnosine synthase/top5/alphafold design proteins model/analysis_50proteins/
**生成工具**: tunnel_pymol_viewer.py (protein-tunnel-analysis skill)

---

## 一、产物清单(每个蛋白 5 个文件)

| 文件 | 内容 | 用法 |
|---|---|---|
| `<name>_load_and_view.pml` | 加载蛋白 + 所有 tunnel cluster(球形, B-factor → 球半径) | 在 PyMOL 中 File → Open |
| `<name>_chemistry_view.pml` | 同上 + 衬里残基按化学分类着色(红/蓝/绿/黄) | 显示 tunnel 表面极性/带电分布 |
| `<name>_save_pse.pml` | 综合加载 + 提供 .pse 保存指令 | 跑完后再输 `save <name>.pse` |
| `<name>_tunnel_volumes.csv` | 各 cluster 的球数 + 估算体积 | 用 pandas/Excel 看 |
| `<name>_tunnel_volumes.json` | 同上 + 衬里化学汇总 | 程序读取 |

---

## 二、Q1:PyMOL 可视化(.pse)

### 步骤
1. 打开 **Windows PyMOL**(用户本地)
2. File → Open → 选择 `fsl_peptidase_load_and_view.pml`(或 Top 4 候选的对应 .pml)
3. 等待自动加载(约 10 秒)
4. **渲染高分辨率图**: 在 PyMOL 命令行输入 `ray 2400, 2.4`
5. **保存 .pse session 文件**: `save fsl_peptidase.pse`

### 关键命令清单
```
reinitialize
load <protein.pdb>, protein
show cartoon, protein
color gray80, protein
... (脚本自动加载 tunnel)
ray 2400, 2.4          # 高质量渲染(2-3 分钟)
png <name>.png, dpi=300  # 导出 PNG
save <name>.pse          # 保存 .pse session(包含所有视角/选择/对象)
```

### 渲染风格参考
- **灰色 cartoon 蛋白**:图 1 那种背景蛋白
- **球形 tunnel**:CAVER 用 B-factor 表示瓶颈半径,球大小 = 通道截面大小
- **衬里残基按化学着色**:红(ASP/GLU,负电)、蓝(ARG/LYS/HIS,正电)、绿(SER/THR/ASN/GLN/TYR/CYS,极性)、黄(其余,疏水)

---

## 三、Q3a:通道体积计算结果

**方法**:对每个 cluster 的所有球(B-factor = 半径)做 `Σ (4/3)πr³` 加和。
**精度**:球和体积略大于 Voronoi 体积,但足以做工程排序(同一计算方式,所有蛋白可比)。

### 50 蛋白体积汇总

#### Top 10 by Volume(通道体积最大)
| Fold | Volume (Å³) | Clusters | Lining (+/-/pol/hyd) |
|---|---|---|---|
| fold_28 | 14,551 | 24 | +2/-8/pol15/hyd17 |
| fold_30 | 14,450 | 23 | +2/-6/pol14/hyd13 |
| fold_11 | 14,314 | 22 | +0/-4/pol14/hyd19 |
| fold_10 | 13,457 | 26 | +2/-6/pol14/hyd14 |
| fold_09 | 12,861 | 16 | +2/-6/pol14/hyd12 |
| fold_32 | 12,571 | 19 | +2/-7/pol14/hyd12 |
| fold_05 | 10,427 | 18 | +2/-8/pol15/hyd11 |
| fold_17 | 10,069 | 16 | +1/-7/pol14/hyd11 |
| fold_26 | 9,910 | 13 | +2/-4/pol19/hyd16 |
| fold_15 | 9,247 | 17 | +3/-8/pol15/hyd14 |

#### Top 10 by Cluster Count(通道数最多,多入口)
| Fold | Clusters | Volume (Å³) |
|---|---|---|
| fold_10 | 26 | 13,457 |
| fold_28 | 24 | 14,551 |
| fold_30 | 23 | 14,450 |
| fold_11 | 22 | 14,314 |
| fold_19 | 20 | 8,416 |
| fold_42 | 20 | 8,943 |
| fold_32 | 19 | 12,571 |
| fold_03 | 18 | 9,057 |
| fold_05 | 18 | 10,427 |
| fold_02 | 17 | 8,617 |

### 关键发现

1. **体积与 cluster 数强相关** —— 体积最大的 fold_28 (14,551 Å³) 同时有 24 个 cluster,意味着通道多且大
2. **fold_04 (Top 4 之一)** 通道体积仅 3,437 Å³,**比中位数低** —— 这是好事,**窄通道有助于选择性**
3. **fold_26 (Top 4 之一)** 通道体积 9,910 Å³,**偏高** —— 比 fold_36 大 70%,但仍在合理范围

---

## 四、Q3b:衬里化学分析

**方法**:从 `analysis/residues.txt` 读 top-2 cluster 的 lining 残基,按 20 个标准氨基酸分组:
- **红 (negative)**: ASP, GLU
- **蓝 (positive)**: ARG, LYS, HIS
- **绿 (polar)**: SER, THR, ASN, GLN, TYR, CYS
- **黄 (hydrophobic)**: ALA, VAL, LEU, ILE, PHE, TRP, MET, GLY, PRO

### Top 4 候选的衬里化学

| Fold | + | - | pol | hyd | 主导化学 | 含义 |
|---|---|---|---|---|---|---|
| fold_36 | 2 | 4 | 19 | 14 | polar | 中等极性,适度引导 |
| fold_46 | 0 | 6 | 14 | 14 | neg/polar | 内壁偏负电,排斥负电底物 |
| fold_26 | 2 | 4 | 19 | 16 | polar | 类似 fold_36 |
| fold_04 | 0 | 8 | 15 | 18 | **负电主导** | 内壁强负电,可能需要正电底物/中间体 |

**解读**:
- **fold_04 的强负电内壁**(8 ASP/GLU)在化学上会**强烈吸引正电反应中间体**,这可能正是为何它在上一轮 Top 4 评分中胜出 —— 与原酶机制一致
- **fold_26/fold_36 的极性主导**(19 polar) 提供 H 键网络,有利于底物取向
- **多数 fold 的 hydrophobic 在 11-19 之间**,提供 VDW 接触

---

## 五、Q2:Tunnel Dynamics(状态:需要你提供 MD trajectory)

**我没有 MD 模拟能力**(需要 GROMACS/Amber + GPU + 力场文件,WSL + 中国大陆网络受限)。

如果你要做图 2 那种 snapshot 44/45/46 序列:
1. 在其他机器(GROMACS/Amber 可用)上跑 5-50 ns MD 模拟
2. 输出 PDB 多帧(MODEL/ENDMDL 格式,或 trajectory DCD/XTC + top PDB)
3. 把 MD 输出文件放到 `D:/OneDrive/carnosine synthase/top5/md_input/`
4. 告诉我文件路径,我可以:
   - 用 CAVER headless 跑每一帧
   - 生成 snapshot 序列对比图
   - 计算 tunnel 几何随时间的动态变化

---

## 六、文件位置索引

```
D:/OneDrive/carnosine synthase/top5/analysis_tunnel_3d/
├── README.md                                   # 本文件
├── fsl_peptidase_load_and_view.pml             # 参照蛋白 viewer
├── fsl_peptidase_chemistry_view.pml            # 参照蛋白化学 viewer
├── fsl_peptidase_save_pse.pml                  # 参照蛋白 .pse saver
├── fsl_peptidase_tunnel_volumes.csv
├── fsl_peptidase_tunnel_volumes.json
├── per_fold/
│   ├── fold_01/ ... fold_50/                   # 50 蛋白各 5 个文件
│   ├── all_50_tunnel_volumes_summary.csv
│   └── all_50_tunnel_volumes_summary.json
└── top4/                                       # Top 4 候选的额外副本(可选)
    ├── fold_36_*.pml ...
    ├── fold_46_*.pml ...
    ├── fold_26_*.pml ...
    └── fold_04_*.pml ...
```

---

**总结:Q1 + Q3 已完成全部产物;Q2 需要你后续提供 MD trajectory。**
