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

历史版本的压缩包不再冗余存放在仓库里，可以从两个地方获取：

1. **[GitHub Releases](https://github.com/tytsxai/IDM-Activation-Script-Chinese/releases)** —— 每个 tag 页面的 `Assets` 区都挂着当时发布的包，长期有效，这是推荐入口；
2. **Git 历史** —— `git log --all --diff-filter=D -- release/` 可以列出被移除的文件，`git checkout <commit>^ -- <路径>` 取回。

## 发版时怎么更新

见 [`docs/maintenance-checklist.md`](../docs/maintenance-checklist.md)。要点：重新打包覆盖 `IDM-Activation-Script.zip`，重算并覆盖 `.sha256`，然后把同一个包作为 Asset 上传到对应 tag 的 GitHub Release。
