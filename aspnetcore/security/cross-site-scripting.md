---
title: Evitar Cross-Site Scripting (XSS) no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a criação de scripts entre sites (XSS) e técnicas para lidar com essa vulnerabilidade em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1e9e988be68313cfd493832519c1be89335d6e48
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815203"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Evitar Cross-Site Scripting (XSS) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Criação de scripts entre sites (XSS) é uma vulnerabilidade de segurança que permite que um invasor inserir os scripts do lado do cliente (geralmente o JavaScript) em páginas da web. Quando outros usuários carregar páginas afetadas, scripts do invasor serão executado, permitindo que o invasor roubar cookies e tokens de sessão, altere o conteúdo da página da web por meio de manipulação de DOM ou redirecionar o navegador para outra página. As vulnerabilidades de XSS geralmente ocorrem quando um aplicativo recebe entrada do usuário e os coloca em uma página sem validação, codificação ou escape-lo.

## <a name="protecting-your-application-against-xss"></a>Proteger seu aplicativo contra o XSS

AT um XSS de nível básico funciona por enganar o seu aplicativo para inserir uma `<script>` marca em sua página processada, ou inserindo um `On*` eventos em um elemento. Os desenvolvedores devem usar as seguintes etapas de prevenção para evitar a introdução de XSS em seu aplicativo.

1. Nunca coloque dados não confiáveis em sua entrada HTML, a menos que você siga o restante das etapas a seguir. Dados não confiáveis são todos os dados que podem ser controlados por um invasor, entradas de formulário HTML, cadeias de caracteres de consulta, os cabeçalhos HTTP, até mesmo dados originados de um banco de dados como um invasor pode ser capaz de violar o seu banco de dados, mesmo se eles não podem violar o seu aplicativo.

2. Antes de colocar os dados não confiáveis dentro de um elemento HTML, verifique se que ele é codificado em HTML. A codificação HTML usa caracteres, como &lt; e alterá-los em um formulário seguro como &amp;lt;

3. Antes de colocar os dados não confiáveis em um atributo HTML, verifique se que ele é codificado em HTML. Codificação do atributo HTML é um superconjunto de codificação HTML e codifica os caracteres adicionais, como "e '.

4. Antes de colocar os dados não confiáveis em JavaScript, colocar os dados em um elemento HTML cujo conteúdo você recuperar em tempo de execução. Se isso não for possível, verifique se que os dados são codificado de JavaScript. Codificação de JavaScript usa caracteres perigosos para JavaScript e os substitui por sua hex, por exemplo &lt; seria codificado como `\u003C`.

5. Antes de colocar os dados não confiáveis em uma cadeia de caracteres de consulta de URL, verifique se que ele é codificado por URL.

## <a name="html-encoding-using-razor"></a>Codificação HTML usando o Razor

O mecanismo Razor usado no MVC automaticamente codifica todos originado de saída de variáveis, a menos que você trabalha muito para impedi-lo ao fazer isso. Ele usa as regras de codificação HTML atributo sempre que você usar o *@* diretiva. Como HTML a codificação do atributo é um superconjunto de codificação de HTML, que isso significa que você não precisa se preocupar com se você deve usar a codificação HTML ou codificação do atributo HTML. Você deve garantir que você só usar em um contexto HTML, não quando a tentativa de inserir entradas não confiáveis diretamente em JavaScript. Os auxiliares de marca codificará também usar parâmetros de marca de entrada.

Execute o seguinte modo de exibição do Razor:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Essa exibição mostra o conteúdo do *untrustedInput* variável. Essa variável inclui alguns caracteres que são usadas em ataques de XSS, ou seja, &lt;, "e &gt;. Examinar o código-fonte mostra a saída renderizada codificada como:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC fornece uma `HtmlString` classe que não é codificado automaticamente após a saída. Isso nunca deve ser usado em combinação com entradas não confiáveis, pois isso irá expor uma vulnerabilidade de XSS.

## <a name="javascript-encoding-using-razor"></a>Usando o Razor de codificação do JavaScript

Pode haver ocasiões que você deseja inserir um valor em JavaScript para processar no modo de exibição. Há duas formas de fazer isso. A maneira mais segura para inserir valores é colocar o valor em um atributo de dados de uma marca e recuperá-lo em seu JavaScript. Por exemplo:

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

Isso produzirá o HTML a seguir

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

Que, quando ele é executado, será renderizado o seguinte:

```none
<"123">
   <"123">
   ```

Você também pode chamar diretamente o codificador de JavaScript:

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

Isso será renderizado no navegador da seguinte maneira:

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> Não concatene entradas não confiáveis em JavaScript para criar elementos DOM. Você deve usar `createElement()` e atribuir valores de propriedade adequadamente, como `node.TextContent=`, ou use `element.SetAttribute()` / `element[attribute]=` caso contrário, você se exporá a XSS baseado em DOM.

