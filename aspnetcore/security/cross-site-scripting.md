---
title: Evitar Cross-Site Scripting (XSS) no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a criação de scripts entre sites (XSS) e técnicas para lidar com essa vulnerabilidade em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 50f0211a2c64708d9b788dd10ce9064e66014d55
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895343"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="28ad5-103">Evitar Cross-Site Scripting (XSS) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28ad5-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="28ad5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28ad5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="28ad5-105">Criação de scripts entre sites (XSS) é uma vulnerabilidade de segurança que permite que um invasor inserir os scripts do lado do cliente (geralmente o JavaScript) em páginas da web.</span><span class="sxs-lookup"><span data-stu-id="28ad5-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="28ad5-106">Quando outros usuários carregar páginas afetadas, scripts do invasor serão executado, permitindo que o invasor roubar cookies e tokens de sessão, altere o conteúdo da página da web por meio de manipulação de DOM ou redirecionar o navegador para outra página.</span><span class="sxs-lookup"><span data-stu-id="28ad5-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="28ad5-107">As vulnerabilidades de XSS geralmente ocorrem quando um aplicativo recebe entrada do usuário e os coloca em uma página sem validação, codificação ou escape-lo.</span><span class="sxs-lookup"><span data-stu-id="28ad5-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="28ad5-108">Proteger seu aplicativo contra o XSS</span><span class="sxs-lookup"><span data-stu-id="28ad5-108">Protecting your application against XSS</span></span>

<span data-ttu-id="28ad5-109">AT um XSS de nível básico funciona por enganar o seu aplicativo para inserir uma `<script>` marca em sua página processada, ou inserindo um `On*` eventos em um elemento.</span><span class="sxs-lookup"><span data-stu-id="28ad5-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="28ad5-110">Os desenvolvedores devem usar as seguintes etapas de prevenção para evitar a introdução de XSS em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="28ad5-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="28ad5-111">Nunca coloque dados não confiáveis em sua entrada HTML, a menos que você siga o restante das etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="28ad5-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="28ad5-112">Dados não confiáveis são todos os dados que podem ser controlados por um invasor, entradas de formulário HTML, cadeias de caracteres de consulta, os cabeçalhos HTTP, até mesmo dados originados de um banco de dados como um invasor pode ser capaz de violar o seu banco de dados, mesmo se eles não podem violar o seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="28ad5-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="28ad5-113">Antes de colocar os dados não confiáveis dentro de um elemento HTML, verifique se que ele é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="28ad5-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="28ad5-114">A codificação HTML usa caracteres, como &lt; e alterá-los em um formulário seguro como &amp;lt;</span><span class="sxs-lookup"><span data-stu-id="28ad5-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="28ad5-115">Antes de colocar os dados não confiáveis em um atributo HTML, verifique se que ele é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="28ad5-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="28ad5-116">Codificação do atributo HTML é um superconjunto de codificação HTML e codifica os caracteres adicionais, como "e '.</span><span class="sxs-lookup"><span data-stu-id="28ad5-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="28ad5-117">Antes de colocar os dados não confiáveis em JavaScript, colocar os dados em um elemento HTML cujo conteúdo você recuperar em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="28ad5-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="28ad5-118">Se isso não for possível, verifique se que os dados são codificado de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="28ad5-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="28ad5-119">Codificação de JavaScript usa caracteres perigosos para JavaScript e os substitui por sua hex, por exemplo &lt; seria codificado como `\u003C`.</span><span class="sxs-lookup"><span data-stu-id="28ad5-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="28ad5-120">Antes de colocar os dados não confiáveis em uma cadeia de caracteres de consulta de URL, verifique se que ele é codificado por URL.</span><span class="sxs-lookup"><span data-stu-id="28ad5-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="28ad5-121">Codificação HTML usando o Razor</span><span class="sxs-lookup"><span data-stu-id="28ad5-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="28ad5-122">O mecanismo Razor usado no MVC automaticamente codifica todos originado de saída de variáveis, a menos que você trabalha muito para impedi-lo ao fazer isso.</span><span class="sxs-lookup"><span data-stu-id="28ad5-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="28ad5-123">Ele usa as regras de codificação HTML atributo sempre que você usar o *@* diretiva.</span><span class="sxs-lookup"><span data-stu-id="28ad5-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="28ad5-124">Como HTML a codificação do atributo é um superconjunto de codificação de HTML, que isso significa que você não precisa se preocupar com se você deve usar a codificação HTML ou codificação do atributo HTML.</span><span class="sxs-lookup"><span data-stu-id="28ad5-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="28ad5-125">Você deve garantir que você só usar em um contexto HTML, não quando a tentativa de inserir entradas não confiáveis diretamente em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="28ad5-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="28ad5-126">Os auxiliares de marca codificará também usar parâmetros de marca de entrada.</span><span class="sxs-lookup"><span data-stu-id="28ad5-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="28ad5-127">Execute o seguinte modo de exibição do Razor:</span><span class="sxs-lookup"><span data-stu-id="28ad5-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="28ad5-128">Essa exibição mostra o conteúdo do *untrustedInput* variável.</span><span class="sxs-lookup"><span data-stu-id="28ad5-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="28ad5-129">Essa variável inclui alguns caracteres que são usadas em ataques de XSS, ou seja, &lt;, "e &gt;.</span><span class="sxs-lookup"><span data-stu-id="28ad5-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="28ad5-130">Examinar o código-fonte mostra a saída renderizada codificada como:</span><span class="sxs-lookup"><span data-stu-id="28ad5-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="28ad5-131">ASP.NET Core MVC fornece uma `HtmlString` classe que não é codificado automaticamente após a saída.</span><span class="sxs-lookup"><span data-stu-id="28ad5-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="28ad5-132">Isso nunca deve ser usado em combinação com entradas não confiáveis, pois isso irá expor uma vulnerabilidade de XSS.</span><span class="sxs-lookup"><span data-stu-id="28ad5-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="28ad5-133">Usando o Razor de codificação do JavaScript</span><span class="sxs-lookup"><span data-stu-id="28ad5-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="28ad5-134">Pode haver ocasiões que você deseja inserir um valor em JavaScript para processar no modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="28ad5-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="28ad5-135">Há duas formas de fazer isso.</span><span class="sxs-lookup"><span data-stu-id="28ad5-135">There are two ways to do this.</span></span> <span data-ttu-id="28ad5-136">A maneira mais segura para inserir valores é colocar o valor em um atributo de dados de uma marca e recuperá-lo em seu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="28ad5-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="28ad5-137">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="28ad5-137">For example:</span></span>

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

<span data-ttu-id="28ad5-138">Isso produzirá o HTML a seguir</span><span class="sxs-lookup"><span data-stu-id="28ad5-138">This will produce the following HTML</span></span>

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

<span data-ttu-id="28ad5-139">Que, quando ele é executado, será renderizado o seguinte:</span><span class="sxs-lookup"><span data-stu-id="28ad5-139">Which, when it runs, will render the following:</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="28ad5-140">Você também pode chamar diretamente o codificador de JavaScript:</span><span class="sxs-lookup"><span data-stu-id="28ad5-140">You can also call the JavaScript encoder directly:</span></span>

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

<span data-ttu-id="28ad5-141">Isso será renderizado no navegador da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="28ad5-141">This will render in the browser as follows:</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="28ad5-142">Não concatene entradas não confiáveis em JavaScript para criar elementos DOM.</span><span class="sxs-lookup"><span data-stu-id="28ad5-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="28ad5-143">Você deve usar `createElement()` e atribuir valores de propriedade adequadamente, como `node.TextContent=`, ou use `element.SetAttribute()` / `element[attribute]=` caso contrário, você se exporá a XSS baseado em DOM.</span><span class="sxs-lookup"><span data-stu-id="28ad5-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="28ad5-144">Acessando codificadores no código</span><span class="sxs-lookup"><span data-stu-id="28ad5-144">Accessing encoders in code</span></span>

