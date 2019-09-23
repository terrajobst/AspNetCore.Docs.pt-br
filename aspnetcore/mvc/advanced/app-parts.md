---
title: Partes do aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ad0372f25377115e6fc7c8ea42db75de56b3e6d2
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187005"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a>Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
=======

<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))

Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo. As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo. `AssemblyPart`encapsula uma referência de assembly e expõe tipos e referências de compilação.

Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core. O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly. Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos. Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos. O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.

Os aplicativos ASP.NET Core carregam <xref:System.Web.WebPages.ApplicationPart>recursos do. A <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe representa uma parte do aplicativo que é apoiada por um assembly.

## <a name="load-aspnet-core-features"></a>Carregar recursos de ASP.NET Core

Use as `ApplicationPart` classes `AssemblyPart` e para descobrir e carregar ASP.NET Core recursos (controladores, exibir componentes, etc.). O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> rastreia as partes do aplicativo e os provedores de recursos disponíveis. `ApplicationPartManager`está configurado em `Startup.ConfigureServices`:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

O código a seguir fornece uma abordagem alternativa para `ApplicationPartManager` configurar `AssemblyPart`o usando:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Os dois exemplos de código anteriores carregam o `SharedController` de um assembly. O `SharedController` não está no projeto de aplicativos. Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .

### <a name="include-views"></a>Incluir exibições

Para incluir exibições no assembly:

* Adicione a seguinte marcação ao arquivo de projeto compartilhado:

  ```csproj
    <ItemGroup>
      <EmbeddedResource Include = "Views\**\*.cshtml" />
    </ ItemGroup >
  ```

* Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>ao:

[!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a>Impedir o carregamento de recursos

Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local. Adicionar ou remover membros da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> coleção para ocultar ou tornar os recursos disponíveis. A ordem das entradas na coleção `ApplicationParts` não é importante. Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner. Por exemplo, configure o `ApplicationPartManager` antes de `AddControllersAsServices`chamar. `Remove` Chame`ApplicationParts` na coleção para remover um recurso.

O código a seguir <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> usa para `MyDependentLibrary` remover do aplicativo:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

O `ApplicationPartManager` inclui partes para:

* O assembly de aplicativos e os assemblies dependentes.
* `Microsoft.AspNetCore.Mvc.TagHelpers`
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Provedores de recursos de aplicativos

Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes. Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:

* [Controladores](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Auxiliares de marcação](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Componentes da exibição](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso. Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente. A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução. Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.

### <a name="generic-controller-feature"></a>Recurso de controlador genérico

ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes). Um controlador genérico tem um parâmetro de tipo (por exemplo `MyController<T>`,). O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos.

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

Os tipos são definidos em `EntityTypes.Types`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

O provedor de recursos é adicionado em `Startup`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*. O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

A classe `GenericController`:

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

### <a name="display-available-features"></a>Exibir recursos disponíveis

Os recursos disponíveis para um aplicativo podem ser enumerados pelo solicitando uma `ApplicationPartManager` [injeção de dependência](../../fundamentals/dependency-injection.md)por meio de:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo.