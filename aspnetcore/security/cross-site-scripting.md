---
title: ASP.NET Core でクロスサイトスクリプティング (XSS) を防止する
author: rick-anderson
description: クロスサイトスクリプティング (XSS) と、ASP.NET Core アプリでこの脆弱性に対処するための手法について説明します。
ms.author: riande
ms.date: 10/02/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cross-site-scripting
ms.openlocfilehash: a94fe1612c023468238f09a91ddb0346b65d52ba
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408020"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>ASP.NET Core でクロスサイトスクリプティング (XSS) を防止する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

クロスサイトスクリプティング (XSS) はセキュリティの脆弱性であり、攻撃者がクライアント側のスクリプト (通常は JavaScript) を web ページに配置できるようにします。 他のユーザーが影響を受けるページを読み込むと、攻撃者のスクリプトが実行されるので、攻撃者は cookie やセッショントークンを盗んだり、DOM 操作を通じて web ページの内容を変更したり、ブラウザーを別のページにリダイレクトしたりできます。 XSS 脆弱性は一般的に、アプリケーションがユーザー入力を受け取り、それを検証、エンコード、またはエスケープせずにページに出力するときに発生します。

## <a name="protecting-your-application-against-xss"></a>XSS からアプリケーションを保護する

基本的なレベルの XSS では、アプリケーションを欺いして、 `<script>` レンダリングされたページにタグを挿入したり、イベントを要素に挿入したりし `On*` ます。 開発者は、次の防止手順を使用して、アプリケーションに XSS が導入されないようにする必要があります。

1. 次の手順を実行しない限り、信頼されていないデータを HTML 入力に入れないでください。 信頼されていないデータとは、攻撃者によって制御される可能性があるすべてのデータのことです。攻撃者としてデータベースからデータをソース化したとしても、アプリケーションを侵害できない場合でも、データベースを侵害する可能性があります。

2. HTML 要素内に信頼できないデータを配置する前に、html がエンコードされていることを確認します。 HTML エンコーディングはなど &lt; の文字を受け取り、lt; のように安全な形式に変更し &amp; ます。

3. 信頼できないデータを HTML 属性に配置する前に、HTML がエンコードされていることを確認します。 HTML 属性のエンコードは、HTML エンコーディングのスーパーセットであり、"and" などの追加の文字をエンコードします。

4. 信頼されていないデータを JavaScript に配置する前に、実行時に取得する内容を持つ HTML 要素にデータを配置します。 これが不可能な場合は、データが JavaScript でエンコードされていることを確認します。 Javascript エンコードでは、JavaScript では危険な文字が使用され、その16進数で置き換えら &lt; れます。たとえば、としてエンコードさ `\u003C` れます。

5. 信頼されていないデータを URL クエリ文字列に含める前に、URL がエンコードされていることを確認してください。

## <a name="html-encoding-using-razor"></a>を使用した HTML エンコードRazor

RazorMVC で使用されるエンジンでは、そのような処理を回避することが難しい場合を除き、変数からのすべての出力ソースが自動的にエンコードされます。 ディレクティブを使用すると、常に HTML 属性のエンコード規則が使用さ *@* れます。 Html 属性のエンコードは HTML エンコーディングのスーパーセットなので、HTML エンコーディングと HTML 属性のどちらを使用する必要があるかについては、それを気にする必要はありません。 信頼されていない入力を JavaScript に直接挿入しようとする場合は、HTML コンテキストでのみ @ を使用する必要があります。 タグヘルパーでは、タグパラメーターで使用する入力もエンコードされます。

次のビューを実行し Razor ます。

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

このビューは、 *Untrustedinput*変数の内容を出力します。 この変数には、XSS 攻撃で使用されるいくつかの文字が含まれてい &lt; &gt; ます。 ソースを調べると、次のようにエンコードされた出力が表示されます。

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> MVC ASP.NET Core は、 `HtmlString` 出力時に自動的にエンコードされないクラスを提供します。 これは XSS 脆弱性を公開するため、信頼されていない入力と組み合わせて使用しないでください。

## <a name="javascript-encoding-using-razor"></a>を使用した JavaScript のエンコードRazor

JavaScript に値を挿入して、ビューで処理することが必要になる場合があります。 これには、2 つの方法があります。 値を挿入する最も安全な方法は、タグのデータ属性に値を配置し、JavaScript で値を取得することです。 次に例を示します。

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

これにより、次の HTML が生成されます。

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

実行すると、次のように表示されます。

```
<"123">
   <"123">
```

