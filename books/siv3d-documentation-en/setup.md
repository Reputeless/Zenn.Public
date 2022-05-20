---
title: "Getting Started (Installing the Siv3D SDK)"
free: true
---

You will be ready to start Siv3D in about 30 seconds on Windows and macOS.

# 1. Windows

## 1.1 Installing the Siv3D SDK

- Download and run **[OpenSiv3D v0.6.4 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.4_Installer.exe)**.
- The installer will automatically do the following:
  - Copy SDK folder (The default location is `Documents`).
  - Set a user environment variable "SIV3D_0_6_4" with the path to the SDK folder.
  - Copy the Visual Studio project template for the Siv3D project (The default locations is `Documents\Visual Studio 2022\Templates\ProjectTemplates\`).
  - Register an uninstaller.

> If you want to uninstall OpenSiv3D SDK, click "Uninstall" in the Apps & features pane.
> ![](/images/doc_v6/manual/uninstall.png)

::: message
If the installer fails to run, please install the SDK manually as described in "4. Manually Installing the SDK (Windows)" on this page.
:::

## 1.2 Building your first application with Siv3D

https://www.youtube.com/watch?v=O0XtvulXSOk

1. Lanuch Visual Studio and open a **New Project Dialog** by clicking **Create a new project**.
1. Select **OpenSiv3D(X.X.X)** project and then click **Next**. (⚠️If the project template is not shown, [see the trouble shooting](https://zenn.dev/reputeless/books/siv3d-documentation-en/viewer/troubleshooting-setup))
1. Type a name for the project and click **OK** to create the project.
1. On the **Build** menu, click **Build Solution**.
1. On the **Debug** menu, click **Start Debugging**.

# 2. macOS

## 2.1 Downloading the Siv3D project template

- Download and extract **[OpenSiv3D v0.6.4 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.4_macOS.zip)**

- (On macOS Catalina or later) Move the SDK folder into `User/Applications` folder to prevent a file access permissions dialog from being displayed when your program launches. Some folders such as `User/Desktop` and `User/Downloads` require an access permission for every build.

## 2.2 Building your first application with Siv3D
1. Open the project file `examples/empty/empty.xcodeproj` in Xcode.
1. Click **Run button ▶️** to build and run the application.
1. (On macOS Catalina or later) A file access permissions dialog can be inactivated by placing the project folder under `User/Applications` folder.

> 新しいプロジェクトを増やしたい場合は、プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください。  
> Xcode 用のプロジェクトジェネレータは将来提供される予定です。

# 3. Linux

The Linux version is not distributed in SDK format, so you will need to build the library yourself.

## 3.1 Getting the latest source code from the official OpenSiv3D repository

[OpenSiv3D 公式リポジトリの main ブランチ](https://github.com/Siv3D/OpenSiv3D) が、最新の安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://storage.googleapis.com/zenn-user-upload/nc8tfa4gj60oyu134d99tboqtla8)

## 3.2 Installing the dependent packages
次を参考に必要なパッケージをインストールします。  
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49

## 3.3 Building the OpenSiv3D library
次を参考に Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60

## 3.4 Building your first application with Siv3D
次を参考に Siv3D アプリをビルドします。 
https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L62-L71


# 4. Manually Installing the Siv3D SDK (Windows)
If you have trouble with the SDK installer in Windows, you can manually install the Siv3D SDK.

## 4.1 Getting the Siv3D SDK

Download and extract [OpenSiv3D_SDK_0.6.4.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.4/OpenSiv3D_SDK_0.6.4.zip) (File size: 88 MB), and place the contents in your documents folder as follows:

- `.../Documents/OpenSiv3D_SDK_0.6.4/include`
- `.../Documents/OpenSiv3D_SDK_0.6.4/lib`
- `.../Documents/OpenSiv3D_SDK_0.6.4/addon`

## 4.2 Setting the SDK folder path to the environment variable

Create a new environment variable `SIV3D_0_6_4` and set the path to the SDK folder (the parent folder of `include/` and `lib/` placed in 4.1).

Example: If you have placed `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.4/include`, set `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.4` to the environment variable `SIV3D_0_6_4`.

![](/images/doc_v6/manual/envvariable.png)

## 4.3 Deploying the OpenSiv3D project template (ZIP)
Download the project template for Visual Studio [OpenSiv3D_0.6.4.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.4/OpenSiv3D_0.6.4.zip) (File size: 63 MB), and place it **without extracting it** in the folder `Documents/Visual Studio 2022/Templates/ProjectTemplates/`, which is created in the Documents folder when you install Visual Studio 2022. 

![](/images/doc_v6/manual/projecttemplate.png)

That's it. Reboot your PC to ensure that the environment variables are applied.

---

In the next page, you will learn how to customize the Hello Siv3D program.
