---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: eba9de26e1af8c4df06b846748d8aa9380fc1862
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65450873"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilação de arquivo do Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC associado é chamado. Não há suporte para a publicação de arquivos do Razor de tempo de build. Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado. Não há suporte para a publicação de arquivos do Razor de tempo de build. Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado. Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk). A compilação do tempo de execução pode ser opcionalmente habilitada através da configuração do aplicativo.

::: moniker-end

## <a name="razor-compilation"></a>Compilação do Razor

::: moniker range=">= aspnetcore-3.0"
A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Quando habilitada, a compilação de tempo de execução complementará a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build. Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.

> [!IMPORTANT]
> A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0. É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).
>
> O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto. Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Se o projeto for direcionado ao .NET Framework, instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

Se o projeto for direcionado ao .NET Core, nenhuma alteração será necessária.

Os modelos de projeto do ASP.NET Core 2.x definem implicitamente a propriedade `MvcRazorCompileOnPublish` como `true` por padrão. Consequentemente, esse elemento pode ser removido com segurança do arquivo *.csproj*.

> [!IMPORTANT]
> A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0. É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).
>
> A pré-compilação do arquivo do Razor não está disponível durante a execução de uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd) no ASP.NET Core 2.0.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Defina a propriedade `MvcRazorCompileOnPublish` como `true` e instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/). A seguinte amostra *.csproj* realça essas configurações:

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Prepare o aplicativo para uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) com o [comando de publicação da CLI do .NET Core](/dotnet/core/tools/dotnet-publish). Por exemplo, execute o seguinte comando na raiz do projeto:

```console
dotnet publish -c Release
```

Um arquivo *\<<nome_do_projeto>.PrecompiledViews.dll*, que contém os arquivos do Razor compilados, é produzido quando a pré-compilação é bem-sucedida. Por exemplo, a captura de tela abaixo mostra o conteúdo de *Index.cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:

![Exibições do Razor dentro da DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Compilação de tempo de execução

::: moniker range="= aspnetcore-2.1"

A compilação de tempo de build é complementada pela compilação de tempo de execução de arquivos do Razor. O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

A compilação de tempo de build é complementada pela compilação de tempo de execução de arquivos do Razor. O <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> obtém ou define um valor que determina se os arquivos do Razor (Exibições Razor e Razor Pages) são recompilados e atualizados, quando alterados em disco.

O valor padrão é `true` para:

* Se a versão de compatibilidade do aplicativo é definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> ou anterior
* Se a versão de compatibilidade do aplicativo é definida para <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> ou posterior e o aplicativo está no ambiente de desenvolvimento <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>. Em outras palavras, arquivos do Razor não serão recompilados no ambiente de não desenvolvimento, a menos que <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> seja definido explicitamente.

Para ver exemplos e obter orientação sobre como definir a versão de compatibilidade do aplicativo, confira <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

A compilação de tempo de execução é habilitada usando o pacote `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`. Para habilitar a compilação de tempo de execução, os aplicativos precisam:

* Instalar o pacote do NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).
* Atualizar o `ConfigureServices` do aplicativo para incluir uma chamada para `AddMvcRazorRuntimeCompilation`:

  ```csharp
  services
      .AddMvc()
      .AddRazorRuntimeCompilation()
  ```

Para que a compilação de tempo de execução funcione quando implantada, os aplicativos devem modificar seus arquivos de projeto para definir o `PreserveCompilationReferences` como `true`:

[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=4)]

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
