---
title: Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core
author: guardrex
description: Descubra como as convenções do provedor de modelo de aplicativo e rota ajudam você a controlar o roteamento, a descoberta e o processamento de página.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724454"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Saiba como usar a página [convenções do provedor de modelo de rota e aplicativo](xref:mvc/controllers/application-model#conventions) para controlar o roteamento, a descoberta e o processamento de páginas nos aplicativos Páginas do Razor.

Quando precisar configurar rotas de página personalizadas para páginas individuais, configure o roteamento para páginas com a [convenção AddPageRoute](#configure-a-page-route) descrita mais adiante neste tópico.

Para especificar uma rota de página, adicione segmentos de rota ou adicionar parâmetros a uma rota, use a página `@page` diretiva. Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes).

Há palavras reservadas não podem ser usadas como segmentos de rota ou nomes de parâmetro. Para obter mais informações, consulte [roteamento: Roteamentos nomes reservados](xref:fundamentals/routing#reserved-routing-names).

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([como baixar](xref:index#how-to-download-a-sample))

| Cenário | A amostra explica... |
| -------- | --------------------------- |
| [Convenções de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Adicione um cabeçalho e um modelo de rota às páginas de um aplicativo. |
| [Convenções de ação da rota de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Adicione um modelo de rota às páginas em uma pasta e a uma única página. |
| [Convenções de ação do modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtro, expressão lambda ou alocador de filtro)</li></ul> | Adicione um cabeçalho às páginas em uma pasta, adicione um cabeçalho a uma única página e configure um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) para adicionar um cabeçalho às páginas de um aplicativo. |

Convenções de páginas Razor são adicionadas e configuradas usando o <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> método de extensão para <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na coleção de serviços no `Startup` classe. Os exemplos de convenção a seguir estão descritos mais adiante neste tópico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>Ordem de rota

As rotas especificam um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para processamento (rota correspondente).

| Pedido            | Comportamento |
| :--------------: | -------- |
| -1               | A rota é processada antes de outras rotas são processadas. |
| 0                | Ordem não for especificada (valor padrão). Não atribui `Order` (`Order = null`) usa como padrão a rota `Order` como 0 (zero) para processamento. |
| 1, 2, &hellip; n | Especifica a ordem de processamento de rota. |

Processamento de rota é estabelecido por convenção:

* As rotas são processadas em ordem sequencial (-1, 0, 1, 2, &hellip; n).
* Quando as rotas têm a mesma `Order`, a maioria dos rota específica é correspondida primeiro, seguido por rotas menos específicas.
* Quando as rotas com o mesmo `Order` e o mesmo número de parâmetros corresponde a uma URL de solicitação, as rotas são processadas na ordem em que eles forem adicionados à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Se possível, evite dependendo de uma ordem de processamento de rota estabelecido. Em geral, o roteamento seleciona a rota correta com a URL correspondente. Se você deve definir a rota `Order` propriedades para rotear solicitações corretamente, o esquema do aplicativo roteamento é provavelmente confuso para os clientes e frágil para manter. Para simplificar o esquema do aplicativo roteamento de busca. O aplicativo de exemplo requer uma rota explícita de processamento de pedido para demonstrar os vários cenários de roteamento usando um único aplicativo. No entanto, você deve tentar evitar a prática de rota de configuração `Order` em aplicativos de produção.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. Informações sobre a ordem de rota nos tópicos MVC estão disponíveis em [roteamento para ações do controlador: Rotas de atributo de ordenação](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenções de modelo

Adicione um delegado <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> para adicionar [convenções de modelo](xref:mvc/controllers/application-model#conventions) que se aplicam a páginas do Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adicionar uma convenção de modelo de rota para todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> construção do modelo de instâncias que são aplicadas durante a rota de página.

O aplicativo de exemplo adiciona um modelo de rota `{globalTemplate?}` a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `1`. Isso garante que a rota correspondência de comportamento no aplicativo de exemplo a seguir:

* Um modelo de rota para `TheContactPage/{text?}` é adicionado posteriormente no tópico. A rota de página Contact tem uma ordem padrão de `null` (`Order = 0`), para que ele corresponda ao antes do `{globalTemplate?}` modelo de rota.
* Um `{aboutTemplate?}` modelo de rota é adicionado posteriormente no tópico. O modelo `{aboutTemplate?}` recebe uma `Order` de `2`. Quando a página About é solicitada no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração da propriedade `Order`.
* Um `{otherPagesTemplate?}` modelo de rota é adicionado posteriormente no tópico. O modelo `{otherPagesTemplate?}` recebe uma `Order` de `2`. Quando qualquer página na *páginas/OtherPages* pasta for solicitada com um parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à configuração de `Order` propriedade.

Sempre que possível, não defina a `Order`, que resulta em `Order = 0`. Dependem de roteamento para selecionar a rota correta.

Opções de páginas do Razor, como a adição <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, são adicionados quando o MVC é adicionado à coleção de serviços em `Startup.ConfigureServices`. Para obter um exemplo, confira o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue` e inspecione o resultado:

![A página About é solicitada com um segmento de rota GlobalRouteValue. A página renderizada mostra que o valor de dados de rota é capturado no método OnGet da página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adicionar uma convenção de modelo de aplicativo para todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instâncias que são aplicadas durante a construção do modelo de aplicativo de página.

Para demonstrar isso e outras convenções mais adiante no tópico, o aplicativo de exemplo inclui uma classe `AddHeaderAttribute`. O construtor de classe aceita uma cadeia de caracteres `name` e uma matriz de cadeia de caracteres `values`. Esses valores são usados em seu método `OnResultExecuting` para definir um cabeçalho de resposta. A classe completa é mostrada na seção [Convenções de ação do modelo de página](#page-model-action-conventions) mais adiante no tópico.

O aplicativo de exemplo usa a classe `AddHeaderAttribute` para adicionar um cabeçalho, `GlobalHeader`, a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o GlobalHeader foi adicionado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adicionar uma convenção de modelo de manipulador a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instâncias que são aplicadas durante a construção do modelo de manipulador de página.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenções de ação da rota de página

O provedor de modelo de rota padrão que é derivada de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar rotas de página.

### <a name="folder-route-model-convention"></a>Convenção de modelo de rota de pasta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas as páginas da pasta especificada.

O aplicativo de exemplo usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para adicionar um modelo de rota `{otherPagesTemplate?}` às páginas da pasta *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico para `1`) tem prioridade para os dados de rota a primeira posição de valor quando um único valor de rota é fornecido. Se uma página na *páginas/OtherPages* pasta for solicitada com um valor de parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à configuração de `Order` propriedade.

Sempre que possível, não defina a `Order`, que resulta em `Order = 0`. Dependem de roteamento para selecionar a rota correta.

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` e inspecione o resultado:

![A Page1 da pasta OtherPages é solicitada com um segmento de rota GlobalRouteValue e OtherPagesRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenção de modelo de rota de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para a página com o nome especificado.

O aplicativo de exemplo usa `AddPageRouteModelConvention` para adicionar um modelo de rota `{aboutTemplate?}` à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico para `1`) tem prioridade para os dados de rota a primeira posição de valor quando um único valor de rota é fornecido. Se a página About é solicitada com um valor de parâmetro de rota no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração de `Order` propriedade.

Sempre que possível, não defina a `Order`, que resulta em `Order = 0`. Dependem de roteamento para selecionar a rota correta.

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue/AboutRouteValue` e inspecione o resultado:

![A página About é solicitada com segmentos de rota para GlobalRouteValue e AboutRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Usar um transformador de parâmetro para personalizar rotas de página

Rotas de página geradas pelo ASP.NET Core podem ser personalizadas usando um transformador de parâmetro. Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.

O `PageRouteTransformerConvention` convenção de modelo de rota de página aplica-se um transformador de parâmetro para os segmentos de nome de arquivo e pasta de rotas de página gerada automaticamente em um aplicativo. Por exemplo, as páginas do Razor arquivo cada */Pages/SubscriptionManagement/ViewAll.cshtml* teria sua rota do reescrita `/SubscriptionManagement/ViewAll` para `/subscription-management/view-all`.

`PageRouteTransformerConvention` apenas transforma os segmentos gerados automaticamente de uma rota de página que vêm do nome de arquivo e pasta páginas do Razor. Ele não transformará os segmentos de rota adicionados com o `@page` diretiva. A convenção não transformará rotas adicionadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

O `PageRouteTransformerConvention` está registrado como uma opção na `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
                        new SlugifyParameterTransformer()));
            });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a>Configurar uma rota de página

Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar uma rota para uma página no caminho de página especificado. Os links gerados para a página usam a rota especificada. `AddPageRoute` usa `AddPageRouteModelConvention` para estabelecer a rota.

O aplicativo de exemplo cria uma rota para `/TheContactPage` para *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

A página Contact também pode ser acessada em `/Contact` por meio de sua rota padrão.

A rota personalizada do aplicativo de exemplo para a página Contact permite um segmento de rota `text` opcional (`{text?}`). A página também inclui esse segmento opcional em sua diretiva `@page`, caso o visitante acesse a página em sua rota `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Observe que a URL gerada para o link **Contato** na página renderizada reflete a rota atualizada:

![Link Contato do aplicativo de exemplo na barra de navegação](razor-pages-conventions/_static/contact-link.png)

![A inspeção do link Contato no HTML renderizado indica que o href é definido como '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visite a página Contact em sua rota comum, `/Contact`, ou na rota personalizada, `/TheContactPage`. Se você fornecer um segmento de rota `text` adicional, a página mostrará o segmento codificado em HTML fornecido:

![Exemplo do navegador Microsoft Edge de fornecimento de um segmento de rota 'text' opcional de 'TextValue' na URL. A página renderizada mostra o valor de segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenções de ação do modelo de página

O provedor de modelo de página padrão que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenções que foram projetadas para fornecer pontos de extensibilidade para configuração de modelos de página. Essas convenções são úteis ao criar e modificar cenários de descoberta e processamento de página.

Para os exemplos nesta seção, o aplicativo de exemplo usa uma `AddHeaderAttribute` classe, que é um <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, que se aplica a um cabeçalho de resposta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Usando convenções, a amostra explica como aplicar o atributo a todas as páginas de uma pasta e a uma única página.

**Convenção de modelo de aplicativo de pasta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instâncias para todas as páginas da pasta especificada.

A amostra explica o uso de `AddFolderApplicationModelConvention` adicionando um cabeçalho, `OtherPagesHeader`, às páginas dentro da pasta *OtherPages* do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página OtherPages/Page1 mostram que o OtherPagesHeader foi adicionado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenção de modelo de aplicativo de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para criar e adicionar uma <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para a página com o nome especificado.

A amostra explica o uso de `AddPageApplicationModelConvention` adicionando um cabeçalho, `AboutHeader`, à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o AboutHeader foi adicionado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar um filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o filtro especificado a ser aplicado. É possível implementar uma classe de filtro, mas o aplicativo de exemplo mostra como implementar um filtro em uma expressão lambda, que é implementado em segundo plano como um alocador que retorna um filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

O modelo de aplicativo de página é usado para verificar o caminho relativo para segmentos que levam à página Page2 na pasta *OtherPages*. Se a condição é aprovada, um cabeçalho é adicionado. Caso contrário, o `EmptyFilter` é aplicado.

`EmptyFilter` é um [filtro de Ação](xref:mvc/controllers/filters#action-filters). Uma vez que os filtros de ação são ignorados pelas páginas do Razor, o `EmptyFilter` não tem nenhum efeito conforme o esperado se o caminho não contém `OtherPages/Page2`.

Solicite a página Page2 da amostra em `localhost:5000/OtherPages/Page2` e inspecione os cabeçalhos para exibir o resultado:

![O OtherPagesPage2Header é adicionado à resposta para Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar um alocador de filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o alocador especificado para aplicar [filtros](xref:mvc/controllers/filters) a todas as páginas do Razor.

O aplicativo de exemplo fornece um exemplo de como usar um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) adicionando um cabeçalho, `FilterFactoryHeader`, com dois valores para as páginas do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que dois cabeçalhos FilterFactoryHeader foram adicionados.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros MVC e o filtro de Página (IPageFilter)

Os [filtros de Ação](xref:mvc/controllers/filters#action-filters) MVC são ignorados pelas Páginas do Razor, pois elas usam métodos de manipulador. Outros tipos de filtros MVC estão disponíveis para uso: [Autorização](xref:mvc/controllers/filters#authorization-filters), [exceção](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), e [resultado](xref:mvc/controllers/filters#result-filters). Para obter mais informações, consulte o tópico [Filtros](xref:mvc/controllers/filters).

O filtro de página (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) é um filtro que se aplica a páginas do Razor. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
