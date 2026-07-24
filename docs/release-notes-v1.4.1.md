# v1.4.1 发布说明 — 发布包改用固定文件名

- 发布日期：2026-07-24
- 类型：打包与文档调整（**脚本逻辑零改动**）
- 上一版：[v1.4.0](./release-notes-v1.4.0.md)（新增禁用 / 恢复 IDM 更新提示）

## 为什么改

以前每发一版就往 `release/` 里多塞一个带版本号的压缩包：

```
release/IDM-Activation-Script-v1.3.zip
release/IDM-Activation-Script-v1.3.1.zip
release/IDM-Activation-Script-v1.3.3.zip
...
release/IDM-Activation-Script-v1.4.0.zip
```

带来两个实际问题：

1. **下载链接每次都要改**。README、`README.en.md`、`llms.txt`、FAQ 里的校验命令都写死了版本号，发版时漏改一处，用户点进去下到的就是旧包。v1.4.0 发布时就漏改了 README 末尾的"版本与维护"段。
2. **仓库里堆了一串没人会去点的历史压缩包**。它们和 GitHub Releases 页面的 Assets 完全重复。

## 改了什么

### 固定文件名

发布包统一叫 **`IDM-Activation-Script.zip`**（同名 `.sha256`），不带版本号：

- 下载链接一次写死，永远指向最新版：
  `https://github.com/tytsxai/IDM-Activation-Script-Chinese/raw/main/release/IDM-Activation-Script.zip`
- 版本号仍然有，只是不放在文件名里 —— 由 Git tag、GitHub Release 标题、`CHANGELOG.md` 和 `IAS.cmd` 启动时显示的 `iasver` 共同标识。解压后运行 `开始激活.cmd`，标题栏会写明当前版本。

### `release/` 只保留一份

仓库内不再冗余存放历史版本压缩包。**历史版本没有丢**，但入口按版本不同，如实说明：

- **v1.3.3 / v1.3.5 / v1.3.6 / v1.3.7 / v1.3.9 / v1.4.0** —— 对应 tag 的 [Release](https://github.com/tytsxai/IDM-Activation-Script-Chinese/releases) 页面 `Assets` 区可直接下载，长期有效；
- **v1.3.4 / v1.3.8** —— 当年就是纯文档版本，没有独立发布包（分别沿用 v1.3.3 / v1.3.7 的包）；
- **v1.3 / v1.3.1** —— 这两个版本**从未发过 GitHub Release**，包只存在于 `release/` 目录里，移除后仅能从 Git 历史取回：

  ```bash
  git show 669c2a8^:release/IDM-Activation-Script-v1.3.1.zip > IDM-Activation-Script-v1.3.1.zip
  ```

  （`669c2a8` 是移除这些文件的提交；`git log --all --diff-filter=D --name-only -- release/` 可列出全部被移除的文件。）这两个版本分别是 2025-12 和 2026-04 的产物，已落后多个大版本，保留取回路径只为不丢历史，不建议使用。

新增 `release/README.md` 把这个约定写在目录里，避免以后有人又往里堆版本号文件。

### 版本号对齐

`IAS.cmd` 的 `iasver` 由 `1.4.0` 升到 `1.4.1`，保证脚本自报版本、Git tag、Release 标题三者一致 —— 仓库以前出现过"文档版本 v1.3.4 / 运行时包 v1.3.3"这类容易让人误解的错位，这次不再制造新的错位。

## 没改什么

- **激活 / 冻结 / 重置 / 禁用更新 / 恢复更新的逻辑一行未动**，功能与 v1.4.0 完全一致；
- 菜单编号、参数（`/act` `/frz` `/res` `/noupd` `/reupd` `/silent` `/log=`）、退出码语义都不变；
- GBK 编码、CRLF 行尾、文件末尾空行等约束不变，CI 校验照旧。

## 升级建议

已经下载并在用 v1.4.0 的用户**无需重新下载**，功能完全一样。以后从 README 或 Releases 页面下载时，文件名会是 `IDM-Activation-Script.zip`。

校验命令（PowerShell）：

```powershell
Get-FileHash .\IDM-Activation-Script.zip -Algorithm SHA256
```

与 `IDM-Activation-Script.zip.sha256` 内的值比对一致后再解压使用。