## <a name="accessing-encoders-in-code"></a>Acessando codificadores no código

Os codificadores HTML, JavaScript e a URL estão disponíveis para seu código de duas maneiras, você pode colocá-los por meio [injeção de dependência](xref:fundamentals/dependency-injection) ou você pode usar os codificadores padrão contidos no `System.Text.Encodings.Web` namespace. Se você usar os codificadores padrão, qualquer aplicadas ao intervalos de caracteres a serem tratados como seguro não terão efeito - os codificadores padrão usam as regras de codificação mais seguras possíveis.

Para usar os codificadores configuráveis por meio da DI seus construtores devem levar uma *HtmlEncoder*, *JavaScriptEncoder* e *UrlEncoder* parâmetro conforme apropriado. Por exemplo:

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

## <a name="encoding-url-parameters"></a>Parâmetros de codificação de URL

Se você quiser criar uma cadeia de caracteres de consulta de URL com a entrada não confiável como um valor, use o `UrlEncoder` para codificar o valor. Por exemplo,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Após a codificação de encodedValue variável conterá `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Espaços, aspas, pontuação e outros caracteres não seguros será porcentagem codificado para seu valor hexadecimal, por exemplo, um caractere de espaço tornará % 20.

>[!WARNING]
> Não use entradas não confiáveis como parte de um caminho de URL. Sempre passe a entrada não confiável como um valor de cadeia de caracteres de consulta.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personalizando os codificadores

Por padrão, codificadores de usam uma lista segura limitada ao intervalo Unicode de Latim básico e codificar todos os caracteres fora do intervalo como seus equivalentes de código de caractere. Esse comportamento também afeta a renderização TagHelper Razor e HtmlHelper como ele utilizará os codificadores para suas cadeias de caracteres de saída.

O raciocínio por trás disso é proteger contra bugs no navegador desconhecido ou futuras (bugs no navegador anterior tiveram atropelados análise com base no processamento de caracteres do inglês). Se seu site da web faz uso intenso de caracteres não latinos, como o chinês, cirílico ou outras pessoas isso provavelmente não é o comportamento desejado.

Você pode personalizar as listas de seguro de codificador para incluir Unicode intervalos apropriadas para seu aplicativo durante a inicialização, no `ConfigureServices()`.

Por exemplo, usando a configuração padrão que você pode usar um HtmlHelper Razor assim;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Quando você exibir a origem da página da web, você verá que ele foi renderizado da seguinte maneira, com o texto codificado; em chinês

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Ampliar os caracteres tratados como seguro pelo codificador você inseriria a linha a seguir para o `ConfigureServices()` método no `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Este exemplo amplia a lista segura para incluir o CjkUnifiedIdeographs de intervalo Unicode. A saída renderizada tornaria

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Intervalos de lista segura são especificados como gráficos de código Unicode, não os idiomas. O [padrão Unicode](https://unicode.org/) tem uma lista de [gráficos de código](https://www.unicode.org/charts/index.html) você pode usar para localizar o gráfico que contém os caracteres. Cada codificador, Html, JavaScript e a Url, deve ser configurado separadamente.

> [!NOTE]
> Personalização da lista de confiáveis afeta apenas os codificadores originados por meio da DI. Se você acessar diretamente um codificador via `System.Text.Encodings.Web.*Encoder.Default` , em seguida, o padrão, Latim básico somente lista segura será usada.

## <a name="where-should-encoding-take-place"></a>Onde a codificação take deve colocar?

Em geral aceito prática é que a codificação ocorre no ponto de saída e valores codificados nunca devem ser armazenados em um banco de dados. Codificação no ponto de saída permite que você altere o uso de dados, por exemplo, de HTML para um valor de cadeia de caracteres de consulta. Ele também permite que você pesquise facilmente seus dados sem a necessidade de codificar valores antes de pesquisar e permite que você se beneficie de quaisquer alterações ou correções de bug feitas aos codificadores.

## <a name="validation-as-an-xss-prevention-technique"></a>Validação como uma técnica de prevenção de XSS

Validação pode ser uma ferramenta útil na limitação de ataques de XSS. Por exemplo, uma cadeia de caracteres numérica que contém somente os caracteres de 0 a 9 não disparar um ataque XSS. Validação se torna mais complicada ao aceitar HTML na entrada do usuário. Analisar a entrada em HTML é difícil, se não impossível. Markdown, juntamente com um analisador que retira HTML incorporado, é uma opção mais segura para aceitar a entrada avançada. Nunca confie em validação sozinha. Sempre codificar entradas não confiáveis antes da saída, não importa quais validação ou limpeza foi executada.
