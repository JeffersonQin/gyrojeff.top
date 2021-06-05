---
allowComment: true
allowFeed: true
allowPing: true
authorId: 1
categories: [记录]
created: '2020-11-30 17:34:00'
fields: {customSummary: '', noThumbInfoStyle: default, outdatedNotice: 'no', reprint: standard,
  thumb: 'https://cdn.jsdelivr.net/gh/JeffersonQin/blog-asset@latest/usr/uploads/bg/69.png',
  thumbChoice: default, thumbDesc: '', thumbSmall: '', thumbStyle: default}
modified: '2021-01-02 10:57:57'
parent: 0
password: ''
slug: '69'
status: publish
tags: [Blog, CSS, HTML, Hexo]
template: ''
title: Hexo博客的管理方法
type: post
---
# Preface

由于过去的Hexo博客被自己不断魔改，功能日趋复杂，所以根据自己的改动写下了这篇博客来告诉自己如何维护（笑。大家有兴趣完Hexo的话也可以参考一下我的魔改方法。Hexo站：https://hexo.gyrojeff.moe

融合的功能：

- 主题魔改
- 自动同步cnblogs
- 自动GitHub备份源文件
- 启用Submodule进行管理
- config在private repo中备份

下面是正文：

---

# Personal Blog - hexo.gyrojeff.moe

Personal blog of Haoyun Qin. Powered by `Hexo` & `Next`

# Maintaining

## Project Tree

```
===================================================================
hexo.gyrojeff.moe (Private)
├── hexo-theme-next (Submodule, Private)
│       Description: Modified theme of NeXT.
│       Directory: ./themes/next/
├── hexo-cnblogs-sync (Submodule, Public)
│   |   Description: Scripts for syncing service to cnblogs.
│   │   Directory: ./scripts_cnblogs/
│   ├── config (Folder)
│   │   ├── blog_config.json (File, Ignored)
│   │   │       Description: Configuration file of cnblogs account.
│   │   └── header.html (File): Cnblogs template.
│   ├── data (Folder, Ignored)
│   │   └── blog_data.json (File, Ignored)
│   │       Description: Information of synced articles. (e.g. id)
│   └── cnblogs.py: Sync script.
├── hexo-cnblogs-generated (Submodule, Private)
|       Description: Files for cnblogs, used for syncing directly.
|       Directory: ./public_cnblogs/
├── public (Folder, Ignored)
|       Description: Temporary Deploy Folder for htmls.
└── config_cnblogs (Folder, Should not be modified manually)
        Description: Contains config files, important for migration
===================================================================
```

## Deploying Commands

**Step 1: Generate & Check**

```bash
hexo clean  # Clean the previously generated file (Not compulsory)
hexo generate
hexo server # Start local server to check the work
```

**Step 2: Deploy** — Deploy to the website repository.

```bash
hexo deploy
```

**Step 3: Sync to `cnblogs`**

```bash
cd scripts_cnblogs # Important, the script only work in this directory
python cnblogs.py  # Note: some dependencies are needed
```

**Step 4: Version Control** (If needed)

The following directories should be concerned:

- `./` — Main Project
- `./themes/next/` — Theme Project
- `./scripts_cnblogs/` — Script Project
- `./public_cnblogs/` — Cnblogs output repository

# Project Migrating

**Step 1: Clone the main project. (Along with the submodule)**

**Step 2: Migrate (Copy) the needed configuration files.**

- `./scripts_cnblogs/data/`
- `./scripts_cnblogs/config/blog_config.json`

If syncing everything is properly synced, you can find the files in `config_cnblogs` just in the root directory.