JavaScript エンコーダーを直接呼び出すこともできます。

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
```

これは、次のようにブラウザーで表示されます。

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> JavaScript では信頼されていない入力を連結して DOM 要素を作成しないでください。 を使用 `createElement()` して、などのプロパティ値 `node.TextContent=` を適切に割り当てるか、またはを使用して `element.SetAttribute()` / `element[attribute]=` DOM ベースの XSS に公開する必要があります。

## <a name="accessing-encoders-in-code"></a>コード内のエンコーダーへのアクセス

HTML、JavaScript、および URL エンコーダーは、次の2つの方法でコードで使用できます。[依存関係の挿入](xref:fundamentals/dependency-injection)を使用して挿入することも、名前空間に含まれる既定のエンコーダーを使用することもでき `System.Text.Encodings.Web` ます。 既定のエンコーダーを使用する場合、安全として扱う文字範囲に適用したものは有効になりません。既定のエンコーダーでは、可能な限り安全なエンコーディング規則が使用されます。

DI を使用して構成可能なエンコーダーを使用するには、コンストラクターが*htmlencoder*、 *JavaScriptEncoder* 、および*urlencoder*パラメーターを必要に応じて受け取る必要があります。 次に例を示します。

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>エンコード URL パラメーター

信頼できない入力を含む URL クエリ文字列を値として作成する場合は、を使用して `UrlEncoder` 値をエンコードします。 たとえば、オブジェクトに適用された

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

エンコード後、エンコード値の変数にはが含まれ `%22Quoted%20Value%20with%20spaces%20and%20%26%22` ます。 スペース、引用符、句読点、およびその他の安全でない文字は、16進数値にパーセントでエンコードされます。たとえば、スペース文字は %20 になります。

>[!WARNING]
> URL パスの一部として信頼されていない入力は使用しないでください。 常に信頼できない入力をクエリ文字列値として渡します。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>エンコーダーのカスタマイズ

既定では、エンコーダーは基本的なラテン Unicode 範囲に限定されたセーフリストを使用し、その範囲外のすべての文字を同等の文字コードとしてエンコードします。 この動作は、 Razor エンコーダーを使用して文字列を出力するため、taghelper と HtmlHelper のレンダリングにも影響します。

この理由は、未知のブラウザーバグや今後のブラウザーバグから保護するためのものです (以前のブラウザーのバグは、英語以外の文字の処理に基づいて解析されました)。 Web サイトで、中国語、キリル語などのラテン文字以外の文字を頻繁に使用する場合、これは必要な動作ではないと思います。

エンコーダーセーフリストをカスタマイズして、起動時にアプリケーションに適した Unicode 範囲をに含めることができ `ConfigureServices()` ます。

たとえば、既定の構成を使用する場合は、 Razor 次のように htmlhelper を使用します。

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Web ページのソースを表示すると、次のように、中国語のテキストがエンコードされた状態で表示されます。

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

エンコーダーによって安全として扱われる文字を拡大するには、のメソッドに次の行を挿入し `ConfigureServices()` `startup.cs` ます。

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

この例では、セーフリストを拡大して、CjkUnifiedIdeographs という Unicode 範囲を追加しています。 レンダリングされた出力は次のようになります。

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

セーフリストの範囲は、言語ではなく、Unicode コードグラフとして指定されます。 [Unicode 規格](https://unicode.org/)には、文字を含むグラフを検索するために使用できる[コード表](https://www.unicode.org/charts/index.html)の一覧があります。 各エンコーダー、Html、JavaScript、および Url は、個別に構成する必要があります。

> [!NOTE]
> セーフリストのカスタマイズは、DI によって供給されるエンコーダーにのみ影響します。 既定のを使用してエンコーダーに直接アクセスした場合は `System.Text.Encodings.Web.*Encoder.Default` 、基本的なラテン only セーフセーフセーフポイントが使用されます。

## <a name="where-should-encoding-take-place"></a>エンコードを行う場所

一般に、エンコードは出力時に行われ、エンコードされた値をデータベースに格納しないようにすることをお勧めします。 出力の時点でエンコードを使用すると、たとえば HTML からクエリ文字列の値まで、データの使用を変更することができます。 また、検索の前に値をエンコードしなくてもデータを簡単に検索でき、エンコーダーに加えられた変更やバグ修正を利用できます。

## <a name="validation-as-an-xss-prevention-technique"></a>XSS 防止手法としての検証

検証は、XSS 攻撃を制限するうえで役に立つツールです。 たとえば、文字0-9 のみを含む数値文字列は、XSS 攻撃をトリガーしません。 ユーザー入力で HTML を受け入れると、検証がより複雑になります。 HTML 入力の解析は、不可能な場合には困難です。 Markdown は、埋め込まれた HTML を取り除くパーサーと組み合わせることで、リッチな入力を受け入れるためのより安全な選択肢となります。 検証だけに依存しない。 検証やサニタイズが実行されているかどうかにかかわらず、常に信頼されていない入力を出力の前にエンコードします。
