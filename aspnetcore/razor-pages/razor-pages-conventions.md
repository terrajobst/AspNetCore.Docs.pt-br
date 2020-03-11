---
title: Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core
author: rick-anderson
description: Descubra como as convenções do provedor de modelo de aplicativo e rota ajudam você a controlar o roteamento, a descoberta e o processamento de página.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: f45e327051aba54d1cab67148eb540fb1a5cc149
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667856"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Saiba como usar a página [convenções do provedor de modelo de rota e aplicativo](xref:mvc/controllers/application-model#conventions) para controlar o roteamento, a descoberta e o processamento de páginas nos aplicativos Páginas do Razor.

Quando precisar configurar rotas de página personalizadas para páginas individuais, configure o roteamento para páginas com a [convenção AddPageRoute](#configure-a-page-route) descrita mais adiante neste tópico.

Para especificar uma rota de página, adicionar segmentos de rota ou adicionar parâmetros a uma rota, use a diretiva de `@page` da página. Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes).

Há palavras reservadas que não podem ser usadas como segmentos de rota ou nomes de parâmetro. Para obter mais informações, consulte [Roteamento: nomes de roteamento reservados](xref:fundamentals/routing#reserved-routing-names).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([como baixar](xref:index#how-to-download-a-sample))

| Cenário | A amostra explica... |
| -------- | --------------------------- |
| [Convenções de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Adicione um cabeçalho e um modelo de rota às páginas de um aplicativo. |
| [Convenções de ação da rota de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Adicione um modelo de rota às páginas em uma pasta e a uma única página. |
| [Convenções de ação do modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtro, expressão lambda ou alocador de filtro)</li></ul> | Adicione um cabeçalho às páginas em uma pasta, adicione um cabeçalho a uma única página e configure um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) para adicionar um cabeçalho às páginas de um aplicativo. |

As convenções de Razor Pages são adicionadas e configuradas usando o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na coleção de serviços na classe `Startup`. Os exemplos de convenção a seguir estão descritos mais adiante neste tópico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordem de rota

Rotas especificam um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para processamento (correspondência de rota).

| Order            | Comportamento |
| :--------------: | -------- |
| -1               | A rota é processada antes que outras rotas sejam processadas. |
| 0                | A ordem não está especificada (valor padrão). Não atribuir `Order` (`Order = null`) usa como padrão a rota `Order` para 0 (zero) para processamento. |
| 1, 2, &hellip; n | Especifica a ordem de processamento da rota. |

O processamento de rota é estabelecido por convenção:

* As rotas são processadas em ordem sequencial (-1, 0, 1, 2, &hellip; n).
* Quando as rotas têm o mesmo `Order`, a rota mais específica é correspondida primeiro, seguida por rotas menos específicas.
* Quando as rotas com o mesmo `Order` e o mesmo número de parâmetros correspondem a uma URL de solicitação, as rotas são processadas na ordem em que são adicionadas à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Se possível, evite dependendo de uma ordem de processamento de rota estabelecida. Em geral, o roteamento seleciona a rota correta com correspondência de URL. Se você tiver que definir as propriedades de `Order` de rota para rotear solicitações corretamente, o esquema de roteamento do aplicativo provavelmente será confuso para clientes e frágeis para manter. Busque para simplificar o esquema de roteamento do aplicativo. O aplicativo de exemplo requer uma ordem de processamento de rota explícita para demonstrar vários cenários de roteamento usando um único aplicativo. No entanto, você deve tentar evitar a prática de definir `Order` de rota em aplicativos de produção.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. As informações sobre a ordem de rota nos tópicos do MVC estão disponíveis em [Roteamento para ações do controlador: ordenando rotas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenções de modelo

Adicione um delegado para <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> adicionar [convenções de modelo](xref:mvc/controllers/application-model#conventions) que se aplicam a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de rota a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de rota de página.

O aplicativo de exemplo adiciona um modelo de rota `{globalTemplate?}` a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `1`. Isso garante o seguinte comportamento de correspondência de rota no aplicativo de exemplo:

* Um modelo de rota para `TheContactPage/{text?}` é adicionado posteriormente no tópico. A rota da página de contato tem uma ordem padrão de `null` (`Order = 0`), portanto, ela corresponde antes do modelo de rota `{globalTemplate?}`.
* Um modelo de rota `{aboutTemplate?}` é adicionado posteriormente no tópico. O modelo `{aboutTemplate?}` recebe uma `Order` de `2`. Quando a página About é solicitada no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração da propriedade `Order`.
* Um modelo de rota `{otherPagesTemplate?}` é adicionado posteriormente no tópico. O modelo `{otherPagesTemplate?}` recebe uma `Order` de `2`. Quando qualquer página na pasta *pages/OtherPages* é solicitada com um parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Razor Pages opções, como adicionar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, são adicionadas quando o MVC é adicionado à coleção de serviços no `Startup.ConfigureServices`. Para obter um exemplo, confira o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue` e inspecione o resultado:

![A página About é solicitada com um segmento de rota GlobalRouteValue. A página renderizada mostra que o valor de dados de rota é capturado no método OnGet da página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de aplicativo a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção de modelo de aplicativo de página.

Para demonstrar isso e outras convenções mais adiante no tópico, o aplicativo de exemplo inclui uma classe `AddHeaderAttribute`. O construtor de classe aceita uma cadeia de caracteres `name` e uma matriz de cadeia de caracteres `values`. Esses valores são usados em seu método `OnResultExecuting` para definir um cabeçalho de resposta. A classe completa é mostrada na seção [Convenções de ação do modelo de página](#page-model-action-conventions) mais adiante no tópico.

O aplicativo de exemplo usa a classe `AddHeaderAttribute` para adicionar um cabeçalho, `GlobalHeader`, a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o GlobalHeader foi adicionado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de manipulador a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de manipulador de página.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenções de ação da rota de página

O provedor de modelo de rota padrão que deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar rotas de página.

### <a name="folder-route-model-convention"></a>Convenção de modelo de rota de pasta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas as páginas na pasta especificada.

O aplicativo de exemplo usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para adicionar um modelo de rota `{otherPagesTemplate?}` às páginas da pasta *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se uma página na pasta *pages/OtherPages* for solicitada com um valor de parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` e inspecione o resultado:

![A Page1 da pasta OtherPages é solicitada com um segmento de rota GlobalRouteValue e OtherPagesRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenção de modelo de rota de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para a página com o nome especificado.

O aplicativo de exemplo usa `AddPageRouteModelConvention` para adicionar um modelo de rota `{aboutTemplate?}` à página About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se a página About for solicitada com um valor de parâmetro de rota em `/About/RouteDataValue`, "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue/AboutRouteValue` e inspecione o resultado:

![A página About é solicitada com segmentos de rota para GlobalRouteValue e AboutRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Usar um transformador de parâmetro para personalizar rotas de página

As rotas de página geradas por ASP.NET Core podem ser personalizadas usando um transformador de parâmetro. Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.

A Convenção de modelo de rota de página `PageRouteTransformerConvention` aplica um transformador de parâmetro aos segmentos de nome de arquivo e pasta de rotas de página geradas automaticamente em um aplicativo. Por exemplo, o arquivo de Razor Pages em */pages/SubscriptionManagement/ViewAll.cshtml* teria sua rota reescrita de `/SubscriptionManagement/ViewAll` para `/subscription-management/view-all`.

`PageRouteTransformerConvention` transforma apenas os segmentos gerados automaticamente de uma rota de página que vem da pasta Razor Pages e o nome do arquivo. Ele não transforma segmentos de rota adicionados com a diretiva `@page`. A Convenção também não transforma as rotas adicionadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

O `PageRouteTransformerConvention` é registrado como uma opção no `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="configure-a-page-route"></a>Configurar uma rota de página

Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar uma rota para uma página no caminho de página especificado. Os links gerados para a página usam a rota especificada. `AddPageRoute` usa `AddPageRouteModelConvention` para estabelecer a rota.

O aplicativo de exemplo cria uma rota para `/TheContactPage` para *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

A página Contact também pode ser acessada em `/Contact` por meio de sua rota padrão.

A rota personalizada do aplicativo de exemplo para a página Contact permite um segmento de rota `text` opcional (`{text?}`). A página também inclui esse segmento opcional em sua diretiva `@page`, caso o visitante acesse a página em sua rota `/Contact`:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Observe que a URL gerada para o link **Contato** na página renderizada reflete a rota atualizada:

![Link Contato do aplicativo de exemplo na barra de navegação](razor-pages-conventions/_static/contact-link.png)

![A inspeção do link Contato no HTML renderizado indica que o href é definido como '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Visite a página Contact em sua rota comum, `/Contact`, ou na rota personalizada, `/TheContactPage`. Se você fornecer um segmento de rota `text` adicional, a página mostrará o segmento codificado em HTML fornecido:

![Exemplo do navegador Microsoft Edge de fornecimento de um segmento de rota 'text' opcional de 'TextValue' na URL. A página renderizada mostra o valor de segmento 'text'.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Convenções de ação do modelo de página

O provedor de modelo de página padrão que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar modelos de página. Essas convenções são úteis ao criar e modificar cenários de descoberta e processamento de página.

Para os exemplos nesta seção, o aplicativo de exemplo usa uma classe `AddHeaderAttribute`, que é uma <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, que aplica um cabeçalho de resposta:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Usando convenções, a amostra explica como aplicar o atributo a todas as páginas de uma pasta e a uma única página.

**Convenção de modelo de aplicativo de pasta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação em <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instâncias para todas as páginas na pasta especificada.

A amostra explica o uso de `AddFolderApplicationModelConvention` adicionando um cabeçalho, `OtherPagesHeader`, às páginas dentro da pasta *OtherPages* do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página OtherPages/Page1 mostram que o OtherPagesHeader foi adicionado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenção de modelo de aplicativo de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para a página com o nome especificado.

A amostra explica o uso de `AddPageApplicationModelConvention` adicionando um cabeçalho, `AboutHeader`, à página About:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o AboutHeader foi adicionado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar um filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o filtro especificado a ser aplicado. É possível implementar uma classe de filtro, mas o aplicativo de exemplo mostra como implementar um filtro em uma expressão lambda, que é implementado em segundo plano como um alocador que retorna um filtro:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

O modelo de aplicativo de página é usado para verificar o caminho relativo para segmentos que levam à página Page2 na pasta *OtherPages*. Se a condição é aprovada, um cabeçalho é adicionado. Caso contrário, o `EmptyFilter` é aplicado.

`EmptyFilter` é um [filtro de Ação](xref:mvc/controllers/filters#action-filters). Como os filtros de ação são ignorados por Razor Pages, o `EmptyFilter` não tem nenhum efeito como se o caminho não contiver `OtherPages/Page2`.

Solicite a página Page2 da amostra em `localhost:5000/OtherPages/Page2` e inspecione os cabeçalhos para exibir o resultado:

![O OtherPagesPage2Header é adicionado à resposta para Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar um alocador de filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura a fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas as Razor Pages.

O aplicativo de exemplo fornece um exemplo de como usar um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) adicionando um cabeçalho, `FilterFactoryHeader`, com dois valores para as páginas do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que dois cabeçalhos FilterFactoryHeader foram adicionados.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros MVC e o filtro de Página (IPageFilter)

Os [filtros de Ação](xref:mvc/controllers/filters#action-filters) MVC são ignorados pelas Páginas do Razor, pois elas usam métodos de manipulador. Outros tipos de filtros MVC estão disponíveis para uso: [Autorização](xref:mvc/controllers/filters#authorization-filters), [Exceção](xref:mvc/controllers/filters#exception-filters), [Recurso](xref:mvc/controllers/filters#resource-filters) e [Resultado](xref:mvc/controllers/filters#result-filters). Para obter mais informações, consulte o tópico [Filtros](xref:mvc/controllers/filters).

O filtro de página (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) é um filtro que se aplica a Razor Pages. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Saiba como usar a página [convenções do provedor de modelo de rota e aplicativo](xref:mvc/controllers/application-model#conventions) para controlar o roteamento, a descoberta e o processamento de páginas nos aplicativos Páginas do Razor.

Quando precisar configurar rotas de página personalizadas para páginas individuais, configure o roteamento para páginas com a [convenção AddPageRoute](#configure-a-page-route) descrita mais adiante neste tópico.

Para especificar uma rota de página, adicionar segmentos de rota ou adicionar parâmetros a uma rota, use a diretiva de `@page` da página. Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes).

Há palavras reservadas que não podem ser usadas como segmentos de rota ou nomes de parâmetro. Para obter mais informações, consulte [Roteamento: nomes de roteamento reservados](xref:fundamentals/routing#reserved-routing-names).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([como baixar](xref:index#how-to-download-a-sample))

| Cenário | A amostra explica... |
| -------- | --------------------------- |
| [Convenções de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Adicione um cabeçalho e um modelo de rota às páginas de um aplicativo. |
| [Convenções de ação da rota de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Adicione um modelo de rota às páginas em uma pasta e a uma única página. |
| [Convenções de ação do modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtro, expressão lambda ou alocador de filtro)</li></ul> | Adicione um cabeçalho às páginas em uma pasta, adicione um cabeçalho a uma única página e configure um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) para adicionar um cabeçalho às páginas de um aplicativo. |

As convenções de Razor Pages são adicionadas e configuradas usando o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na coleção de serviços na classe `Startup`. Os exemplos de convenção a seguir estão descritos mais adiante neste tópico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordem de rota

Rotas especificam um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para processamento (correspondência de rota).

| Order            | Comportamento |
| :--------------: | -------- |
| -1               | A rota é processada antes que outras rotas sejam processadas. |
| 0                | A ordem não está especificada (valor padrão). Não atribuir `Order` (`Order = null`) usa como padrão a rota `Order` para 0 (zero) para processamento. |
| 1, 2, &hellip; n | Especifica a ordem de processamento da rota. |

O processamento de rota é estabelecido por convenção:

* As rotas são processadas em ordem sequencial (-1, 0, 1, 2, &hellip; n).
* Quando as rotas têm o mesmo `Order`, a rota mais específica é correspondida primeiro, seguida por rotas menos específicas.
* Quando as rotas com o mesmo `Order` e o mesmo número de parâmetros correspondem a uma URL de solicitação, as rotas são processadas na ordem em que são adicionadas à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Se possível, evite dependendo de uma ordem de processamento de rota estabelecida. Em geral, o roteamento seleciona a rota correta com correspondência de URL. Se você tiver que definir as propriedades de `Order` de rota para rotear solicitações corretamente, o esquema de roteamento do aplicativo provavelmente será confuso para clientes e frágeis para manter. Busque para simplificar o esquema de roteamento do aplicativo. O aplicativo de exemplo requer uma ordem de processamento de rota explícita para demonstrar vários cenários de roteamento usando um único aplicativo. No entanto, você deve tentar evitar a prática de definir `Order` de rota em aplicativos de produção.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. As informações sobre a ordem de rota nos tópicos do MVC estão disponíveis em [Roteamento para ações do controlador: ordenando rotas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenções de modelo

Adicione um delegado para <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> adicionar [convenções de modelo](xref:mvc/controllers/application-model#conventions) que se aplicam a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de rota a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de rota de página.

O aplicativo de exemplo adiciona um modelo de rota `{globalTemplate?}` a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `1`. Isso garante o seguinte comportamento de correspondência de rota no aplicativo de exemplo:

* Um modelo de rota para `TheContactPage/{text?}` é adicionado posteriormente no tópico. A rota da página de contato tem uma ordem padrão de `null` (`Order = 0`), portanto, ela corresponde antes do modelo de rota `{globalTemplate?}`.
* Um modelo de rota `{aboutTemplate?}` é adicionado posteriormente no tópico. O modelo `{aboutTemplate?}` recebe uma `Order` de `2`. Quando a página About é solicitada no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração da propriedade `Order`.
* Um modelo de rota `{otherPagesTemplate?}` é adicionado posteriormente no tópico. O modelo `{otherPagesTemplate?}` recebe uma `Order` de `2`. Quando qualquer página na pasta *pages/OtherPages* é solicitada com um parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Razor Pages opções, como adicionar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, são adicionadas quando o MVC é adicionado à coleção de serviços no `Startup.ConfigureServices`. Para obter um exemplo, confira o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue` e inspecione o resultado:

![A página About é solicitada com um segmento de rota GlobalRouteValue. A página renderizada mostra que o valor de dados de rota é capturado no método OnGet da página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de aplicativo a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção de modelo de aplicativo de página.

Para demonstrar isso e outras convenções mais adiante no tópico, o aplicativo de exemplo inclui uma classe `AddHeaderAttribute`. O construtor de classe aceita uma cadeia de caracteres `name` e uma matriz de cadeia de caracteres `values`. Esses valores são usados em seu método `OnResultExecuting` para definir um cabeçalho de resposta. A classe completa é mostrada na seção [Convenções de ação do modelo de página](#page-model-action-conventions) mais adiante no tópico.

O aplicativo de exemplo usa a classe `AddHeaderAttribute` para adicionar um cabeçalho, `GlobalHeader`, a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o GlobalHeader foi adicionado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de manipulador a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de manipulador de página.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenções de ação da rota de página

O provedor de modelo de rota padrão que deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar rotas de página.

### <a name="folder-route-model-convention"></a>Convenção de modelo de rota de pasta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas as páginas na pasta especificada.

O aplicativo de exemplo usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para adicionar um modelo de rota `{otherPagesTemplate?}` às páginas da pasta *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se uma página na pasta *pages/OtherPages* for solicitada com um valor de parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` e inspecione o resultado:

![A Page1 da pasta OtherPages é solicitada com um segmento de rota GlobalRouteValue e OtherPagesRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenção de modelo de rota de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para a página com o nome especificado.

O aplicativo de exemplo usa `AddPageRouteModelConvention` para adicionar um modelo de rota `{aboutTemplate?}` à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se a página About for solicitada com um valor de parâmetro de rota em `/About/RouteDataValue`, "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue/AboutRouteValue` e inspecione o resultado:

![A página About é solicitada com segmentos de rota para GlobalRouteValue e AboutRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Usar um transformador de parâmetro para personalizar rotas de página

As rotas de página geradas por ASP.NET Core podem ser personalizadas usando um transformador de parâmetro. Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros. Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.

A Convenção de modelo de rota de página `PageRouteTransformerConvention` aplica um transformador de parâmetro aos segmentos de nome de arquivo e pasta de rotas de página geradas automaticamente em um aplicativo. Por exemplo, o arquivo de Razor Pages em */pages/SubscriptionManagement/ViewAll.cshtml* teria sua rota reescrita de `/SubscriptionManagement/ViewAll` para `/subscription-management/view-all`.

`PageRouteTransformerConvention` transforma apenas os segmentos gerados automaticamente de uma rota de página que vem da pasta Razor Pages e o nome do arquivo. Ele não transforma segmentos de rota adicionados com a diretiva `@page`. A Convenção também não transforma as rotas adicionadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

O `PageRouteTransformerConvention` é registrado como uma opção no `Startup.ConfigureServices`:

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

O provedor de modelo de página padrão que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar modelos de página. Essas convenções são úteis ao criar e modificar cenários de descoberta e processamento de página.

Para os exemplos nesta seção, o aplicativo de exemplo usa uma classe `AddHeaderAttribute`, que é uma <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, que aplica um cabeçalho de resposta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Usando convenções, a amostra explica como aplicar o atributo a todas as páginas de uma pasta e a uma única página.

**Convenção de modelo de aplicativo de pasta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação em <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instâncias para todas as páginas na pasta especificada.

A amostra explica o uso de `AddFolderApplicationModelConvention` adicionando um cabeçalho, `OtherPagesHeader`, às páginas dentro da pasta *OtherPages* do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página OtherPages/Page1 mostram que o OtherPagesHeader foi adicionado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenção de modelo de aplicativo de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para a página com o nome especificado.

A amostra explica o uso de `AddPageApplicationModelConvention` adicionando um cabeçalho, `AboutHeader`, à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o AboutHeader foi adicionado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar um filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o filtro especificado a ser aplicado. É possível implementar uma classe de filtro, mas o aplicativo de exemplo mostra como implementar um filtro em uma expressão lambda, que é implementado em segundo plano como um alocador que retorna um filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

O modelo de aplicativo de página é usado para verificar o caminho relativo para segmentos que levam à página Page2 na pasta *OtherPages*. Se a condição é aprovada, um cabeçalho é adicionado. Caso contrário, o `EmptyFilter` é aplicado.

`EmptyFilter` é um [filtro de Ação](xref:mvc/controllers/filters#action-filters). Como os filtros de ação são ignorados por Razor Pages, o `EmptyFilter` não tem nenhum efeito como se o caminho não contiver `OtherPages/Page2`.

Solicite a página Page2 da amostra em `localhost:5000/OtherPages/Page2` e inspecione os cabeçalhos para exibir o resultado:

![O OtherPagesPage2Header é adicionado à resposta para Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar um alocador de filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura a fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas as Razor Pages.

O aplicativo de exemplo fornece um exemplo de como usar um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) adicionando um cabeçalho, `FilterFactoryHeader`, com dois valores para as páginas do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que dois cabeçalhos FilterFactoryHeader foram adicionados.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros MVC e o filtro de Página (IPageFilter)

Os [filtros de Ação](xref:mvc/controllers/filters#action-filters) MVC são ignorados pelas Páginas do Razor, pois elas usam métodos de manipulador. Outros tipos de filtros MVC estão disponíveis para uso: [Autorização](xref:mvc/controllers/filters#authorization-filters), [Exceção](xref:mvc/controllers/filters#exception-filters), [Recurso](xref:mvc/controllers/filters#resource-filters) e [Resultado](xref:mvc/controllers/filters#result-filters). Para obter mais informações, consulte o tópico [Filtros](xref:mvc/controllers/filters).

O filtro de página (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) é um filtro que se aplica a Razor Pages. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Saiba como usar a página [convenções do provedor de modelo de rota e aplicativo](xref:mvc/controllers/application-model#conventions) para controlar o roteamento, a descoberta e o processamento de páginas nos aplicativos Páginas do Razor.

Quando precisar configurar rotas de página personalizadas para páginas individuais, configure o roteamento para páginas com a [convenção AddPageRoute](#configure-a-page-route) descrita mais adiante neste tópico.

Para especificar uma rota de página, adicionar segmentos de rota ou adicionar parâmetros a uma rota, use a diretiva de `@page` da página. Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes).

Há palavras reservadas que não podem ser usadas como segmentos de rota ou nomes de parâmetro. Para obter mais informações, consulte [Roteamento: nomes de roteamento reservados](xref:fundamentals/routing#reserved-routing-names).

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([como baixar](xref:index#how-to-download-a-sample))

| Cenário | A amostra explica... |
| -------- | --------------------------- |
| [Convenções de modelo](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Adicione um cabeçalho e um modelo de rota às páginas de um aplicativo. |
| [Convenções de ação da rota de página](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Adicione um modelo de rota às páginas em uma pasta e a uma única página. |
| [Convenções de ação do modelo de página](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtro, expressão lambda ou alocador de filtro)</li></ul> | Adicione um cabeçalho às páginas em uma pasta, adicione um cabeçalho a uma única página e configure um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) para adicionar um cabeçalho às páginas de um aplicativo. |

As convenções de Razor Pages são adicionadas e configuradas usando o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na coleção de serviços na classe `Startup`. Os exemplos de convenção a seguir estão descritos mais adiante neste tópico:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordem de rota

Rotas especificam um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para processamento (correspondência de rota).

| Order            | Comportamento |
| :--------------: | -------- |
| -1               | A rota é processada antes que outras rotas sejam processadas. |
| 0                | A ordem não está especificada (valor padrão). Não atribuir `Order` (`Order = null`) usa como padrão a rota `Order` para 0 (zero) para processamento. |
| 1, 2, &hellip; n | Especifica a ordem de processamento da rota. |

O processamento de rota é estabelecido por convenção:

* As rotas são processadas em ordem sequencial (-1, 0, 1, 2, &hellip; n).
* Quando as rotas têm o mesmo `Order`, a rota mais específica é correspondida primeiro, seguida por rotas menos específicas.
* Quando as rotas com o mesmo `Order` e o mesmo número de parâmetros correspondem a uma URL de solicitação, as rotas são processadas na ordem em que são adicionadas à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Se possível, evite dependendo de uma ordem de processamento de rota estabelecida. Em geral, o roteamento seleciona a rota correta com correspondência de URL. Se você tiver que definir as propriedades de `Order` de rota para rotear solicitações corretamente, o esquema de roteamento do aplicativo provavelmente será confuso para clientes e frágeis para manter. Busque para simplificar o esquema de roteamento do aplicativo. O aplicativo de exemplo requer uma ordem de processamento de rota explícita para demonstrar vários cenários de roteamento usando um único aplicativo. No entanto, você deve tentar evitar a prática de definir `Order` de rota em aplicativos de produção.

Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação. As informações sobre a ordem de rota nos tópicos do MVC estão disponíveis em [Roteamento para ações do controlador: ordenando rotas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Convenções de modelo

Adicione um delegado para <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> adicionar [convenções de modelo](xref:mvc/controllers/application-model#conventions) que se aplicam a Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de rota a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de rota de página.

O aplicativo de exemplo adiciona um modelo de rota `{globalTemplate?}` a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `1`. Isso garante o seguinte comportamento de correspondência de rota no aplicativo de exemplo:

* Um modelo de rota para `TheContactPage/{text?}` é adicionado posteriormente no tópico. A rota da página de contato tem uma ordem padrão de `null` (`Order = 0`), portanto, ela corresponde antes do modelo de rota `{globalTemplate?}`.
* Um modelo de rota `{aboutTemplate?}` é adicionado posteriormente no tópico. O modelo `{aboutTemplate?}` recebe uma `Order` de `2`. Quando a página About é solicitada no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração da propriedade `Order`.
* Um modelo de rota `{otherPagesTemplate?}` é adicionado posteriormente no tópico. O modelo `{otherPagesTemplate?}` recebe uma `Order` de `2`. Quando qualquer página na pasta *pages/OtherPages* é solicitada com um parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Razor Pages opções, como adicionar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, são adicionadas quando o MVC é adicionado à coleção de serviços no `Startup.ConfigureServices`. Para obter um exemplo, confira o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue` e inspecione o resultado:

![A página About é solicitada com um segmento de rota GlobalRouteValue. A página renderizada mostra que o valor de dados de rota é capturado no método OnGet da página.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de aplicativo a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção de modelo de aplicativo de página.

Para demonstrar isso e outras convenções mais adiante no tópico, o aplicativo de exemplo inclui uma classe `AddHeaderAttribute`. O construtor de classe aceita uma cadeia de caracteres `name` e uma matriz de cadeia de caracteres `values`. Esses valores são usados em seu método `OnResultExecuting` para definir um cabeçalho de resposta. A classe completa é mostrada na seção [Convenções de ação do modelo de página](#page-model-action-conventions) mais adiante no tópico.

O aplicativo de exemplo usa a classe `AddHeaderAttribute` para adicionar um cabeçalho, `GlobalHeader`, a todas as páginas no aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o GlobalHeader foi adicionado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Adicionar uma Convenção de modelo de manipulador a todas as páginas

Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de manipulador de página.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Convenções de ação da rota de página

O provedor de modelo de rota padrão que deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar rotas de página.

### <a name="folder-route-model-convention"></a>Convenção de modelo de rota de pasta

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas as páginas na pasta especificada.

O aplicativo de exemplo usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para adicionar um modelo de rota `{otherPagesTemplate?}` às páginas da pasta *OtherPages*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se uma página na pasta *pages/OtherPages* for solicitada com um valor de parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` e inspecione o resultado:

![A Page1 da pasta OtherPages é solicitada com um segmento de rota GlobalRouteValue e OtherPagesRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convenção de modelo de rota de página

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para a página com o nome especificado.

O aplicativo de exemplo usa `AddPageRouteModelConvention` para adicionar um modelo de rota `{aboutTemplate?}` à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`. Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido. Se a página About for solicitada com um valor de parâmetro de rota em `/About/RouteDataValue`, "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.

Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`. Confie no roteamento para selecionar a rota correta.

Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue/AboutRouteValue` e inspecione o resultado:

![A página About é solicitada com segmentos de rota para GlobalRouteValue e AboutRouteValue. A página renderizada mostra que os valores de dados de rota são capturados no método OnGet da página.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

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

O provedor de modelo de página padrão que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar modelos de página. Essas convenções são úteis ao criar e modificar cenários de descoberta e processamento de página.

Para os exemplos nesta seção, o aplicativo de exemplo usa uma classe `AddHeaderAttribute`, que é uma <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, que aplica um cabeçalho de resposta:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Usando convenções, a amostra explica como aplicar o atributo a todas as páginas de uma pasta e a uma única página.

**Convenção de modelo de aplicativo de pasta**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação em <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instâncias para todas as páginas na pasta especificada.

A amostra explica o uso de `AddFolderApplicationModelConvention` adicionando um cabeçalho, `OtherPagesHeader`, às páginas dentro da pasta *OtherPages* do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página OtherPages/Page1 mostram que o OtherPagesHeader foi adicionado.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convenção de modelo de aplicativo de página**

Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para a página com o nome especificado.

A amostra explica o uso de `AddPageApplicationModelConvention` adicionando um cabeçalho, `AboutHeader`, à página About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que o AboutHeader foi adicionado.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurar um filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o filtro especificado a ser aplicado. É possível implementar uma classe de filtro, mas o aplicativo de exemplo mostra como implementar um filtro em uma expressão lambda, que é implementado em segundo plano como um alocador que retorna um filtro:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

O modelo de aplicativo de página é usado para verificar o caminho relativo para segmentos que levam à página Page2 na pasta *OtherPages*. Se a condição é aprovada, um cabeçalho é adicionado. Caso contrário, o `EmptyFilter` é aplicado.

`EmptyFilter` é um [filtro de Ação](xref:mvc/controllers/filters#action-filters). Como os filtros de ação são ignorados por Razor Pages, o `EmptyFilter` não tem nenhum efeito como se o caminho não contiver `OtherPages/Page2`.

Solicite a página Page2 da amostra em `localhost:5000/OtherPages/Page2` e inspecione os cabeçalhos para exibir o resultado:

![O OtherPagesPage2Header é adicionado à resposta para Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurar um alocador de filtro**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura a fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas as Razor Pages.

O aplicativo de exemplo fornece um exemplo de como usar um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) adicionando um cabeçalho, `FilterFactoryHeader`, com dois valores para as páginas do aplicativo:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:

![Os cabeçalhos de resposta da página About mostram que dois cabeçalhos FilterFactoryHeader foram adicionados.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtros MVC e o filtro de Página (IPageFilter)

Os [filtros de Ação](xref:mvc/controllers/filters#action-filters) MVC são ignorados pelas Páginas do Razor, pois elas usam métodos de manipulador. Outros tipos de filtros MVC estão disponíveis para uso: [Autorização](xref:mvc/controllers/filters#authorization-filters), [Exceção](xref:mvc/controllers/filters#exception-filters), [Recurso](xref:mvc/controllers/filters#resource-filters) e [Resultado](xref:mvc/controllers/filters#result-filters). Para obter mais informações, consulte o tópico [Filtros](xref:mvc/controllers/filters).

O filtro de página (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) é um filtro que se aplica a Razor Pages. Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
