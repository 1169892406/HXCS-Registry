# HXCS Registry — HOMEX 社区插件注册表

本仓库只承担**一个**角色：HXCS 插件 `catalog_list` 拉取的注册表。

| 文件 | 作用 |
|---|---|
| `index.json` | 插件清单（版本 / 下载地址 / sha256 / 权限） |
| `plugins/<slug>/releases/*.hpkg` | 各插件的签名制品 |

## 为什么要独立成库

原注册表与 HXCS 插件源码放在同一个仓库（`HXCS`）。发一个插件版本就得动源码仓库，
而源码仓库的推送权限、CI、发布节奏都与"注册表该不该更新"无关 —— 两件事绑在一起，
结果是**注册表更新被源码流程拖住**。

拆开后：

- 发插件版本 ⇒ 只改本仓库（`index.json` + 一个 `.hpkg`）
- 改 HXCS 源码 ⇒ 只动源码仓库
- 客户端只需把 `registry_url` 指向本仓库

## 客户端地址

```
https://raw.githubusercontent.com/1169892406/HXCS-Registry/main
```

指向后在 HXCS 配置页把「注册表地址」改成这一行即可（`catalog_list` 会自动拼
`/index.json`）。

## 更新流程（人工）

1. 把新 `.hpkg` 放到 `plugins/<slug>/releases/`
2. 改 `index.json` 里该条目的 `version` / `package_url` / `package_sha256`
3. 更新顶层 `updated_at`
4. 删掉同 slug 的旧版本包（`catalog_fetch` 只认 `package_url`，留着不会被下载，
   但会让仓库体积一直涨）
5. 提交推送

`package_sha256` 必须是**实际文件**的 sha256，客户端校验不过会拒装。
