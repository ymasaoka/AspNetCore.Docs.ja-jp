---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft.NET.Sdk.Web の概要。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/web-sdk
ms.openlocfilehash: 2d154ebdbcb564ff5174940691b63ecce4154987
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403730"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>概要

`Microsoft.NET.Sdk.Web` は、ASP.NET Core アプリをビルドするための [MSBuild プロジェクト SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) です。 この SDK を使用せずに ASP.NET Core アプリをビルドすることもできますが、Web SDK は、

* 最上級のエクスペリエンスを提供するために作成されています。
* ほとんどのユーザーに推奨されるターゲットです。

プロジェクトで Web SDK を使用する:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Web SDK を使用して有効にされる機能:

* .NET Core 3.0 以降を対象とするプロジェクトは暗黙的に次を参照します。

  * [ASP.NET Core 共有フレームワーク](xref:fundamentals/metapackage-app).
  * ASP.NET Core アプリをビルドするために設計された[アナライザー](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。
* Web SDK は、発行プロファイルの使用と WebDeploy を使用した発行を可能にする MSBuild ターゲットをインポートします。

### <a name="properties"></a>プロパティ

| プロパティ | 説明 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | `Microsoft.AspNetCore.App` 共有フレームワークへの暗黙的な参照を無効にします。 |
| `DisableImplicitAspNetCoreAnalyzers` | ASP.NET Core アナライザーへの暗黙的な参照を無効にします。 |
| `DisableImplicitComponentsAnalyzers` | Blazor (サーバー) アプリケーションをビルドするときに、Razor コンポーネント アナライザーへの暗黙的な参照を無効にします。 |