<span data-ttu-id="28ad5-145">Os codificadores HTML, JavaScript e a URL estão disponíveis para seu código de duas maneiras, você pode colocá-los por meio [injeção de dependência](xref:fundamentals/dependency-injection) ou você pode usar os codificadores padrão contidos no `System.Text.Encodings.Web` namespace.</span><span class="sxs-lookup"><span data-stu-id="28ad5-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="28ad5-146">Se você usar os codificadores padrão, qualquer aplicadas ao intervalos de caracteres a serem tratados como seguro não terão efeito - os codificadores padrão usam as regras de codificação mais seguras possíveis.</span><span class="sxs-lookup"><span data-stu-id="28ad5-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="28ad5-147">Para usar os codificadores configuráveis por meio da DI seus construtores devem levar uma *HtmlEncoder*, *JavaScriptEncoder* e *UrlEncoder* parâmetro conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="28ad5-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="28ad5-148">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="28ad5-148">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="28ad5-149">Parâmetros de codificação de URL</span><span class="sxs-lookup"><span data-stu-id="28ad5-149">Encoding URL Parameters</span></span>

<span data-ttu-id="28ad5-150">Se você quiser criar uma cadeia de caracteres de consulta de URL com a entrada não confiável como um valor, use o `UrlEncoder` para codificar o valor.</span><span class="sxs-lookup"><span data-stu-id="28ad5-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="28ad5-151">Por exemplo,</span><span class="sxs-lookup"><span data-stu-id="28ad5-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="28ad5-152">Após a codificação de encodedValue variável conterá `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span><span class="sxs-lookup"><span data-stu-id="28ad5-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="28ad5-153">Espaços, aspas, pontuação e outros caracteres não seguros será porcentagem codificado para seu valor hexadecimal, por exemplo, um caractere de espaço tornará % 20.</span><span class="sxs-lookup"><span data-stu-id="28ad5-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="28ad5-154">Não use entradas não confiáveis como parte de um caminho de URL.</span><span class="sxs-lookup"><span data-stu-id="28ad5-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="28ad5-155">Sempre passe a entrada não confiável como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="28ad5-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="28ad5-156">Personalizando os codificadores</span><span class="sxs-lookup"><span data-stu-id="28ad5-156">Customizing the Encoders</span></span>

<span data-ttu-id="28ad5-157">Por padrão, codificadores de usam uma lista segura limitada ao intervalo Unicode de Latim básico e codificar todos os caracteres fora do intervalo como seus equivalentes de código de caractere.</span><span class="sxs-lookup"><span data-stu-id="28ad5-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="28ad5-158">Esse comportamento também afeta a renderização TagHelper Razor e HtmlHelper como ele utilizará os codificadores para suas cadeias de caracteres de saída.</span><span class="sxs-lookup"><span data-stu-id="28ad5-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="28ad5-159">O raciocínio por trás disso é proteger contra bugs no navegador desconhecido ou futuras (bugs no navegador anterior tiveram atropelados análise com base no processamento de caracteres do inglês).</span><span class="sxs-lookup"><span data-stu-id="28ad5-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="28ad5-160">Se seu site da web faz uso intenso de caracteres não latinos, como o chinês, cirílico ou outras pessoas isso provavelmente não é o comportamento desejado.</span><span class="sxs-lookup"><span data-stu-id="28ad5-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="28ad5-161">Você pode personalizar as listas de seguro de codificador para incluir Unicode intervalos apropriadas para seu aplicativo durante a inicialização, no `ConfigureServices()`.</span><span class="sxs-lookup"><span data-stu-id="28ad5-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="28ad5-162">Por exemplo, usando a configuração padrão que você pode usar um HtmlHelper Razor assim;</span><span class="sxs-lookup"><span data-stu-id="28ad5-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="28ad5-163">Quando você exibir a origem da página da web, você verá que ele foi renderizado da seguinte maneira, com o texto codificado; em chinês</span><span class="sxs-lookup"><span data-stu-id="28ad5-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="28ad5-164">Ampliar os caracteres tratados como seguro pelo codificador você inseriria a linha a seguir para o `ConfigureServices()` método no `startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="28ad5-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="28ad5-165">Este exemplo amplia a lista segura para incluir o CjkUnifiedIdeographs de intervalo Unicode.</span><span class="sxs-lookup"><span data-stu-id="28ad5-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="28ad5-166">A saída renderizada tornaria</span><span class="sxs-lookup"><span data-stu-id="28ad5-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="28ad5-167">Intervalos de lista segura são especificados como gráficos de código Unicode, não os idiomas.</span><span class="sxs-lookup"><span data-stu-id="28ad5-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="28ad5-168">O [padrão Unicode](http://unicode.org/) tem uma lista de [gráficos de código](http://www.unicode.org/charts/index.html) você pode usar para localizar o gráfico que contém os caracteres.</span><span class="sxs-lookup"><span data-stu-id="28ad5-168">The [Unicode standard](http://unicode.org/) has a list of [code charts](http://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="28ad5-169">Cada codificador, Html, JavaScript e a Url, deve ser configurado separadamente.</span><span class="sxs-lookup"><span data-stu-id="28ad5-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="28ad5-170">Personalização da lista de confiáveis afeta apenas os codificadores originados por meio da DI.</span><span class="sxs-lookup"><span data-stu-id="28ad5-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="28ad5-171">Se você acessar diretamente um codificador via `System.Text.Encodings.Web.*Encoder.Default` , em seguida, o padrão, Latim básico somente lista segura será usada.</span><span class="sxs-lookup"><span data-stu-id="28ad5-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="28ad5-172">Onde a codificação take deve colocar?</span><span class="sxs-lookup"><span data-stu-id="28ad5-172">Where should encoding take place?</span></span>

<span data-ttu-id="28ad5-173">Em geral aceito prática é que a codificação ocorre no ponto de saída e valores codificados nunca devem ser armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="28ad5-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="28ad5-174">Codificação no ponto de saída permite que você altere o uso de dados, por exemplo, de HTML para um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="28ad5-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="28ad5-175">Ele também permite que você pesquise facilmente seus dados sem a necessidade de codificar valores antes de pesquisar e permite que você se beneficie de quaisquer alterações ou correções de bug feitas aos codificadores.</span><span class="sxs-lookup"><span data-stu-id="28ad5-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="28ad5-176">Validação como uma técnica de prevenção de XSS</span><span class="sxs-lookup"><span data-stu-id="28ad5-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="28ad5-177">Validação pode ser uma ferramenta útil na limitação de ataques de XSS.</span><span class="sxs-lookup"><span data-stu-id="28ad5-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="28ad5-178">Por exemplo, uma cadeia de caracteres numérica que contém somente os caracteres de 0 a 9 não disparar um ataque XSS.</span><span class="sxs-lookup"><span data-stu-id="28ad5-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="28ad5-179">Validação se torna mais complicada ao aceitar HTML na entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="28ad5-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="28ad5-180">Analisar a entrada em HTML é difícil, se não impossível.</span><span class="sxs-lookup"><span data-stu-id="28ad5-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="28ad5-181">Markdown, juntamente com um analisador que retira HTML incorporado, é uma opção mais segura para aceitar a entrada avançada.</span><span class="sxs-lookup"><span data-stu-id="28ad5-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="28ad5-182">Nunca confie em validação sozinha.</span><span class="sxs-lookup"><span data-stu-id="28ad5-182">Never rely on validation alone.</span></span> <span data-ttu-id="28ad5-183">Sempre codificar entradas não confiáveis antes da saída, não importa quais validação ou limpeza foi executada.</span><span class="sxs-lookup"><span data-stu-id="28ad5-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
