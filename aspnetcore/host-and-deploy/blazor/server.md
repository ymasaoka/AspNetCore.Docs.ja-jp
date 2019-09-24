---
title: ASP.NET Core Blazor サーバーをホストして展開する
author: guardrex
description: ASP.NET Core を使用して Blazor サーバー アプリをホストおよび展開する方法について学習します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: a393d620924d847e674a09972515a8130a15fc6a
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964228"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="3e85f-103">Blazor サーバーをホストして展開する</span><span class="sxs-lookup"><span data-stu-id="3e85f-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="3e85f-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3e85f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="3e85f-105">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="3e85f-105">Host configuration values</span></span>

<span data-ttu-id="3e85f-106">[Blazor サーバー アプリ](xref:blazor/hosting-models#blazor-server)では、[汎用ホスト構成値](xref:fundamentals/host/generic-host#host-configuration)を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="3e85f-107">配置</span><span class="sxs-lookup"><span data-stu-id="3e85f-107">Deployment</span></span>

<span data-ttu-id="3e85f-108">[Blazor サーバーのホスティング モデル](xref:blazor/hosting-models#blazor-server)を使用する場合、Blazor はサーバー上で ASP.NET Core アプリ内から実行されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="3e85f-109">UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="3e85f-110">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="3e85f-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="3e85f-111">Visual Studio には **Blazor サーバー アプリ** プロジェクト テンプレートが含まれています ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorserverside` テンプレート)。</span><span class="sxs-lookup"><span data-stu-id="3e85f-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="3e85f-112">スケーラビリティ</span><span class="sxs-lookup"><span data-stu-id="3e85f-112">Scalability</span></span>

<span data-ttu-id="3e85f-113">Blazor サーバー アプリで使用できるインフラストラクチャを最大限に活用できるように展開を計画します。</span><span class="sxs-lookup"><span data-stu-id="3e85f-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="3e85f-114">Blazor サーバー アプリのスケーラビリティに対処するには、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e85f-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="3e85f-115">Blazor サーバー アプリの基礎</span><span class="sxs-lookup"><span data-stu-id="3e85f-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="3e85f-116">展開サーバー</span><span class="sxs-lookup"><span data-stu-id="3e85f-116">Deployment server</span></span>

<span data-ttu-id="3e85f-117">1 台のサーバーのスケーラビリティ (スケールアップ) を検討する場合、アプリに使用できるメモリは、ユーザーの要求が増えたときにアプリによって使い果たされる最初のリソースになります。</span><span class="sxs-lookup"><span data-stu-id="3e85f-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="3e85f-118">サーバー上の使用可能なメモリは、以下の影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="3e85f-119">サーバーがサポートできるアクティブ回線の数。</span><span class="sxs-lookup"><span data-stu-id="3e85f-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="3e85f-120">クライアントでの UI の待機時間。</span><span class="sxs-lookup"><span data-stu-id="3e85f-120">UI latency on the client.</span></span>

<span data-ttu-id="3e85f-121">セキュリティで保護されたスケーラブルな Blazor サーバー アプリを構築するためのガイダンスについては、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e85f-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="3e85f-122">各回線では、最小限の *Hello World* スタイルのアプリに約 250 KB のメモリが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="3e85f-123">回線のサイズは、アプリのコードと各コンポーネントに関連付けられている状態の保守要件によって変わります。</span><span class="sxs-lookup"><span data-stu-id="3e85f-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="3e85f-124">アプリとインフラストラクチャの開発時にはリソースのニーズを測定することをお勧めしますが、展開ターゲットを計画する際に、次のベースラインを出発点にすることができます。アプリで 5,000 人の同時ユーザーをサポートすることを想定している場合は、アプリに対して少なくとも 1.3 GB のサーバー メモリ (またはユーザーあたり最大 273 KB) の予算を割り当てること検討してください。</span><span class="sxs-lookup"><span data-stu-id="3e85f-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="3e85f-125">SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="3e85f-125">SignalR configuration</span></span>

<span data-ttu-id="3e85f-126">Blazor サーバー アプリでは、ブラウザーとの通信に ASP.NET Core SignalR が使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="3e85f-127">[SignalR のホストとスケールの条件](xref:signalr/publish-to-azure-web-app)は、Blazor サーバー アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="3e85f-128">Blazor は、待ち時間、信頼性、および[セキュリティ](xref:signalr/security)が低いために WebSocket を SignalR トランスポートとして使用する場合に最適です。</span><span class="sxs-lookup"><span data-stu-id="3e85f-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="3e85f-129">WebSocket が使用できない場合や、ロング ポーリングを使用するようにアプリが明示的に構成されている場合は、SignalR によってロング ポーリングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="3e85f-130">Azure App Service にデプロイする場合は、サービスの Azure portal 設定で WebSocket を使用するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="3e85f-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="3e85f-131">Azure App Service 用にアプリを構成する方法の詳細については、[SignalR の発行ガイドライン](xref:signalr/publish-to-azure-web-app)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e85f-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="3e85f-132">Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3e85f-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="3e85f-133">このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="3e85f-134">さらに、SignalR サービスのグローバル リーチと高パフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="3e85f-135">ネットワーク待機時間の測定</span><span class="sxs-lookup"><span data-stu-id="3e85f-135">Measure network latency</span></span>

<span data-ttu-id="3e85f-136">次の例に示すように、[JS 相互運用機能](xref:blazor/javascript-interop)を使用してネットワーク待機時間を測定できます。</span><span class="sxs-lookup"><span data-stu-id="3e85f-136">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="3e85f-137">妥当な UI エクスペリエンスを実現するために、UI の待機時間を 250 ミリ秒以下にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3e85f-137">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>