---
title: Formatar dados de resposta na API Web ASP.NET Core
author: ardalis
description: Saiba como formatar dados de resposta na API Web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 05/29/2019
uid: web-api/advanced/formatting
ms.openlocfilehash: 8bee4efdae5341ddab5bd3aec278ecfef37f0c08
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082354"
---
<!-- DO NOT EDIT BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12077 MERGES -->
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatar dados de resposta na API Web ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

O ASP.NET Core MVC tem suporte interno para formatação de dados de resposta, usando formatos fixos ou em resposta às especificações do cliente.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Resultados da ação específicos a um formato

Alguns tipos de resultado de ação são específicos a um formato específico, como `JsonResult` e `ContentResult`. As ações podem retornar resultados específicos que são sempre formatados de determinada maneira. Por exemplo, o retorno de um `JsonResult` retornará dados formatados em JSON, independentemente das preferências do cliente. Da mesma forma, o retorno de um `ContentResult` retornará dados de cadeia de caracteres formatados como texto sem formatação (assim como o simples retorno de uma cadeia de caracteres).

> [!NOTE]
> Uma ação não precisa retornar nenhum tipo específico; o MVC dá suporte a qualquer valor retornado de objeto. Se uma ação retorna uma implementação `IActionResult` e o controlador herda de `Controller`, os desenvolvedores têm muitos métodos auxiliares correspondentes a muitas das opções. Os resultados de ações que retornam objetos que não são tipos `IActionResult` serão serializados usando a implementação `IOutputFormatter` apropriada.

Para retornar dados em um formato específico de um controlador que herda da classe base `Controller`, use o método auxiliar interno `Json` para retornar JSON e `Content` para texto sem formatação. O método de ação deve retornar o tipo de resultado específico (por exemplo, `JsonResult`) ou `IActionResult`.

Retornando dados formatados em JSON:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

Resposta de exemplo desta ação:

![Guia Rede das Ferramentas para Desenvolvedores no Microsoft Edge mostrando que o Tipo de conteúdo da resposta é application/json](formatting/_static/json-response.png)

Observe que o tipo de conteúdo da resposta é `application/json`, conforme mostrado na lista de solicitações de rede e na seção Cabeçalhos de Resposta. Além disso, observe a lista de opções apresentada pelo navegador (nesse caso, Microsoft Edge) no cabeçalho Accept da seção Cabeçalhos de Solicitação. A técnica atual é ignorar esse cabeçalho. Abaixo, abordamos como obedecê-lo.

Para retornar dados formatados como texto sem formatação, use `ContentResult` e o auxiliar `Content`:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

Uma resposta dessa ação:

![Guia Rede das Ferramentas para Desenvolvedores no Microsoft Edge mostrando que o Tipo de conteúdo da resposta é text/plain](formatting/_static/text-response.png)

Observe, neste caso, que o `Content-Type` retornado é `text/plain`. Também obtenha esse mesmo comportamento usando apenas um tipo de resposta de cadeia de caracteres:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> Para ações não triviais com vários tipos de retorno ou várias opções (por exemplo, códigos de status HTTP diferentes com base no resultado das operações executadas), prefira `IActionResult` como o tipo de retorno.

## <a name="content-negotiation"></a>Negociação de conteúdo

A negociação de conteúdo (*conneg* de forma abreviada) ocorre quando o cliente especifica um [cabeçalho Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). O formato padrão usado pelo ASP.NET Core MVC é JSON. A negociação de conteúdo é implementada por `ObjectResult`. Ela também foi desenvolvida com base nos resultados de ação específica de código de status retornados dos métodos auxiliares (que se baseiam em `ObjectResult`). Também retorne um tipo de modelo (uma classe que você definiu como o tipo de transferência de dados) e a estrutura o encapsulará automaticamente em um `ObjectResult` para você.

