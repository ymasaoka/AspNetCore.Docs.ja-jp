---
title: :::no-loc(Identity):::ASP.NET Core の概要
author: rick-anderson
description: :::no-loc(Identity):::ASP.NET Core アプリで使用します。 パスワードの要件 (RequireDigit、RequiredLength、RequiredUniqueChars など) を設定する方法について説明します。
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445433"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>:::no-loc(Identity):::ASP.NET Core の概要

::: moniker range=">= aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::::

* は、ユーザーインターフェイス (UI) のログイン機能をサポートする API です。
* ユーザー、パスワード、プロファイルデータ、ロール、要求、トークン、電子メールの確認などを管理します。

ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき :::no-loc(Identity)::: ます。 サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。

[ :::no-loc(Identity)::: ソースコード](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::)は GitHub で入手できます。 [スキャフォールディング :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity)生成されたファイルを表示して、テンプレートとの対話を確認し :::no-loc(Identity)::: ます。

:::no-loc(Identity):::は、通常、ユーザー名、パスワード、およびプロファイルデータを格納するために SQL Server データベースを使用して構成されます。 別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。

このトピックでは、を使用し :::no-loc(Identity)::: てユーザーを登録、ログイン、ログアウトする方法について説明します。 注: このテンプレートでは、ユーザー名と電子メールはユーザーに対して同じものとして扱われます。 を使用するアプリを作成する方法の詳細について :::no-loc(Identity)::: は、「[次のステップ](#next)」を参照してください。

[Microsoft id プラットフォーム](/azure/active-directory/develop/)は次のとおりです。

* Azure Active Directory (Azure AD) 開発プラットフォームの進化。
* ASP.NET Core に関連付けら :::no-loc(Identity)::: れていません。

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)します。

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックします。
* ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。
* **個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

上記のコマンドは、 :::no-loc(Razor)::: SQLite を使用して web アプリを作成します。 LocalDB を使用して web アプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

生成されたプロジェクトでは、 [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity)が提供されます。 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリは、領域と共にエンドポイントを公開し `:::no-loc(Identity):::` ます。 次に例を示します。

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/アカウント/ログアウト
* /:::no-loc(Identity):::/アカウント/管理

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite を使用する場合、この手順で移行する必要はありません。

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

LocalDB の場合は、次のコマンドを実行します。

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>サービスの構成 :::no-loc(Identity):::

サービスはに追加されて `ConfigureServices` います。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

前の強調表示されたコードは、 :::no-loc(Identity)::: 既定のオプション値を使用してを構成します。 サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。

:::no-loc(Identity):::は、を呼び出すことによって有効になり <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ます。 `UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

テンプレートで生成されたアプリは、[承認](xref:security/authorization/secure-data)を使用しません。 `app.UseAuthorization`は、アプリが承認を追加する正しい順序で追加されるようにするために用意されています。 `UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 、および `UseEndpoints` は、前のコードに示されている順序で呼び出す必要があります。

およびの詳細に `:::no-loc(Identity):::Options` つい `Startup` ては、「」および「アプリケーションの起動」を参照してください <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> 。 [Application Startup](xref:fundamentals/startup)

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>スキャフォールディング Register、Login、LogOut、および RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

