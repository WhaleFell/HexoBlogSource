---

title: GitHub Workflow 工作流使用笔记
date: 2022-03-29 20:51:48
updated: 2022-03-29 20:51:48
categories: GitHub
tags: [GitHub, Workflow]
description: GitHub Workflow 工作流使用笔记

---

# Github-workflow

下面是格式化后的 YAML 文件：

```yaml
name: commit HexoBlogSource (Obsidian update)

on:
  push:
    branches:
      - main

jobs:
  submit:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        
      - name: git config
        run: |
          git config --local user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git config --local user.name "github-actions[bot]"
          git commit -a -m "Add changes"

      - name: New Branch to pull
        run: |
          git checkout -b temp-branch
          git add .
          git commit -m "Obsidian Update"
          

      - name: Push to target repository
        uses: ad-m/github-push-action@v0.5.0
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: main
          directory: /source/__post/
          repository: WhaleFell/HexoBlogSource
```

我移除了缩进错误，并确保所有步骤的格式正确。请注意，我没有更改步骤的顺序或内容，只是对整个 YAML 文件进行了格式化。