# 资产驱动恢复与收尾：U29 的条件操作参考

不是平行权威。只有恢复入口不明、绑定不符、索引失效或资产变更需要收尾时读取；规则以 BRAIN_PROTOCOL U29、项目当前原件及授权边界为准。源自 U4/U7 既有资产优先要求，2026-10-25 补足具体契约。

> **公开版说明**：本文件中的绝对路径已替换为占位符；`aorta_digital_twin` / `brain_core` 的代码、数据与门控仪器不随本仓发布，相关段落仅作适配示例。

## 跨项目通法

先声明目标绝对 root，再核 cwd 与宿主记忆绑定。cwd变了不代表记忆绑定随之变；错绑不写项目事实。已授权证据落目标项目目录即可，不要为绑定问题停掉全部独立工作。

恢复载荷只包含：主线/当前目标；权威原件路径；当前状态；缺口/下一步；被推翻结论和失败路线入口。恢复热读总预算≤6000字符，已加载规则不再重复读。目录只定位，任务输入必须回原件；检索库不能全文注入。

缓存要带 root、source paths+hash、生成器版本/hash、覆盖与验证状态。快照时间、mtime与条目数只是诊断信号。若验证范围未覆盖某源，不声称它是新鲜的。

有变更：先写原件与 produced_by/hash、正例/反例验证 → 现有扫描器 → 台账 → 检索索引 → 对显式 changed paths 逐项确认路径、指纹和状态 → brief与门控。禁止另造人工总账或仅统计件数；各步exit code非0就停止依赖动作。

当前/历史分层是索引投影，不是删原件。移出活跃层须保留原路径/hash、替代关系、原因和失败经验；没有消费者标未接入，不仅按年龄/新旧判无用。恢复不能复活已否证结论。

OFFLINE：源盘/位置不可达，记录依赖面，不能宣布文件丢失。MISSING：位置已核可达但原件不存在。STALE：指纹/生成器/源版本不一致。三者均拒绝相关依赖动作；修复后重新验证。不依赖该资产的授权工作可以继续。

补查范围：活跃目录→当前检索→必要时历史检索/限定路径。每批≤12命中，最多两批无新证据即报缺口；不盲扫其他项目/系统盘。普通概念问答、无资产变更解释不扫描、不写盘。

跨模型可恢复的前提是宿主确实加载项目入口或brain，不能保证每个模型执行。哈希门只证明其覆盖的完整性；资产语义、任务当前状态和模型行为另有验证。

## 本机适配：aorta_digital_twin

目标root固定为 `$AORTA_PROJECT`；无论当前cwd在哪，都用以下绝对脚本路径。不得因此获得修改该仓的授权。命令以宿主当前可用Python执行；PowerShell先设置TEMP/TMP/TMPDIR=`$TMP_ROOT\pytemp`、PYTHONIOENCODING=utf-8及PYTHONDONTWRITEBYTECODE=1。

恢复只读入口：

```text
python $AORTA_PROJECT/code/1363_brain_index.py --brief
python $AORTA_PROJECT/code/1363_brain_index.py --q keyword --limit 12
python $AORTA_PROJECT/code/1363_brain_index.py --history --q keyword --limit 12
```

`--brief`只读、拒绝陈旧；本机分配≤4000字符，与≤2000字符skill入口合计≤6000；它失败不自动重建。根`BRAIN.md`是≤8KB生成目录，不是手写事实本；`brain_index.json`是完整检索数据库，不当正文读。需要原始结论按命中路径回读。`8960_turn_boot.py`也接brief；AGENTS/CLAUDE顶部加载绝对入口。这里是接口契约，实际可用性以当轮命令输出为证，未部署接口不得假称实跑成功。

有资产变化且已获该项目写授权时，按序执行；每一步单独检查退出码：

```text
python $AORTA_PROJECT/code/8075_register_assets.py --changed FILE --changed ANOTHER_FILE
python $AORTA_PROJECT/code/1363_brain_index.py --rebuild
python $AORTA_PROJECT/code/1363_brain_index.py --brief
python $AORTA_PROJECT/code/1300_ultimate_protocol_gate.py
```

8075内部沿既有扫描→台账链，显式changed路径不可省；1363更新检索投影并核对这些原件。未通过扫描/台账/索引任何一步，不宣称该变更已完成资产收尾。协议门不是替代物。

## 本机适配：brain_core

目标root为 `$BRAIN_CORE`。本仓没有aorta资产管线，不能从当前cwd误跑另一项目扫描。按本仓README、BRAIN_PROTOCOL U29/§9、仓管skill源和审计证据定位任务；引用原件而非历史记忆。

改规则先备份/新版本，然后运行 `code/1300_protocol_gate.py --register`。继承只用 `code/0001_bootstrap_inherit.py`（先备份继承副本/凭据），不改aorta源。把BRAIN_SKILL_DIR指向`$BRAIN_CORE/skills/brain`，运行86的apply/verify；再运行87部署/verify及协议门。安装仅限已授权的brain目录，其他C路径不写。

提交由本轮用户/lead决定；未提交仍是§9待完成状态，任何机械门PASS都不能覆盖它。证据脚本记录显式changed paths、hash与消费者，报告放本仓docs；绑定未确认时不调项目记忆写工具。
