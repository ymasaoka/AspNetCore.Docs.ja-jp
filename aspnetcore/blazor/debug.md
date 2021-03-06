---
title: ASP.NET Core Blazor WebAssembly をデバッグする
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 828fb0ce5101407b6f40195138d59c335eec389f
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407672"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>ASP.NET Core Blazor WebAssembly をデバッグする

[Daniel Roth](https://github.com/danroth27)

Blazor WebAssembly アプリは、Chromium ベースのブラウザー (Edge/Chrome) のブラウザー開発ツールを使用してデバッグできます。 または、Visual Studio または Visual Studio Code を使用してアプリをデバッグすることもできます。

利用可能なシナリオには以下が含まれます。

* ブレークポイントの設定と削除を行う。
* Visual Studio と Visual Studio Code (<kbd>F5</kbd> サポート) でデバッグ サポートを使用して、アプリを実行する。
* コードを使用して、シングルステップ (<kbd>F10</kbd>) を実行する。
* ブラウザーで <kbd>F8</kbd> を使用するか、Visual Studio または Visual Studio Code で <kbd>F5</kbd> を使用して、コードの実行を再開する。
* *Locals* の表示で、ローカル変数の値を観察する。
* 呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。

現時点では、次の操作を行うことは*できません*。

* 未処理の例外の発生時に中断する。
* アプリの起動中にブレークポイントにヒットする。

今後のリリースでは、引き続きデバッグエクス ペリエンスが向上します。

## <a name="prerequisites"></a>必須コンポーネント

デバッグには、次のいずれかのブラウザーが必要です。

* Google Chrome (バージョン 70 以降) (既定値)
* Microsoft Edge (バージョン 80 以降)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Visual Studio と Visual Studio Code のデバッグを有効にする

既存の Blazor WebAssembly アプリのデバッグを有効にするには、スタートアップ プロジェクトの `launchSettings.json` ファイルを更新して、各起動プロファイルに次の `inspectUri` プロパティを含めます。

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

更新されると、`launchSettings.json` ファイルは次の例のようになります。

[!code-json[](debug/launchSettings.json?highlight=14,22)]

`inspectUri` プロパティ:

* アプリが Blazor WebAssembly アプリであることを IDE で検出できるようにします。
* スクリプト デバッグ インフラストラクチャに対して、Blazor のデバッグ プロキシを使用してブラウザーに接続するように指示します。

起動したブラウザー (`browserInspectUri`) の Websocket プロトコル (`wsProtocol`)、ホスト (`url.hostname`)、ポート (`url.port`)、およびインスペクター URI のプレースホルダー値は、フレームワークによって提供されます。

## <a name="visual-studio"></a>Visual Studio

Visual Studio で Blazor WebAssembly アプリをデバッグするには、次のようにします。

1. 新しい ASP.NET Core でホストされる Blazor WebAssembly アプリを作成します。
1. <kbd>F5</kbd> キーを押して、デバッガーでアプリを実行します。
1. `Pages/Counter.razor` の `IncrementCount` メソッドにブレークポイントを設定します。
1. **`Counter`** タブに移動し、ボタンを選択してブレークポイントをヒットします。

   ![デバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. [ローカル] ウィンドウの `currentCount` フィールドの値を確認します。

   ![[ローカル] を表示する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <kbd>F5</kbd> キーを押して、実行を続行します。

Blazor WebAssembly アプリのデバッグ中に、サーバー コードをデバッグすることもできます。

1. `Pages/FetchData.razor` ページの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> にブレークポイントを設定します。
1. `Get` アクション メソッドの `WeatherForecastController` にブレークポイントを設定します。
1. **`Fetch Data`** タブに移動し、サーバーへの HTTP 要求を発行する直前に `FetchData` コンポーネント内の最初のブレークポイントをヒットします。

   ![フェッチ データをデバッグする](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <kbd>F5</kbd> キーを押して実行を続行し、`WeatherForecastController` 内のサーバーでブレークポイントをヒットします。

   ![デバッグ サーバー](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <kbd>F5</kbd> をもう一度押して実行を続行し、天気予報テーブルがレンダリングされたことを確認します。

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Blazor アプリ開発のための Visual Studio Code のインストールの詳細については、「<xref:blazor/tooling>」を参照してください。

### <a name="debug-standalone-blazor-webassembly"></a>スタンドアロン Blazor WebAssembly のデバッグ

1. VS Code でスタンドアロンの Blazor WebAssembly アプリを開きます。

   デバッグを有効にするために追加の設定が必要であるという次の通知が表示された場合は、以下のようにします。
   
   * 正しい拡張機能がインストールされていることを確認します。
   * JavaScript のプレビュー デバッグが有効になっていることを確認します。
   * ウィンドウを再度読み込みます。

   ![追加の設定が必要](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <kbd>F5</kbd> キーボード ショートカットまたはメニュー項目を使用してデバッグを開始します。

1. プロンプトが表示されたら、 **[Blazor WebAssembly Debug]** オプションを選択してデバッグを開始します。

   ![使用可能なデバッグ オプションの一覧](index/_static/blazor-vscode-debugtypes.png)

1. スタンドアロン アプリが起動され、デバッグ ブラウザーが開きます。

1. `Counter` コンポーネントの `IncrementCount` メソッドにブレークポイントを設定し、ボタンを選択してブレークポイントをヒットします。

   ![VS Code のデバッグ カウンター](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>ホストされた Blazor WebAssembly のデバッグ

1. VS Code で、ホストされた Blazor WebAssembly アプリのソリューション フォルダーを開きます。

1. プロジェクトの起動構成が設定されていない場合は、次の通知が表示されます。 **[はい]** を選択します。

   ![必要なアセットを追加する](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. ウィンドウの上部にあるコマンド パレットで、ホストされているソリューション内の *Server* プロジェクトを選択します。

`launch.json` ファイルが、デバッガーを起動するための起動構成を使用して生成されます。

### <a name="attach-to-an-existing-debugging-session"></a>既存のデバッグセッションにアタッチする

実行中の Blazor アプリにアタッチするには、次のように構成された `launch.json` ファイルを作成します。

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> デバッグ セッションへのアタッチは、スタンドアロン アプリでのみサポートされています。 フルスタック デバッグを使用するには、VS Code からアプリを起動する必要があります。

### <a name="launch-configuration-options"></a>起動構成のオプション

`blazorwasm` デバッグの種類では、次の起動構成オプションがサポートされています (`.vscode/launch.json`)。

| オプション    | 説明 |
| --------- | ----------- |
| `request` | `launch` を使用して、Blazor WebAssembly アプリにデバッグ セッションを起動してアタッチするか、`attach` を使用して既に実行中のアプリにデバッグ セッションをアタッチします。 |
| `url`     | デバッグ時にブラウザーで開く URL。 既定値は `https://localhost:5001` です。 |
| `browser` | デバッグ セッション用に起動するブラウザー。 `edge` または `chrome` に設定します。 既定値は `chrome` です。 |
| `trace`   | JS デバッガーからログを生成するために使用されます。 ログを生成するには `true` に設定します。 |
| `hosted`  | ホストされている Blazor WebAssembly アプリを起動およびデバッグする場合は、`true` に設定する必要があります。 |
| `webRoot` | Web サーバーの絶対パスを指定します。 アプリをサブルートから提供する場合は設定する必要があります。 |
| `timeout` | デバッグ セッションがアタッチされるのを待機するミリ秒単位の時間。 既定値は 30,000 ミリ秒 (30 秒) です。 |
| `program` | ホストされたアプリのサーバーを実行する実行可能ファイルへの参照。 `hosted` が `true` の場合に設定する必要があります。 |
| `cwd`     | アプリを起動する作業ディレクトリ。 `hosted` が `true` の場合に設定する必要があります。 |
| `env`     | 起動されたプロセスに提供する環境変数。 `hosted` が `true`に設定されている場合にのみ適用されます。 |

### <a name="example-launch-configurations"></a>起動構成の例

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>スタンドアロン Blazor WebAssembly アプリを起動してデバッグする

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>指定された URL で実行中のアプリにアタッチする

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a>Microsoft Edge でホストされている Blazor WebAssembly アプリを起動してデバッグする

ブラウザーの構成の既定値は Google Chrome です。 デバッグに Microsoft Edge を使用する場合は、`browser` を `edge` に設定します。 Google Chrome を使用するには、`browser` オプションを設定しないか、オプションの値を `chrome` に設定します。

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

前の例では、`MyHostedApp.Server.dll` は *Server* アプリのアセンブリです。 `.vscode` フォルダーは、`Client`、`Server`、および `Shared` フォルダーの隣にあるソリューションのフォルダー内にあります。

## <a name="debug-in-the-browser"></a>ブラウザーでデバッグする

1. 開発環境でアプリのデバッグ ビルドを実行します。

1. ブラウザーを起動し、アプリの URL (`https://localhost:5001` など) に移動します。

1. ブラウザーで <kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd> キーを押すことによって、リモート デバッグの開始を試みます。

   ブラウザーはリモート デバッグが有効で実行されている必要があります。これは既定ではありません。 リモート デバッグが無効になっている場合、"**デバッグ可能なブラウザー タブが見つからない**" というエラー ページと、デバッグ ポートを開いた状態でブラウザーを起動する手順がレンダリングされます。 ブラウザーの指示に従って、新しいブラウザー ウィンドウを開きます。 前のブラウザー ウィンドウを閉じます。

1. リモート デバッグが有効な状態でブラウザーを実行すると、デバッグ用のキーボード ショートカット (<kbd>Shift</kbd> + <kbd>Alt</kbd> + <kbd>D</kbd>) によって新しいデバッガー タブが開きます。

1. 少しすると、 **[ソース]** タブに、`file://` ノード内にあるアプリの .NET アセンブリの一覧が表示されます。

1. コンポーネント コード (`.razor` ファイル) と C# コード ファイル (`.cs`) で、設定したブレークポイントがコードの実行時にヒットします。 ブレークポイントにヒットした後、コード全体をステップ実行する (<kbd>F10</kbd>) か、コードの実行を普通に再開 (<kbd>F8</kbd>) します。

Blazor は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。 デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。 プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。

## <a name="browser-source-maps"></a>ブラウザー ソース マップ

ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。 ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。 その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。

## <a name="troubleshoot"></a>トラブルシューティング

エラーが発生している場合は、次のヒントが役立つことがあります。

* **[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。 コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。
* ASP.NET Core HTTPS 開発証明書がインストールされ、信頼されていることを確認します。 詳細については、「<xref:security/enforcing-ssl#troubleshoot-certificate-problems>」を参照してください。
* Visual Studio では、 **[ツール]**  >  **[オプション]**  >  **[デバッグ]**  >  **[全般]** で **[Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)] (ASP.NET (Chrome、Edge、IE) の JavaScript デバッグを有効にする)** オプションをオンにする必要があります。 これは、Visual Studio の既定の設定です。 デバッグが機能していない場合は、オプションがオンになっていることを確認します。
