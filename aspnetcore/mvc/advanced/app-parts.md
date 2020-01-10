---
title: Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a95c344410db0651b9f8f1c1eb7551029f084c25
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829069"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a>Compartilhar controladores, exibições, Razor Pages e muito mais com partes do aplicativo

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))

Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo. As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> é uma parte do aplicativo. `AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.

Os [provedores de recursos](#fp) trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core. O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly. Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos. Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos. O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.

Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>. A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.

## <a name="load-aspnet-core-features"></a>Carregar recursos de ASP.NET Core

Use as classes <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> e <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.). O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis. o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Os dois exemplos de código anteriores carregam o `SharedController` de um assembly. O `SharedController` não está no projeto do aplicativo. Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .

### <a name="include-views"></a>Incluir exibições

Use uma [biblioteca de classes Razor](xref:razor-pages/ui-class) para incluir exibições no assembly.

### <a name="prevent-loading-resources"></a>Impedir o carregamento de recursos

Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local. Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis. A ordem das entradas na coleção `ApplicationParts` não é importante. Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner. Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`. Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.

O `ApplicationPartManager` inclui partes para:

* O assembly do aplicativo e os assemblies dependentes.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Provedores de recursos

Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes. Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso. Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente. A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução. Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.

### <a name="display-available-features"></a>Exibir recursos disponíveis

Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Descoberta em partes do aplicativo

Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo. Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas. Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:

* A configuração de `applicationName` precisa ser definida como o assembly raiz usado para descoberta. O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.
* O assembly raiz precisa ter uma referência às partes usadas para descoberta. A referência pode ser direta ou transitiva.
* O assembly raiz precisa fazer referência ao SDK da Web. A estrutura tem lógica que carimba os atributos no assembly raiz que são usados para descoberta.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))

Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo. As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo. `AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.

Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core. O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly. Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos. Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos. O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.

Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>. A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.

## <a name="load-aspnet-core-features"></a>Carregar recursos de ASP.NET Core

Use as classes `ApplicationPart` e `AssemblyPart` para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.). O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis. o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Os dois exemplos de código anteriores carregam o `SharedController` de um assembly. O `SharedController` não está no projeto do aplicativo. Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Incluir exibições

Use uma [biblioteca de classes Razor](xref:razor-pages/ui-class) para incluir exibições no assembly.

### <a name="prevent-loading-resources"></a>Impedir o carregamento de recursos

Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local. Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis. A ordem das entradas na coleção `ApplicationParts` não é importante. Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner. Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`. Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.

O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

O `ApplicationPartManager` inclui partes para:

* O assembly do aplicativo e os assemblies dependentes.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Provedores de recursos de aplicativos

Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes. Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:

* [Controladores](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Auxiliares de marcação](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Componentes da exibição](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso. Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente. A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução. Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.

### <a name="display-available-features"></a>Exibir recursos disponíveis

Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Descoberta em partes do aplicativo

Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo. Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas. Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:

* A configuração de `applicationName` precisa ser definida como o assembly raiz usado para descoberta. O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.
* O assembly raiz precisa ter uma referência às partes usadas para descoberta. A referência pode ser direta ou transitiva.
* O assembly raiz precisa fazer referência ao SDK da Web.
  * A estrutura de ASP.NET Core tem uma lógica de compilação personalizada que carimba atributos no assembly raiz que são usados para descoberta.

::: moniker-end