、、、およびの各ファイルを追加し `Register` `Login` `LogOut` `RegisterConfirmation` ます。 このセクションに示されているコードを生成するための[ :::no-loc(Razor)::: 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。 それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

スキャフォールディングの詳細につい :::no-loc(Identity)::: ては、「[スキャフォールディング identity to a :::no-loc(Razor)::: project in authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)」を参照してください。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーがページの [**登録**] ボタンをクリックすると、 `Register` `RegisterModel.OnPostAsync` アクションが呼び出されます。 ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>ログイン

ログインフォームは次の場合に表示されます。

* **ログイン**リンクが選択されています。
* ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。

ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。 `PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。

### <a name="log-out"></a>ログアウト

[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

前のコードでは、 `return RedirectToPage();` ブラウザーが新しい要求を実行し、ユーザーの id が更新されるように、コードをリダイレクトにする必要があります。

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。

Post は*Pages/Shared/_LoginPartial*に指定されています。

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Test:::no-loc(Identity):::

既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。 テストするには :::no-loc(Identity)::: 、次のように追加し [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ます。

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-no-locidentity"></a>Tsm:::no-loc(Identity):::

詳細については、次を参照して :::no-loc(Identity)::: ください。

* [完全な id UI ソースの作成](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーをステップ実行します。

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::コンポーネント

すべての :::no-loc(Identity)::: 依存する NuGet パッケージは、 [ASP.NET Core 共有フレームワーク](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)に含まれています。

のプライマリパッケージ :::no-loc(Identity)::: は[AspNetCore です :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)。 このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい :::no-loc(Identity)::: `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` ます。

## <a name="migrating-to-aspnet-core-no-locidentity"></a>ASP.NET Core への移行:::no-loc(Identity):::

既存のストアの移行に関する詳細とガイダンスについて :::no-loc(Identity)::: は、「[認証と :::no-loc(Identity)::: の移行](xref:migration/identity)」を参照してください。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: と Add:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>は ASP.NET Core 2.1 で導入されました。 `AddDefault:::no-loc(Identity):::`の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

詳細については、「 [Adddefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>静的アセットの発行を禁止する :::no-loc(Identity):::

静的 :::no-loc(Identity)::: アセット (UI 用のスタイルシートおよび JavaScript ファイル) を web ルートに発行できないようにするには :::no-loc(Identity)::: 、 `ResolveStaticWebAssetsInputsDependsOn` `Remove:::no-loc(Identity):::Assets` アプリケーションのプロジェクトファイルに次のプロパティとターゲットを追加します。

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>次の手順

* [ASP.NET Core :::no-loc(Identity)::: ソースコード](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)
* SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください :::no-loc(Identity)::: 。
* [:::no-loc(Identity)::: を構成する](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core :::no-loc(Identity)::: は ASP.NET Core アプリにログイン機能を追加するメンバーシップシステムです。 ユーザーは、に格納されているログイン情報を持つアカウントを作成することも、外部ログインプロバイダーを使用することもでき :::no-loc(Identity)::: ます。 サポートされている外部ログインプロバイダーには、 [Facebook、Google、Microsoft アカウント、Twitter](xref:security/authentication/social/index)があります。

:::no-loc(Identity):::SQL Server データベースを使用して、ユーザー名、パスワード、およびプロファイルデータを格納するように構成できます。 別の永続ストアを使用することもできます (たとえば、Azure Table Storage)。

サンプルコード ([ダウンロード方法)](xref:index#how-to-download-a-sample)を[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/)します。

このトピックでは、を使用し :::no-loc(Identity)::: てユーザーを登録、ログイン、ログアウトする方法について説明します。 を使用するアプリを作成する方法の詳細については :::no-loc(Identity)::: 、この記事の最後にある「次のステップ」を参照してください。

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>AddDefault :::no-loc(Identity)::: と Add:::no-loc(Identity):::

<xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>は ASP.NET Core 2.1 で導入されました。 `AddDefault:::no-loc(Identity):::`の呼び出しは、次の呼び出しに似ています。

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

詳細については、「 [Adddefault :::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) 」を参照してください。

## <a name="create-a-web-app-with-authentication"></a>認証を使用して Web アプリを作成する

個々のユーザーアカウントを使用して ASP.NET Core Web アプリケーションプロジェクトを作成します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [**ファイル**] [新しいプロジェクト] を選択し > **New** > **Project**ます。
* **[ASP.NET Core Web アプリケーション]** を選択します。 プロジェクトに**WebApp1**という名前を付け、プロジェクトのダウンロードと同じ名前空間にします。 **[OK]** をクリックします。
* ASP.NET Core **Web アプリケーション**を選択し、[**認証の変更**] を選択します。
* **個々のユーザーアカウント**を選択し、[ **OK]** をクリックします。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成されたプロジェクトでは、 [ :::no-loc(Razor)::: クラスライブラリ](xref:razor-pages/ui-class)として[ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity)が提供されます。 :::no-loc(Identity)::: :::no-loc(Razor)::: クラスライブラリは、領域と共にエンドポイントを公開し `:::no-loc(Identity):::` ます。 次に例を示します。

* /:::no-loc(Identity):::/Account/Login
* /:::no-loc(Identity):::/アカウント/ログアウト
* /:::no-loc(Identity):::/アカウント/管理

### <a name="apply-migrations"></a>移行を適用する

移行を適用してデータベースを初期化します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージマネージャーコンソール (PMC) で次のコマンドを実行します。

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>テストレジスタとログイン

アプリを実行し、ユーザーを登録します。 画面のサイズによっては、[ナビゲーション] トグルボタンを選択して、**登録**リンクと**ログイン**リンクを表示する必要がある場合があります。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>サービスの構成 :::no-loc(Identity):::

サービスはに追加されて `ConfigureServices` います。 一般的なパターンは、すべての `Add{Service}` メソッドを呼び出した後、すべての `services.Configure{Service}` メソッドを呼び出すことです。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

前のコードでは、 :::no-loc(Identity)::: 既定のオプション値を使用してを構成します。 サービスは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアプリで使用できるようになります。

:::no-loc(Identity):::は、 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)を呼び出すことによって有効になります。 `UseAuthentication`認証[ミドルウェア](xref:fundamentals/middleware/index)を要求パイプラインに追加します。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

詳細については、「 [ :::no-loc(Identity)::: オプションクラス](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)」と「[アプリケーションの起動](xref:fundamentals/startup)」を参照してください。

## <a name="scaffold-register-login-and-logout"></a>スキャフォールディング Register、Login、および LogOut

このセクションに示されているコードを生成するための[ :::no-loc(Razor)::: 承認手順を含むプロジェクトに、スキャフォールディング id](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)に従います。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Register、Login、および LogOut の各ファイルを追加します。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1**という名前のプロジェクトを作成した場合は、次のコマンドを実行します。 それ以外の場合は、の正しい名前空間を使用し `ApplicationDbContext` ます。

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell では、コマンドの区切り記号としてセミコロンを使用します。 PowerShell を使用する場合は、前の例に示したように、ファイルリスト内のセミコロンをエスケープするか、ファイルリストを二重引用符で囲みます。

---

### <a name="examine-register"></a>レジスタの確認

ユーザーが [**登録**] リンクをクリックすると、 `RegisterModel.OnPostAsync` アクションが呼び出されます。 ユーザーは、オブジェクトに対して[Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_)によって作成され `_userManager` ます。

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

ユーザーが正常に作成された場合は、の呼び出しによってユーザーがログインし `_signInManager.SignInAsync` ます。

**注:** 登録時にすぐにログインできないようにする手順については、「[アカウントの確認](xref:security/authentication/accconfirm#prevent-login-at-registration)」をご覧ください。

### <a name="log-in"></a>ログイン

ログインフォームは次の場合に表示されます。

* **ログイン**リンクが選択されています。
* ユーザーがアクセスを承認されていない、**または**システムによって認証されていない制限付きページにアクセスしようとしています。

ログインページのフォームが送信されると、 `OnPostAsync` アクションが呼び出されます。 `PasswordSignInAsync`は、オブジェクトで呼び出され `_signInManager` ます。

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

承認決定を行う方法については、「」を参照してください <xref:security/authorization/introduction> 。

### <a name="log-out"></a>ログアウト

[**ログアウト**すると、アクションが呼び出され `LogoutModel.OnPost` ます。 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync)者は、cookie に格納されているユーザーの要求をクリアします。

Post は*Pages/Shared/_LoginPartial*に指定されています。

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Test:::no-loc(Identity):::

既定の web プロジェクトテンプレートでは、ホームページへの匿名アクセスが許可されます。 をテストするに :::no-loc(Identity)::: は、[ [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) プライバシー] ページにを追加します。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

サインインしている場合は、サインアウトします。アプリを実行し、[**プライバシー** ] リンクを選択します。 ログイン ページにリダイレクトされます。

### <a name="explore-no-locidentity"></a>Tsm:::no-loc(Identity):::

詳細については、次を参照して :::no-loc(Identity)::: ください。

* [完全な id UI ソースの作成](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 各ページのソースを確認し、デバッガーをステップ実行します。

## <a name="no-locidentity-components"></a>:::no-loc(Identity):::コンポーネント

すべての :::no-loc(Identity)::: 依存する NuGet パッケージは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。

のプライマリパッケージ :::no-loc(Identity)::: は[AspNetCore です :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)。 このパッケージには ASP.NET Core のインターフェイスのコアセットが含まれており、によって含まれてい :::no-loc(Identity)::: `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` ます。

## <a name="migrating-to-aspnet-core-no-locidentity"></a>ASP.NET Core への移行:::no-loc(Identity):::

既存のストアの移行に関する詳細とガイダンスについて :::no-loc(Identity)::: は、「[認証と :::no-loc(Identity)::: の移行](xref:migration/identity)」を参照してください。

## <a name="setting-password-strength"></a>パスワードの強度を設定する

パスワードの最小要件を設定するサンプルについては、「[構成](#pw)」を参照してください。

## <a name="next-steps"></a>次の手順

* SQLite を使用したの構成の詳細については、こちらの[GitHub の問題](https://github.com/dotnet/AspNetCore.Docs/issues/5131)を参照してください :::no-loc(Identity)::: 。
* [:::no-loc(Identity)::: を構成する](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
