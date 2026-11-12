# Changelog

## 0.7.1 · 2026-11-12 · 发布闸口

### 识图硬链路
- 标准链路固定为 `DWG/DXF/PDF → lis-skill-bus → cad-file-reader → cad-contract/v1 → lis-skill-bus → site-layout-plan`；本技能不打开、不解析 CAD/PDF，也不自带 reader。
- `prepare_site.py` 读取前拒绝 `.dwg/.dxf/.pdf`，只收 `cad-site-geometry/v1 + contract.contract_version=cad-contract/v1` 的 reader 契约；`crane_check.py` 只收带 `contract_id/source_sha256` 的 `site-handoff/v1`。
- 新增 `tests/test_reader_gate.py`（6 项）：直读 DXF、非 reader JSON、旧契约版本、缺 contract 来源均拒绝，合法 reader 契约通过；无 `CAD_ALLOW_DIRECT` 逃生口。
- 规则 26「办公区靠近主出入口」保持经验提示（`manual`），50 m 不设硬性 fail；自动布置仍可能超距，必须人工调整。
- 发布物排除 `work/`、真实图纸与项目证据；manifest 增加 `requires_skills` 与 `reader_contract` 声明。

## 0.7.0 · 2026-11-11

### 三区分类与生活区
- 办公区、员工宿舍区、工人生活区分开建模；工人生活区内置食堂、卫生间/淋浴、晾晒区三个子块，子块声明 `parent_id` 且不越出父包络。
- 规则新增生活区专项：与作业面分离、道路/消防疏散、配套完整性、办公区靠近主出入口；场地夹逼降级时写 `size_degraded/size_note`。

### 标准块与设施形象化 v2
- 7 类设施（塔吊/施工电梯/地磅/洗车台/三区）形象化 v2；DXF BLOCK 与 PNG 图例同源。
- 标准块路线定为纯 Python 自绘参数化图块（`site_layout/symbols.py`），零第三方 DWG/DXF 图块；图块只表达类型，不表达厂家真实尺寸、基础与工况。

### 三阶段与出图
- 土石方/主体/装饰三阶段分别建模、校核、出图；26 条临建规则 + 道路 R1~R4 + 周边硬/软约束 + 六视角审查。
- R12「看图版」导出；图签始终带 `DRAFT`，`final_check=false`。

### 验收（本机，未发布）
- 真实总图回归：规则 fail=0，selfcheck 12/0/0，SHA 49 项一致（s3）。
- 联合验收：本机全量测试 16 个文件全部 OK；样例代号 R09 的图签 OCR/CAD 对照在本机完成，发布包不含原图与项目字段。
- 边界：自动设施为 `candidate`；不替代审批、签字、消防/报建。