O seguinte método de ação usa os métodos auxiliares `Ok` e `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

Uma resposta formatada em JSON será retornada, a menos que outro formato tenha sido solicitado e o servidor possa retornar o formato solicitado. Use uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) para criar uma solicitação que inclui um cabeçalho Accept e especifique outro formato. Nesse caso, se o servidor tiver um *formatador* que pode produzir uma resposta no formato solicitado, o resultado será retornado no formato preferencial do cliente.

![Console do Fiddler mostrando uma solicitação GET criada manualmente com o valor application/xml do cabeçalho Accept](formatting/_static/fiddler-composer.png)

Na captura de tela acima, o Fiddler Composer foi usado para gerar uma solicitação, especificando `Accept: application/xml`. Por padrão, o ASP.NET Core MVC dá suporte apenas ao JSON e, portanto, mesmo quando outro formato é especificado, o resultado retornado ainda é formatado em JSON. Você verá como adicionar outros formatadores na próxima seção.

As ações do controlador podem retornar POCOs (Objetos CLR Básicos); nesse caso, o ASP.NET Core MVC criará automaticamente um `ObjectResult` que encapsula o objeto. O cliente receberá o objeto serializado formatado (o formato JSON é o padrão; você pode configurar XML ou outros formatos). Se o objeto que está sendo retornado for `null`, a estrutura retornará uma resposta `204 No Content`.

Retornando um tipo de objeto:

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

Na amostra, uma solicitação para um alias de autor válido receberá uma resposta 200 OK com os dados do autor. Uma solicitação para um alias inválido receberá uma resposta 204 Sem Conteúdo. Capturas de tela mostrando a resposta nos formatos XML e JSON são mostradas abaixo.

### <a name="content-negotiation-process"></a>Processo de negociação de conteúdo

A *negociação* de conteúdo ocorre apenas se um cabeçalho `Accept` é exibido na solicitação. Quando uma solicitação contiver um cabeçalho Accept, a estrutura enumerará os tipos de mídia no cabeçalho Accept na ordem de preferência e tentará encontrar um formatador que pode produzir uma resposta em um dos formatos especificados pelo cabeçalho Accept. Caso nenhum formatador que pode atender à solicitação do cliente seja encontrado, a estrutura tentará encontrar o primeiro formatador que pode produzir uma resposta (a menos que o desenvolvedor tenha configurado a opção em `MvcOptions` para retornar 406 Não Aceitável). Se a solicitação especificar XML, mas o formatador XML não tiver sido configurado, o formatador JSON será usado. Geralmente, se nenhum formatador que pode fornecer o formato solicitado for configurado, o primeiro formatador que pode formatar o objeto será usado. Se nenhum cabeçalho for fornecido, o primeiro formatador que pode manipular o objeto a ser retornado será usado para serializar a resposta. Nesse caso, não ocorre nenhuma negociação – o servidor determina qual formato será usado.

> [!NOTE]
> Se o cabeçalho Accept contiver `*/*`, o Cabeçalho será ignorado, a menos que `RespectBrowserAcceptHeader` seja definido como verdadeiro em `MvcOptions`.

### <a name="browsers-and-content-negotiation"></a>Navegadores e negociação de conteúdo

Ao contrário dos clientes de API típicos, os navegadores da Web tendem a fornecer cabeçalhos `Accept` que incluem uma ampla variedade de formatos, incluindo caracteres curinga. Por padrão, quando a estrutura detectar que a solicitação é proveniente de um navegador, ela ignorará o cabeçalho `Accept` e retornará o conteúdo no formato padrão configurado do aplicativo (JSON, a menos que outro formato seja configurado). Isso fornece uma experiência mais consistente no uso de diferentes navegadores para consumir APIs.

Se preferir que o aplicativo respeite os cabeçalhos Accept do navegador, defina isso como parte da configuração do MVC definindo `RespectBrowserAcceptHeader` como `true` no método `ConfigureServices` em *Startup.cs*.

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a>Configurando formatadores

Se o aplicativo precisar dar suporte a outros formatos além do padrão de JSON, adicione pacotes NuGet e configure o MVC para dar suporte a eles. Há formatadores separados para entrada e saída. Os formatadores de entrada são usados pelo [Model Binding](xref:mvc/models/model-binding); os formatadores de saída são usados para formatar as respostas. Também configure [Formatadores Personalizados](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="configure-systemtextjson-based-formatters"></a>Configurar formatadores com base em System.Text.Json

Os recursos do formatadores com base em `System.Text.Json` podem ser configurados, usando `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.SerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, em uma base por ação, podem ser `JsonResult`configuradas usando. Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Adicionar suporte ao formato JSON com base em Newtonsoft.Json

Antes do ASP.NET Core 3.0, o MVC assumia como padrão usar formatadores JSON implementados, usando o pacote `Newtonsoft.Json`. No ASP.NET Core 3.0 ou posterior, os formatadores JSON padrão baseiam-se no `System.Text.Json`. O suporte para formatadores e recursos baseados em `Newtonsoft.Json` está disponível por meio da instalação do pacote NuGet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) e da configuração dele no `Startup.ConfigureServices`.

```csharp
services.AddControllers()
    .AddNewtonsoftJson();
```

Alguns recursos podem não funcionar bem com formatadores baseados em `System.Text.Json` e exigem uma referência aos formatadores baseados em `Newtonsoft.Json` para a versão do ASP.NET Core 3.0. Continue a usar os formatadores baseados em `Newtonsoft.Json`, se seu aplicativo for o ASP.NET Core 3.0 ou posterior:

* Use os atributos `Newtonsoft.Json` (por exemplo, `[JsonProperty]` ou `[JsonIgnore]`), personalize as configurações de serialização ou conte com os recursos fornecidos por `Newtonsoft.Json`.
* Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Antes do ASP.NET Core 3.0, `JsonResult.SerializerSettings` aceita uma instância de `JsonSerializerSettings` que é específico para `Newtonsoft.Json`.
* Gera documentação [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Os recursos para `Newtonsoft.Json`os formatadores baseados em podem ser configurados usando: `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefautlContractResolver();

    // Configure a custom converter
    options.SerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

As opções de serialização de saída, em uma base por ação, podem ser `JsonResult`configuradas usando. Por exemplo:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

### <a name="add-xml-format-support"></a>Adicionar suporte ao formato XML

::: moniker range="<= aspnetcore-2.2"

Para adicionar suporte à formatação XML ao ASP.NET Core 2.2 ou anterior, instale o pacote do NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).

::: moniker-end

Os formatadores XML implementados usando `System.Xml.Serialization.XmlSerializer` podem ser configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> em `Startup.ConfigureServices`:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

Como alternativa, os formatadores XML implementados, usando `System.Runtime.Serialization.DataContractSerializer`, podem ser configurados chamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlDataContractSerializerFormatters*> em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .AddXmlDataContractSerializerFormatters();
```

Depois de adicionar suporte para a formatação XML, os métodos do controlador deverão retornar o formato apropriado com base no cabeçalho `Accept` da solicitação, como demonstra este exemplo do Fiddler:

![Console do Fiddler: a guia Bruto da solicitação mostra que o valor do cabeçalho Accept é application/xml. A guia Bruto da resposta mostra o valor application/xml do cabeçalho Content-Type.](formatting/_static/xml-response.png)

Na guia Inspetores, é possível ver que a solicitação GET Bruta foi feita com um conjunto de cabeçalhos `Accept: application/xml`. O painel de resposta mostra o cabeçalho `Content-Type: application/xml` e o objeto `Author` foi serializado em XML.

Use a guia Criador para modificar a solicitação para especificar `application/json` no cabeçalho `Accept`. Execute a solicitação e a resposta será formatada como JSON:

![Console do Fiddler: a guia Bruto da solicitação mostra que o valor do cabeçalho Accept é application/json. A guia Bruto da resposta mostra o valor application/json do cabeçalho Content-Type.](formatting/_static/json-response-fiddler.png)

Nessa captura de tela, é possível ver a solicitação definir um cabeçalho `Accept: application/json` e a resposta especificar o mesmo como seu `Content-Type`. O objeto `Author` é mostrado no corpo da resposta, em formato JSON.

### <a name="forcing-a-particular-format"></a>Forçando um formato específico

Caso deseje restringir os formatos de resposta de uma ação específica, aplique o filtro `[Produces]`. O filtro `[Produces]` especifica os formatos de resposta de uma ação específica (ou o controlador). Como a maioria dos [Filtros](xref:mvc/controllers/filters), isso pode ser aplicado no escopo da ação, do controlador ou global.

```csharp
[Produces("application/json")]
public class AuthorsController
```

O filtro `[Produces]` forçará todas as ações em `AuthorsController` a retornar respostas formatadas em JSON, mesmo se outros formatadores foram configurados para o aplicativo e o cliente forneceu um cabeçalho `Accept` solicitando outro formato disponível. Consulte [Filtros](xref:mvc/controllers/filters) para saber mais, incluindo como aplicar filtros globalmente.

### <a name="special-case-formatters"></a>Formatadores de casos especiais

Alguns casos especiais são implementados com formatadores internos. Por padrão, os tipos de retorno `string` serão formatados como *text/plain* (*text/html*, se solicitado por meio do cabeçalho `Accept`). Esse comportamento pode ser removido com a remoção do `TextOutputFormatter`. Remova formatadores no método `Configure` em *Startup.cs* (mostrado abaixo). Ações que têm um tipo de retorno de objeto de modelo retornarão uma resposta 204 Sem Conteúdo ao retornar `null`. Esse comportamento pode ser removido com a remoção do `HttpNoContentOutputFormatter`. O código a seguir remove o `TextOutputFormatter` e o `HttpNoContentOutputFormatter`.

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

Sem o `TextOutputFormatter`, os tipos de retorno `string` retornam 406 Não Aceitável, por exemplo. Observe que se um formatador XML existir, ele formatará tipos de retorno `string` se o `TextOutputFormatter` for removido.

Sem o `HttpNoContentOutputFormatter`, os objetos nulos são formatados com o formatador configurado. Por exemplo, o formatador JSON apenas retornará uma resposta com um corpo `null`, enquanto o formatador XML retornará um elemento XML vazio com o atributo `xsi:nil="true"` definido.

## <a name="response-format-url-mappings"></a>Mapeamentos de URL do formato da resposta

Os clientes podem solicitar um formato específico como parte da URL, como na cadeia de caracteres de consulta ou parte do caminho, ou usando uma extensão de arquivo específica a um formato, como .xml ou .json. O mapeamento do caminho da solicitação deve ser especificado na rota que está sendo usada pela API. Por exemplo:

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

Essa rota permitirá que o formato solicitado seja especificado como uma extensão de arquivo opcional. O atributo `[FormatFilter]` verifica a existência do valor de formato no `RouteData` e mapeará o formato da resposta para o formatador adequado quando a resposta for criada.

|           Rota            |             Formatador              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    O formatador de saída padrão    |
| `/products/GetById/5.json` | O formatador JSON (se configurado) |
| `/products/GetById/5.xml`  | O formatador XML (se configurado)  |
