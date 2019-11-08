---
title: Partes do aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 11/7/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ff6afa1852a3ee97fc4dbbae970dd746ec92f74c
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799460"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

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

Para incluir exibições no assembly:

* Adicione a seguinte marcação ao arquivo de projeto compartilhado:

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> ao <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>Impedir o carregamento de recursos

Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local. Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis. A ordem das entradas na coleção `ApplicationParts` não é importante. Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner. Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`. Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.

O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

O `ApplicationPartManager` inclui partes para:

* O assembly do aplicativo e os assemblies dependentes.
* `Microsoft.AspNetCore.Mvc.TagHelpers`
* `Microsoft.AspNetCore.Mvc.Razor`

## <a name="application-feature-providers"></a>Provedores de recursos de aplicativos

Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes. Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:

* [Controladores](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Auxiliares de Marcas](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Componentes da exibição](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso. Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente. A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução. Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.

### <a name="generic-controller-feature"></a>Recurso de controlador genérico

ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes). Um controlador genérico tem um parâmetro de tipo (por exemplo, `MyController<T>`). O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

Os tipos são definidos no `EntityTypes.Types`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

O provedor de recursos é adicionado em `Startup`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*. O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

A classe `GenericController`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

Por exemplo, solicitar uma URL de `https://localhost:5001/Sprocket` resulta na seguinte resposta:

```text
Hello from a generic Sprocket controller.
```

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

::: moniker-end

::: moniker range="<= aspnetcore-3.0"

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

Para incluir exibições no assembly:

* Adicione a seguinte marcação ao arquivo de projeto compartilhado:

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> ao <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>Impedir o carregamento de recursos

Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local. Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis. A ordem das entradas na coleção `ApplicationParts` não é importante. Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner. Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`. Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.

O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

O `ApplicationPartManager` inclui partes para:

* O assembly do aplicativo e os assemblies dependentes.
* `Microsoft.AspNetCore.Mvc.TagHelpers`
* `Microsoft.AspNetCore.Mvc.Razor`

## <a name="application-feature-providers"></a>Provedores de recursos de aplicativos

Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes. Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:

* [Controladores](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Auxiliares de Marcas](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Componentes da exibição](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso. Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente. A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução. Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.

### <a name="generic-controller-feature"></a>Recurso de controlador genérico

ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes). Um controlador genérico tem um parâmetro de tipo (por exemplo, `MyController<T>`). O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

Os tipos são definidos no `EntityTypes.Types`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

O provedor de recursos é adicionado em `Startup`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*. O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

A classe `GenericController`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

Por exemplo, solicitar uma URL de `https://localhost:5001/Sprocket` resulta na seguinte resposta:

```text
Hello from a generic Sprocket controller.
```

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

::: moniker-end