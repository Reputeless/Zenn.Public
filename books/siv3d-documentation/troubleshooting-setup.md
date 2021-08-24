---
title: "トラブルシューティング | インストール編"
free: true
---

# インストール時のトラブルシューティング

## Visual Studio のプロジェクトテンプレート一覧に OpenSiv3D が表示されない

### 原因 1: ユーザプロジェクト テンプレートの場所の設定が正しくありません

Visual Studio のメニューで、「ツール」→「オプション」→「プロジェクトおよびソリューション」→「全般」→「場所」→「**ユーザプロジェクトテンプレートの場所**」が `~\Documents\Visual Studio 2019\Templates\ProjectTemplates` のように、ドキュメントフォルダ内の `ProjectTemplates` フォルダに設定されているかを確認してください。

### 原因 2: Visual Studio が新しいプロジェクトテンプレートを認識していません

Visual Studio のプロジェクトテンプレート一覧を、再スキャンによって更新する必要があります。コマンドプロンプトを起動して `Program Files (x86)/Microsoft Visual Studio/2019/Community/Common7/IDE` へ移動し、`devenv /InstallVSTemplates` コマンドを実行すると、プロジェクトテンプレートを再スキャンします。

![](/images/doc_v6/manual/devenv.png)
