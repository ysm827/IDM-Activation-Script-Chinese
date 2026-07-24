# release/ 目录约定

本目录**只保留一份最新的运行时发布包**：

| 文件 | 说明 |
| --- | --- |
| `IDM-Activation-Script.zip` | 最新版发布包（固定文件名，不带版本号） |
| `IDM-Activation-Script.zip.sha256` | 对应的 SHA256 校验值 |

## 为什么不带版本号

文件名固定后，README / `README.en.md` / `llms.txt` 里的下载链接只需写一次，永远指向最新版，不会因为发版漏改而把用户导到旧包。

版本号并没有消失，由这几处标识：

- Git tag（如 `v1.4.1`）与对应的 [GitHub Release](https://github.com/tytsxai/IDM-Activation-Script-Chinese/releases) 标题；
- [`CHANGELOG.md`](../CHANGELOG.md) 与 `docs/release-notes-*.md`；
- `IAS.cmd` 里的 `iasver`，脚本运行时会显示在窗口标题和菜单上方。

## 历史版本去哪儿了

历史版本的压缩包不再冗余存放在仓库里。**各版本实际能从哪里拿到，按版本区分**：

| 版本 | 下载入口 |
| --- | --- |
| v1.3.3 / v1.3.5 / v1.3.6 / v1.3.7 / v1.3.9 / v1.4.0 | 对应 tag 的 [Release](https://github.com/tytsxai/IDM-Activation-Script-Chinese/releases) 页面 `Assets` 区，长期有效 |
| v1.3.4 | 纯文档版本，当时就没有独立发布包，沿用 v1.3.3 的包 |
| v1.3.8 | 纯文档版本，Release 里挂的是 v1.3.7 的包 |
| **v1.3 / v1.3.1** | **从未发过 GitHub Release**，当初只存在于本目录；现在只能从 Git 历史取回（见下） |

从 Git 历史取回（以 v1.3.1 为例，`669c2a8` 是移除这些文件的提交）：

```bash
git show 669c2a8^:release/IDM-Activation-Script-v1.3.1.zip > IDM-Activation-Script-v1.3.1.zip
```

列出所有被移除的文件：

```bash
git log --all --diff-filter=D --name-only -- release/
```

> 说明：v1.3 与 v1.3.1 分别是 2025-12 和 2026-04 的版本，已落后当前版本多个大版本，**不建议使用**，保留取回路径只是为了不丢历史。

## 发版时怎么更新

见 [`docs/maintenance-checklist.md`](../docs/maintenance-checklist.md)。要点：重新打包覆盖 `IDM-Activation-Script.zip`，重算并覆盖 `.sha256`，然后把同一个包作为 Asset 上传到对应 tag 的 GitHub Release。
