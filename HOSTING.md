# 版本清单托管方案 (HOSTING)

> 配套 skill：`social-science-visualization-prompt` · 远程版本自检机制
> 仓库：https://github.com/skura55/draw_skill （public · 默认分支 `main`）

## 一、远程 version.json 托管方案

**单一真相源**：用仓库 `main` 分支上的 `references/VERSION.json` 同时充当——
- 已安装副本的「本地版本清单」
- 远程校验用的「最新版本清单」

发布新版只需改这一个文件并 push，所有已安装副本下次启动步骤0 即自动检测到新版本。无需单独再发 manifest 文件，远程不可达时步骤0 静默降级为本地展示。

**远程清单 URL（已确定，jsDelivr CDN）**：
```
https://cdn.jsdelivr.net/gh/skura55/draw_skill@main/references/VERSION.json
```
备选直连（即时生效，国内可能不稳）：`https://raw.githubusercontent.com/skura55/draw_skill/main/references/VERSION.json`

## 二、⚠️ 当前仓库状态（需先补传文件）

经核验，repo `main` 分支当前是**旧版**：
- `SKILL.md` = 9172 字节（原始版，**没有步骤0版本自检、没有 version 字段**）
- `references/` 只有 `prompt1.txt` + `prompt2.txt`，**没有 `VERSION.json`**

本地（`C:\Users\35239\.workbuddy\skills\social-science-visualization-prompt`）已是改版后状态，且 `remote_manifest_url` 已填好。

### 需上传到 repo `main` 的文件清单

| 本地文件 | repo 目标路径 | 操作 | 说明 |
|---|---|---|---|
| `SKILL.md` | `SKILL.md` | **覆盖** | 12282 字节，含步骤0 + frontmatter(version/updated_at/remote_manifest_url) |
| `references/VERSION.json` | `references/VERSION.json` | **新增** | 含 version 1.1.0 + changelog + remote_manifest_url |
| `HOSTING.md` | `HOSTING.md` | 可选新增 | 本文档 |

> `prompt1.txt` / `prompt2.txt` / `assets/` repo 上已有且未变，无需重传。

## 三、jsDelivr CDN 配置流程（你自己上传）

1. **上传文件**：把上表两个文件传到 repo `main` 分支
   - 方式 A（网页）：在 GitHub 仓库点 `Add file` → `Upload files`，拖入 `SKILL.md` 和 `references/VERSION.json`（注意 VERSION.json 放进 `references/` 目录，网页上传时先进入 references 文件夹再传），commit
   - 方式 B（git）：
     ```bash
     git clone https://github.com/skura55/draw_skill.git
     cd draw_skill
     # 用本地改版后的 SKILL.md 覆盖；把 references/VERSION.json 拷进 references/
     git add SKILL.md references/VERSION.json
     git commit -m "feat: 版本自检步骤0 + VERSION.json 清单"
     git push
     ```
2. **验证远程清单可达**：浏览器打开
   ```
   https://cdn.jsdelivr.net/gh/skura55/draw_skill@main/references/VERSION.json
   ```
   应直接返回 JSON（含 `"version": "1.1.0"`）。若 404：检查路径大小写 / 分支名 / 文件是否真传上去了。
3. **（首次或更新后）刷新 CDN 缓存**：浏览器访问一次
   ```
   https://purge.jsdelivr.net/gh/skura55/draw_skill@main/references/VERSION.json
   ```
4. **本地无需再改**：`references/VERSION.json` 与 `SKILL.md` frontmatter 的 `remote_manifest_url` 我已填好上述 jsDelivr URL。
5. **联调验证**：调用一次 skill，步骤0 应输出类似
   ```
   [技能版本] v1.1.0 · 更新于 2026-07-14 · 已是最新版本
   ```

## 四、发布新版本速查

```bash
# 1. 改 references/VERSION.json：bump version / updated_at / 加 changelog 条目
# 2. push
git add references/VERSION.json
git commit -m "release vX.Y.Z"
git push
# 3. (可选) purge CDN：浏览器访问 purge.jsdelivr.net/gh/skura55/draw_skill@main/references/VERSION.json
```
所有已安装副本下次启动步骤0 即检测到新版本并提示 changelog。

## 五、备注

- jsDelivr 对 `@main` 有分钟级缓存，purge 可立即生效
- 仓库为 public，CDN 无需鉴权
- 远程校验失败时步骤0 自动降级为本地版本展示，绝不阻断五步主流程
- 远程 VERSION.json 的 `remote_manifest_url` 字段为自引用（指向自身），校验逻辑只读 `version/updated_at/changelog`，忽略该字段，无害
