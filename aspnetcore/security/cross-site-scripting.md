---
title: Impedir XSS (script entre sites) no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre scripts entre sites (XSS) e técnicas para abordar essa vulnerabilidade em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667975"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Impedir XSS (script entre sites) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

O XSS (script entre sites) é uma vulnerabilidade de segurança que permite que um invasor coloque scripts do lado do cliente (geralmente JavaScript) em páginas da Web. Quando outros usuários carregam páginas afetadas, os scripts do invasor serão executados, permitindo que o invasor roube cookies e tokens de sessão, altere o conteúdo da página da Web por meio da manipulação DOM ou redirecione o navegador para outra página. As vulnerabilidades de XSS geralmente ocorrem quando um aplicativo usa a entrada do usuário e a envia para uma página sem validar, codificar ou escapar dela.

## <a name="protecting-your-application-against-xss"></a>Protegendo seu aplicativo contra XSS

Em um nível básico, o XSS funciona ao enganar seu aplicativo para inserir uma marca de `<script>` na página renderizada ou inserindo um evento de `On*` em um elemento. Os desenvolvedores devem usar as seguintes etapas de prevenção para evitar a introdução de XSS em seus aplicativos.

1. Nunca coloque dados não confiáveis em sua entrada HTML, a menos que você siga o restante das etapas abaixo. Os dados não confiáveis são todos os dados que podem ser controlados por um invasor, entradas de formulário HTML, cadeias de caracteres de consulta, cabeçalhos HTTP, até mesmo dados provenientes de um banco de dado como um invasor pode violar seu banco mesmo se não puderem violar seu aplicativo.

2. Antes de colocar dados não confiáveis dentro de um elemento HTML, verifique se ele é codificado em HTML. A codificação HTML usa caracteres como &lt; e os altera em um formato seguro, como &amp;lt;

3. Antes de colocar dados não confiáveis em um atributo HTML, verifique se ele é codificado em HTML. A codificação de atributo HTML é um superconjunto de codificação HTML e codifica caracteres adicionais como "e".

4. Antes de colocar dados não confiáveis no JavaScript, coloque os dados em um elemento HTML cujo conteúdo você recupera no tempo de execução. Se isso não for possível, verifique se os dados são codificados em JavaScript. A codificação JavaScript leva caracteres perigosos para o JavaScript e os substitui por seu Hex, por exemplo &lt; seria codificada como `\u003C`.

5. Antes de colocar dados não confiáveis em uma cadeia de caracteres de consulta de URL, verifique se ela é codificada em URL.

## <a name="html-encoding-using-razor"></a>Codificação HTML usando o Razor

O mecanismo Razor usado no MVC codifica automaticamente todas as saídas originadas de variáveis, a menos que você trabalhe muito difícil para impedi-lo de fazer isso. Ele usa regras de codificação de atributo HTML sempre que você usa a diretiva *@* . Como a codificação de atributo HTML é um superconjunto de codificação HTML isso significa que você não precisa se preocupar com a possibilidade de usar codificação HTML ou codificação de atributo HTML. Você deve garantir que use apenas @ em um contexto HTML, não ao tentar inserir uma entrada não confiável diretamente no JavaScript. Os auxiliares de marca também codificam a entrada usada nos parâmetros de marca.

Faça o seguinte modo de exibição do Razor:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Essa exibição gera o conteúdo da variável *untrustedInput* . Essa variável inclui alguns caracteres que são usados em ataques XSS, a saber &lt;, "e &gt;. Examinar a origem mostra a saída renderizada codificada como:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC fornece uma classe `HtmlString` que não é codificada automaticamente na saída. Isso nunca deve ser usado em combinação com a entrada não confiável, pois isso vai expor uma vulnerabilidade de XSS.

## <a name="javascript-encoding-using-razor"></a>Codificação JavaScript usando Razor

Pode haver ocasiões em que você deseja inserir um valor em JavaScript para processar em sua exibição. Existem duas maneiras para isso A maneira mais segura de inserir valores é inserir o valor em um atributo de dados de uma marca e recuperá-lo em seu JavaScript. Por exemplo:

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

Isso produzirá o seguinte HTML

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

Que, quando executado, processará o seguinte:

```
<"123">
   <"123">
```

Você também pode chamar o codificador JavaScript diretamente:

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
> Não concatene a entrada não confiável em JavaScript para criar elementos DOM. Você deve usar `createElement()` e atribuir valores de propriedade adequadamente, como `node.TextContent=`, ou usar `element.SetAttribute()`/`element[attribute]=` caso contrário, você se exporá a XSS baseado em DOM.

