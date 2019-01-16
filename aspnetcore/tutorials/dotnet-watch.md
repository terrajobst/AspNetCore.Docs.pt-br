---
title: Desenvolver aplicativos ASP.NET Core usando um observador de arquivo
author: rick-anderson
description: Este tutorial demonstra como instalar e usar a ferramenta observador de arquivo (observação de dotnet) da CLI do .NET Core em um aplicativo do ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: f1e0d91b27df4af7cbfb6f2547c94c0370c65d0d
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207496"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="39ad5-103">Desenvolver aplicativos ASP.NET Core usando um observador de arquivo</span><span class="sxs-lookup"><span data-stu-id="39ad5-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="39ad5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="39ad5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="39ad5-105">`dotnet watch` é uma ferramenta que executa um comando do [CLI do .NET Core](/dotnet/core/tools) quando os arquivos de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="39ad5-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="39ad5-106">Por exemplo, uma alteração de arquivo pode disparar uma compilação, execução de teste ou uma implantação.</span><span class="sxs-lookup"><span data-stu-id="39ad5-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="39ad5-107">Este tutorial usa um aplicativo de API Web existente com dois pontos de extremidade: um que retorna uma soma e outro que retorna um produto.</span><span class="sxs-lookup"><span data-stu-id="39ad5-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="39ad5-108">O método de produto tem um bug, que é corrigido neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="39ad5-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="39ad5-109">Baixe o [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="39ad5-109">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="39ad5-110">Ele é composto por dois projetos: *WebApp* (uma API Web do ASP.NET Core) e *WebAppTests* (testes de unidade para a API Web).</span><span class="sxs-lookup"><span data-stu-id="39ad5-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="39ad5-111">Em um shell de comando, navegue até a pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="39ad5-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="39ad5-112">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="39ad5-112">Run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="39ad5-113">O resultado do console mostra mensagens semelhantes à seguinte (indicando que o aplicativo está em execução e aguarda solicitações):</span><span class="sxs-lookup"><span data-stu-id="39ad5-113">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="39ad5-114">Em um navegador da Web, navegue até `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="39ad5-114">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="39ad5-115">Você deve ver o resultado de `9`.</span><span class="sxs-lookup"><span data-stu-id="39ad5-115">You should see the result of `9`.</span></span>

<span data-ttu-id="39ad5-116">Navegue para o API do produto (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="39ad5-116">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="39ad5-117">Ele retorna `9`, não `20`, conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="39ad5-117">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="39ad5-118">Esse problema é corrigido mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="39ad5-118">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="39ad5-119">Adicionar `dotnet watch` a um projeto</span><span class="sxs-lookup"><span data-stu-id="39ad5-119">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="39ad5-120">A ferramenta de observador de arquivo `dotnet watch` está incluída com a versão 2.1.300 do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="39ad5-120">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="39ad5-121">As etapas a seguir são necessárias ao usar uma versão anterior do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="39ad5-121">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="39ad5-122">Adicionar uma referência ao pacote de `Microsoft.DotNet.Watcher.Tools` para o arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="39ad5-122">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="39ad5-123">Instale o pacote `Microsoft.DotNet.Watcher.Tools` executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="39ad5-123">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```console
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="39ad5-124">Executar os comandos da CLI do .NET Core usando `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="39ad5-124">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="39ad5-125">Qualquer [comando da CLI do .NET Core](/dotnet/core/tools#cli-commands) pode ser executado com `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="39ad5-125">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="39ad5-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="39ad5-126">For example:</span></span>

| <span data-ttu-id="39ad5-127">Comando</span><span class="sxs-lookup"><span data-stu-id="39ad5-127">Command</span></span> | <span data-ttu-id="39ad5-128">Comando com inspeção</span><span class="sxs-lookup"><span data-stu-id="39ad5-128">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="39ad5-129">dotnet run</span><span class="sxs-lookup"><span data-stu-id="39ad5-129">dotnet run</span></span> | <span data-ttu-id="39ad5-130">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="39ad5-130">dotnet watch run</span></span> |
| <span data-ttu-id="39ad5-131">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="39ad5-131">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="39ad5-132">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="39ad5-132">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="39ad5-133">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="39ad5-133">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="39ad5-134">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="39ad5-134">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="39ad5-135">dotnet test</span><span class="sxs-lookup"><span data-stu-id="39ad5-135">dotnet test</span></span> | <span data-ttu-id="39ad5-136">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="39ad5-136">dotnet watch test</span></span> |

<span data-ttu-id="39ad5-137">Executar `dotnet watch run` na pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="39ad5-137">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="39ad5-138">O resultado do console indica que `watch` foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="39ad5-138">The console output indicates `watch` has started.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="39ad5-139">Fazer alterações com `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="39ad5-139">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="39ad5-140">Verifique se `dotnet watch` está em execução.</span><span class="sxs-lookup"><span data-stu-id="39ad5-140">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="39ad5-141">Corrija o bug no método `Product` do *MathController.cs* para que ele retorne o produto e não a soma:</span><span class="sxs-lookup"><span data-stu-id="39ad5-141">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
}
```

<span data-ttu-id="39ad5-142">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="39ad5-142">Save the file.</span></span> <span data-ttu-id="39ad5-143">O resultado do console indica que o `dotnet watch` detectou uma alteração de arquivo e reiniciou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39ad5-143">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="39ad5-144">Verifique se `http://localhost:<port number>/api/math/product?a=4&b=5` retorna o resultado correto.</span><span class="sxs-lookup"><span data-stu-id="39ad5-144">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="39ad5-145">Executar testes usando o `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="39ad5-145">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="39ad5-146">Altere o método `Product` de *MathController.cs* de volta para retornar a soma.</span><span class="sxs-lookup"><span data-stu-id="39ad5-146">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="39ad5-147">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="39ad5-147">Save the file.</span></span>
1. <span data-ttu-id="39ad5-148">Em um shell de comando, navegue até a pasta *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="39ad5-148">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="39ad5-149">Execute [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="39ad5-149">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="39ad5-150">Execute `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="39ad5-150">Run `dotnet watch test`.</span></span> <span data-ttu-id="39ad5-151">Seu resultado indica que um teste falhou e que o observador está aguardando as alterações de arquivo:</span><span class="sxs-lookup"><span data-stu-id="39ad5-151">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="39ad5-152">Corrija o código do método `Product` para que ele retorne o produto.</span><span class="sxs-lookup"><span data-stu-id="39ad5-152">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="39ad5-153">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="39ad5-153">Save the file.</span></span>

<span data-ttu-id="39ad5-154">`dotnet watch` detecta a alteração de arquivo e executa os testes novamente.</span><span class="sxs-lookup"><span data-stu-id="39ad5-154">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="39ad5-155">O resultado do console indica a aprovação nos testes.</span><span class="sxs-lookup"><span data-stu-id="39ad5-155">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="39ad5-156">Personalizar lista de arquivos a observar</span><span class="sxs-lookup"><span data-stu-id="39ad5-156">Customize files list to watch</span></span>

<span data-ttu-id="39ad5-157">Por padrão, o `dotnet-watch` controla todos os arquivos que correspondem aos seguintes padrões glob:</span><span class="sxs-lookup"><span data-stu-id="39ad5-157">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="39ad5-158">Mais itens podem ser adicionados à lista de inspeção editando o arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="39ad5-158">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="39ad5-159">Os itens podem ser especificados individualmente ou usando padrões glob.</span><span class="sxs-lookup"><span data-stu-id="39ad5-159">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="39ad5-160">Recusa de arquivos a serem observados</span><span class="sxs-lookup"><span data-stu-id="39ad5-160">Opt-out of files to be watched</span></span>

<span data-ttu-id="39ad5-161">`dotnet-watch` pode ser configurado para ignorar as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="39ad5-161">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="39ad5-162">Para ignorar arquivos específicos, adicione o atributo `Watch="false"` à definição de um item no arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="39ad5-162">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="39ad5-163">Projetos de inspeção personalizados</span><span class="sxs-lookup"><span data-stu-id="39ad5-163">Custom watch projects</span></span>

<span data-ttu-id="39ad5-164">`dotnet-watch` não é restrito a projetos C#.</span><span class="sxs-lookup"><span data-stu-id="39ad5-164">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="39ad5-165">Projetos de inspeção personalizados podem ser criados para lidar com cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="39ad5-165">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="39ad5-166">Considere o layout de projeto a seguir:</span><span class="sxs-lookup"><span data-stu-id="39ad5-166">Consider the following project layout:</span></span>

* <span data-ttu-id="39ad5-167">**test/**</span><span class="sxs-lookup"><span data-stu-id="39ad5-167">**test/**</span></span>
  * <span data-ttu-id="39ad5-168">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="39ad5-168">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="39ad5-169">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="39ad5-169">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="39ad5-170">Se a meta é observar ambos os projetos, crie um arquivo de projeto personalizados configurado para observar os dois projetos:</span><span class="sxs-lookup"><span data-stu-id="39ad5-170">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="39ad5-171">Para iniciar a observação de arquivo em ambos os projetos, mude para a pasta de *teste*.</span><span class="sxs-lookup"><span data-stu-id="39ad5-171">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="39ad5-172">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="39ad5-172">Execute the following command:</span></span>

```console
dotnet watch msbuild /t:Test
```

<span data-ttu-id="39ad5-173">O VSTest é executado quando há qualquer mudança de arquivo no projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="39ad5-173">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="39ad5-174">`dotnet-watch` no GitHub</span><span class="sxs-lookup"><span data-stu-id="39ad5-174">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="39ad5-175">`dotnet-watch` faz parte do [repositório aspnet/AspNetCore](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch) do GitHub.</span><span class="sxs-lookup"><span data-stu-id="39ad5-175">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
