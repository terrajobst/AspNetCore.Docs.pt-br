---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: cd096bba5eb580c0a606699a2bf7c36442fb56f7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661101"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Compilação de arquivo do Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC associado é chamado. Não há suporte para a publicação de arquivos do Razor de tempo de build. Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado. Não há suporte para a publicação de arquivos do Razor de tempo de build. Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Um arquivo do Razor é compilado em runtime, quando o modo de exibição do MVC ou da Página do Razor associada é chamado. Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Os arquivos Razor com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [SDK do Razor](xref:razor-pages/sdk). A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.

::: moniker-end

## <a name="razor-compilation"></a>Compilação do Razor

::: moniker range=">= aspnetcore-3.0"

A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Quando habilitada, a compilação de runtime complementa a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor. Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build. Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.

> [!IMPORTANT]
> A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0. É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).
>
> O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto. Por exemplo, definir a propriedade *do arquivo*.csproj`MvcRazorCompileOnPublish` como `true` desabilita o SDK do Razor.

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

```dotnetcli
dotnet publish -c Release
```

Um arquivo *\<<nome_do_projeto>.PrecompiledViews.dll*, que contém os arquivos do Razor compilados, é produzido quando a pré-compilação é bem-sucedida. Por exemplo, a captura de tela abaixo mostra o conteúdo de *Index.cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:

![Exibições do Razor dentro da DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a>Compilação de runtime

::: moniker range="= aspnetcore-2.1"

A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor. O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

A compilação de tempo de build é complementada pela compilação de runtime de arquivos do Razor. O <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> Obtém ou define um valor que determina se os arquivos Razor (exibições do Razor e Razor Pages) são recompilados e atualizados se os arquivos são alterados no disco.

O valor padrão é `true` para:

* Se a versão de compatibilidade do aplicativo é definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> ou anterior
* Se a versão de compatibilidade do aplicativo está definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> ou posterior e se o aplicativo está no ambiente de desenvolvimento <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>. Em outras palavras, os arquivos do Razor não seriam recompilados em um ambiente que não seja de desenvolvimento, a menos que <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> seja explicitamente definido.

Para ver exemplos e obter orientação sobre como definir a versão de compatibilidade do aplicativo, confira <xref:mvc/compatibility-version>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:

1. Instalar o pacote do NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).

1. Atualize o método de `Startup.ConfigureServices` do projeto para incluir uma chamada para `AddRazorRuntimeCompilation`. Por exemplo:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Habilitar condicionalmente a compilação em tempo de execução

A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local. Habilitar condicionalmente dessa maneira garante que a saída publicada:

* Usa exibições compiladas.
* É menor em tamanho.
* Não habilita os inspetores de arquivo na produção.

Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:

1. Referencie condicionalmente o pacote [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) com base no valor de `Configuration` ativo:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Atualize o método de `Startup.ConfigureServices` do projeto para incluir uma chamada para `AddRazorRuntimeCompilation`. Execute condicionalmente `AddRazorRuntimeCompilation` de modo que ele só seja executado em um momento, quando a variável `ASPNETCORE_ENVIRONMENT` for definida como `Development`:

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

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
