---
title: ASP.NET Core での承認の概要
author: rick-anderson
description: 承認の基本と ASP.NET Core アプリでの承認のしくみについて説明します。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405433"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>ASP.NET Core での承認の概要

<a name="security-authorization-introduction"></a>

承認とは、ユーザーが実行できる操作を決定するプロセスを指します。 たとえば、管理ユーザーは、ドキュメントライブラリの作成、ドキュメントの追加、ドキュメントの編集、および削除を行うことができます。 ライブラリを操作する管理者以外のユーザーは、ドキュメントの読み取りのみが許可されています。

認証は直交しており、認証から独立しています。 ただし、承認には認証メカニズムが必要です。 認証は、ユーザーを突き止めるするプロセスです。 承認では、現在のユーザーのために 1 つ以上の ID を作成する場合があります。

ASP.NET Core での認証の詳細については、「」を参照してください <xref:security/authentication/index> 。

## <a name="authorization-types"></a>承認の種類

ASP.NET Core 承認は、単純な宣言型の[役割](xref:security/authorization/roles)と豊富な[ポリシーベース](xref:security/authorization/policies)のモデルを提供します。 承認は要件で表現され、ハンドラーは要件に対してユーザーの要求を評価します。 命令型チェックは、ユーザーがアクセスしようとしているリソースのユーザー id とプロパティの両方を評価する単純なポリシーまたはポリシーに基づいて行うことができます。

## <a name="namespaces"></a>名前空間

属性と属性を含む承認コンポーネント `AuthorizeAttribute` `AllowAnonymousAttribute` は、 `Microsoft.AspNetCore.Authorization` 名前空間にあります。

[単純な承認](xref:security/authorization/simple)に関するドキュメントを参照してください。
