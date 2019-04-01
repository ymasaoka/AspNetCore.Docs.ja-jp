---
title: ASP.NET Core のローカライズに関するトラブルシューティング
author: hishamco
description: ASP.NET Core アプリのローカライズに関する問題を診断する方法について説明します。
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 73405f539c89d79096e7b536407cd9730679d478
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488995"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="bbc00-103">ASP.NET Core のローカライズに関するトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="bbc00-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="bbc00-104">作成者: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="bbc00-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="bbc00-105">この記事では、ASP.NET Core アプリのローカライズに関する問題を診断する方法の手順を紹介します。</span><span class="sxs-lookup"><span data-stu-id="bbc00-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="bbc00-106">ローカライズの構成に関する問題</span><span class="sxs-lookup"><span data-stu-id="bbc00-106">Localization configuration issues</span></span>

<span data-ttu-id="bbc00-107">**ローカライズ ミドルウェアの順序**</span><span class="sxs-lookup"><span data-stu-id="bbc00-107">**Localization middleware order**</span></span>  
<span data-ttu-id="bbc00-108">ローカライズ ミドルウェアが正しい順序で設定されていないため、アプリがローカライズされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="bbc00-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="bbc00-109">この問題を解決するには、確実にローカライズ ミドルウェアを登録してから、MVC ミドルウェアを登録します。</span><span class="sxs-lookup"><span data-stu-id="bbc00-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="bbc00-110">それ以外の場合は、ローカライズ ミドルウェアは適用されません。</span><span class="sxs-lookup"><span data-stu-id="bbc00-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="bbc00-111">**ローカライズ リソースのパスが見つかりません**</span><span class="sxs-lookup"><span data-stu-id="bbc00-111">**Localization resources path not found**</span></span>

<span data-ttu-id="bbc00-112">**RequestCultureProvider でサポートされているカルチャが一度登録したものと一致しません**</span><span class="sxs-lookup"><span data-stu-id="bbc00-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="bbc00-113">リソース ファイルの名前付けに関する問題</span><span class="sxs-lookup"><span data-stu-id="bbc00-113">Resource file naming issues</span></span>

<span data-ttu-id="bbc00-114">ASP.NET Core では、ローカライズ リソース ファイルの名前付けに対するルールとガイドラインがあらかじめ定義されています。詳細については、[こちら](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bbc00-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="bbc00-115">リソースの不足</span><span class="sxs-lookup"><span data-stu-id="bbc00-115">Missing resources</span></span>

<span data-ttu-id="bbc00-116">リソースが見つからない一般的な原因は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bbc00-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="bbc00-117">`resx` ファイルまたはローカライズ ツールの要求のいずれかで、ファイル名のスペルが間違っています。</span><span class="sxs-lookup"><span data-stu-id="bbc00-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="bbc00-118">一部の言語の `resx` でリソースが不足しており、その他の言語に存在しています。</span><span class="sxs-lookup"><span data-stu-id="bbc00-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="bbc00-119">引き続き問題が発生する場合は、不足しているリソースの詳細について、ローカライズのログ メッセージ (`Debug` ログ レベル) を確認してください。</span><span class="sxs-lookup"><span data-stu-id="bbc00-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="bbc00-120">_**ヒント:** `CookieRequestCultureProvider` を使用している場合、ローカライズの Cookie 値内のカルチャで単一引用符が使用されないことを確認します。たとえば、`c='en-UK'|uic='en-US'` は無効な Cookie 値ですが、`c=en-UK|uic=en-US` は有効です。_</span><span class="sxs-lookup"><span data-stu-id="bbc00-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="bbc00-121">リソースとクラス ライブラリに関する問題</span><span class="sxs-lookup"><span data-stu-id="bbc00-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="bbc00-122">既定で、ASP.NET Core では、クラス ライブラリで [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) を使ってリソース ファイルを検索できる方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="bbc00-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="bbc00-123">クラス ライブラリに関する一般的な問題には次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="bbc00-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="bbc00-124">クラス ライブラリでの `ResourceLocationAttribute` の不足によって、`ResourceManagerStringLocalizerFactory` でリソースを検出することができない。</span><span class="sxs-lookup"><span data-stu-id="bbc00-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="bbc00-125">リソース ファイルの名前付け。</span><span class="sxs-lookup"><span data-stu-id="bbc00-125">Resource file naming.</span></span> <span data-ttu-id="bbc00-126">詳細については、「[リソース ファイルの名前付けに関する問題](#resource-file-naming-issues)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc00-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="bbc00-127">クラス ライブラリのルート名前空間の変更。</span><span class="sxs-lookup"><span data-stu-id="bbc00-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="bbc00-128">詳細については、「[ルート名前空間に関する問題](#root-namespace-issues)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bbc00-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="bbc00-129">CustomRequestCultureProvider が正しく動作しない</span><span class="sxs-lookup"><span data-stu-id="bbc00-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="bbc00-130">`RequestLocalizationOptions` クラスには、既定のプロバイダーが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="bbc00-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="bbc00-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) によって、ローカライズ カルチャをご利用のアプリ内で指定する方法をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="bbc00-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="bbc00-132">`CustomRequestCultureProvider` は、既定のプロバイダーが要件に合わないときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="bbc00-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="bbc00-133">カスタム プロバイダーが適切に動作しない一般的な理由は、これが `RequestCultureProviders` リストの最初のプロバイダーではないことです。</span><span class="sxs-lookup"><span data-stu-id="bbc00-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="bbc00-134">この問題を解決するには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="bbc00-134">To resolve this issue:</span></span>

- <span data-ttu-id="bbc00-135">次のように、`RequestCultureProviders` リストの 0 の位置にカスタム プロバイダーを挿入します。</span><span class="sxs-lookup"><span data-stu-id="bbc00-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```

- <span data-ttu-id="bbc00-136">`AddInitialRequestCultureProvider` 拡張メソッドを使用して、初期プロバイダーとしてカスタム プロバイダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="bbc00-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="bbc00-137">ルート名前空間に関する問題</span><span class="sxs-lookup"><span data-stu-id="bbc00-137">Root Namespace issues</span></span>

<span data-ttu-id="bbc00-138">アセンブリのルート名前空間がアセンブリ名と異なる場合、既定ではローカライズは動作しません。</span><span class="sxs-lookup"><span data-stu-id="bbc00-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="bbc00-139">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) を使用するこの問題を回避する方法は、[ここ](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)で説明されています。</span><span class="sxs-lookup"><span data-stu-id="bbc00-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

## <a name="resources--build-action"></a><span data-ttu-id="bbc00-140">リソースとビルド アクション</span><span class="sxs-lookup"><span data-stu-id="bbc00-140">Resources & Build Action</span></span>

<span data-ttu-id="bbc00-141">ローカライズのためにリソース ファイルを使用する場合、適切なビルド アクションが存在することが重要です。</span><span class="sxs-lookup"><span data-stu-id="bbc00-141">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="bbc00-142">これらは**埋め込みリソース**である必要があり、それ以外の場合、`ResourceStringLocalizer` でこれらのリソースを見つけることはできません。</span><span class="sxs-lookup"><span data-stu-id="bbc00-142">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>