## <a name="accessing-encoders-in-code"></a>Acessando codificadores no código

Os codificadores HTML, JavaScript e URL estão disponíveis para seu código de duas maneiras, você pode injeta-los por meio de [injeção de dependência](xref:fundamentals/dependency-injection) ou pode usar os codificadores padrão contidos no namespace `System.Text.Encodings.Web`. Se você usar os codificadores padrão, qualquer um que você aplicou aos intervalos de caracteres a serem tratados como seguros não entrará em vigor – os codificadores padrão usam as regras de codificação mais seguras possíveis.

Para usar os codificadores configuráveis por meio de DI, seus construtores devem pegar um parâmetro de *HtmlEncode*, *JavaScriptEncoder* e *urlencoder* conforme apropriado. Por exemplo:

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

## <a name="encoding-url-parameters"></a>Parâmetros de URL de codificação

Se você quiser criar uma cadeia de caracteres de consulta de URL com entrada não confiável como um valor, use o `UrlEncoder` para codificar o valor. Por exemplo,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Após a codificação, a variável EncodedValue conterá `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Espaços, aspas, pontuação e outros caracteres não seguros serão codificados por porcentagem para seu valor hexadecimal, por exemplo, um caractere de espaço se tornará %20.

>[!WARNING]
> Não use a entrada não confiável como parte de um caminho de URL. Sempre passe uma entrada não confiável como um valor de cadeia de caracteres de consulta.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personalizando os codificadores

Por padrão, os codificadores usam uma lista segura limitada ao intervalo Unicode latino básico e codificam todos os caracteres fora desse intervalo como seus equivalentes de código de caractere. Esse comportamento também afeta a renderização Razor TagHelper e HtmlHelper, pois usará os codificadores para gerar suas cadeias de caracteres.

O raciocínio por trás disso é proteger contra bugs de navegador desconhecidos ou futuros (os bugs anteriores do navegador acabaram analisando com base no processamento de caracteres que não são do inglês). Se o seu site faz uso intensivo de caracteres não latinos, como chinês, cirílico ou outros, isso provavelmente não é o comportamento desejado.

Você pode personalizar as listas de codificador com segurança para incluir intervalos Unicode apropriados ao seu aplicativo durante a inicialização, em `ConfigureServices()`.

Por exemplo, usando a configuração padrão, você pode usar um HtmlHelper Razor como esse;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Quando você exibir a origem da página da Web, verá que ela foi renderizada da seguinte maneira, com o texto codificado em Chinês;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Para ampliar os caracteres tratados como seguros pelo codificador, você inseriria a linha a seguir no método `ConfigureServices()` em `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Este exemplo amplia a lista segura para incluir o intervalo Unicode CjkUnifiedIdeographs. A saída renderizada agora se tornaria

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Os intervalos de lista segura são especificados como gráficos de código Unicode, não idiomas. O [padrão Unicode](https://unicode.org/) tem uma lista de [gráficos de código](https://www.unicode.org/charts/index.html) que você pode usar para localizar o gráfico que contém seus caracteres. Cada codificador, HTML, JavaScript e URL deve ser configurado separadamente.

> [!NOTE]
> A personalização da lista segura afeta apenas os codificadores originados por meio de DI. Se você acessar diretamente um codificador por meio de `System.Text.Encodings.Web.*Encoder.Default` o padrão, somente a camada de acesso do latim básico será usada.

## <a name="where-should-encoding-take-place"></a>Onde a codificação deve ocorrer?

A prática aceita geral é que a codificação ocorre no ponto de saída e os valores codificados nunca devem ser armazenados em um banco de dados. A codificação no ponto de saída permite que você altere o uso de dados, por exemplo, de HTML para um valor de cadeia de caracteres de consulta. Ele também permite que você pesquise seus dados facilmente sem precisar codificar valores antes de Pesquisar e permite aproveitar as alterações ou correções de bugs feitas aos codificadores.

## <a name="validation-as-an-xss-prevention-technique"></a>Validação como uma técnica de prevenção de XSS

A validação pode ser uma ferramenta útil para limitar ataques de XSS. Por exemplo, uma cadeia de caracteres numérica contendo apenas os caracteres 0-9 não disparará um ataque XSS. A validação se torna mais complicada ao aceitar HTML na entrada do usuário. A análise da entrada HTML é difícil, se não impossível. A redução, acoplada a um analisador que retira o HTML inserido, é uma opção mais segura para aceitar a entrada avançada. Nunca confie apenas na validação. Sempre codifique a entrada não confiável antes da saída, não importa qual validação ou limpeza foi executada.
