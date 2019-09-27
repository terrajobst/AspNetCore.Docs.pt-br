---
title: Introdução ao Swashbuckle e ao ASP.NET Core
author: zuckerthoben
description: Saiba como adicionar o Swashbuckle ao seu projeto de API Web ASP.NET Core para integrar a interface do usuário do Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/21/2019
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: b96d15e732b78fd834e16347739b5a3a2ba2e98d
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317784"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="66ea6-103">Introdução ao Swashbuckle e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66ea6-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="66ea6-104">Por [Shayne Boyer](https://twitter.com/spboyer) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="66ea6-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="66ea6-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66ea6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="66ea6-106">Há três componentes principais bo Swashbuckle:</span><span class="sxs-lookup"><span data-stu-id="66ea6-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="66ea6-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): um modelo de objeto e um middleware do Swagger para expor objetos `SwaggerDocument` como pontos de extremidade JSON.</span><span class="sxs-lookup"><span data-stu-id="66ea6-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="66ea6-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): um gerador do Swagger cria objetos `SwaggerDocument` diretamente de modelos, controladores e rotas.</span><span class="sxs-lookup"><span data-stu-id="66ea6-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="66ea6-109">Normalmente, ele é combinado com o middleware de ponto de extremidade do Swagger para expor automaticamente o JSON do Swagger.</span><span class="sxs-lookup"><span data-stu-id="66ea6-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="66ea6-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): uma versão incorporada da ferramenta de interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="66ea6-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="66ea6-111">Ele interpreta o JSON do Swagger para criar uma experiência avançada e personalizável para descrever a funcionalidade da API Web.</span><span class="sxs-lookup"><span data-stu-id="66ea6-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="66ea6-112">Ela inclui o agente de teste interno para os métodos públicos.</span><span class="sxs-lookup"><span data-stu-id="66ea6-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="66ea6-113">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="66ea6-113">Package installation</span></span>

<span data-ttu-id="66ea6-114">O Swashbuckle pode ser adicionado com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="66ea6-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="66ea6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66ea6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="66ea6-116">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="66ea6-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="66ea6-117">Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="66ea6-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="66ea6-118">Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado</span><span class="sxs-lookup"><span data-stu-id="66ea6-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="66ea6-119">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="66ea6-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0-rc3
    ```

* <span data-ttu-id="66ea6-120">Da caixa de diálogo **Gerenciar Pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="66ea6-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="66ea6-121">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="66ea6-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="66ea6-122">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="66ea6-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="66ea6-123">Verifique se a opção "Incluir pré-lançamento" está habilitada</span><span class="sxs-lookup"><span data-stu-id="66ea6-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="66ea6-124">Insira "Swashbuckle.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="66ea6-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="66ea6-125">Selecione o pacote "Swashbuckle.AspNetCore" mais recente na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="66ea6-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="66ea6-126">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="66ea6-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="66ea6-127">Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**</span><span class="sxs-lookup"><span data-stu-id="66ea6-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="66ea6-128">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="66ea6-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="66ea6-129">Verifique se a opção "Mostrar pacotes de pré-lançamento" está habilitada</span><span class="sxs-lookup"><span data-stu-id="66ea6-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="66ea6-130">Insira "Swashbuckle.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="66ea6-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="66ea6-131">Selecione o pacote "Swashbuckle.AspNetCore" mais recente no painel de resultados e clique em **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="66ea6-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="66ea6-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66ea6-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66ea6-133">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="66ea6-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc3
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="66ea6-134">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="66ea6-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="66ea6-135">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="66ea6-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0-rc3
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="66ea6-136">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="66ea6-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="66ea6-137">Na classe `Startup`, importe o namespace a seguir para usar a classe `OpenApiInfo`:</span><span class="sxs-lookup"><span data-stu-id="66ea6-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="66ea6-138">Adicione o gerador do Swagger à coleção de serviços no método `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="66ea6-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="66ea6-139">No método `Startup.Configure`, habilite o middleware para atender ao documento JSON gerado e à interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="66ea6-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="66ea6-140">A chamada do método `UseSwaggerUI` precedente habilita o [middleware de arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="66ea6-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="66ea6-141">Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="66ea6-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="66ea6-142">Inicie o aplicativo e navegue até `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="66ea6-143">O documento gerado que descreve os pontos de extremidade é exibido conforme é mostrado na [Especificação do Swagger (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="66ea6-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="66ea6-144">A interface do usuário do Swagger pode ser encontrada em `http://localhost:<port>/swagger`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="66ea6-145">Explore a API por meio da interface do usuário do Swagger e incorpore-a em outros programas.</span><span class="sxs-lookup"><span data-stu-id="66ea6-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="66ea6-146">Para atender à interface do usuário do Swagger na raiz do aplicativo (`http://localhost:<port>/`), defina a propriedade `RoutePrefix` como uma cadeia de caracteres vazia:</span><span class="sxs-lookup"><span data-stu-id="66ea6-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="66ea6-147">Se estiver usando diretórios com o IIS ou um proxy reverso, defina o ponto de extremidade do Swagger como um caminho relativo usando o prefixo `./`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="66ea6-148">Por exemplo: `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="66ea6-149">Usar o `/swagger/v1/swagger.json` instrui o aplicativo a procurar o arquivo JSON na raiz verdadeira da URL (mais o prefixo da rota, se usado).</span><span class="sxs-lookup"><span data-stu-id="66ea6-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="66ea6-150">Por exemplo, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` em vez de `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="66ea6-151">Personalizar e estender</span><span class="sxs-lookup"><span data-stu-id="66ea6-151">Customize and extend</span></span>

<span data-ttu-id="66ea6-152">O Swagger fornece opções para documentar o modelo de objeto e personalizar a interface do usuário para corresponder ao seu tema.</span><span class="sxs-lookup"><span data-stu-id="66ea6-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="66ea6-153">Na classe `Startup`, adicione os seguintes namespaces:</span><span class="sxs-lookup"><span data-stu-id="66ea6-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="66ea6-154">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="66ea6-154">API info and description</span></span>

<span data-ttu-id="66ea6-155">A ação de configuração passada para o método `AddSwaggerGen` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="66ea6-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="66ea6-156">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="66ea6-156">The Swagger UI displays the version's information:</span></span>

![A interface do usuário do Swagger com informações de versão: descrição, autor e link veja mais](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="66ea6-158">comentários XML</span><span class="sxs-lookup"><span data-stu-id="66ea6-158">XML comments</span></span>

<span data-ttu-id="66ea6-159">Comentários XML podem ser habilitados com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="66ea6-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="66ea6-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66ea6-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="66ea6-161">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="66ea6-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="66ea6-162">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="66ea6-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="66ea6-163">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="66ea6-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="66ea6-164">Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**.</span><span class="sxs-lookup"><span data-stu-id="66ea6-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="66ea6-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="66ea6-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="66ea6-166">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="66ea6-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="66ea6-167">Navegue até **Ferramentas** > **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="66ea6-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="66ea6-168">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="66ea6-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="66ea6-169">Abra a caixa de diálogo **Opções do Projeto** > **Compilar**>**Compilador**</span><span class="sxs-lookup"><span data-stu-id="66ea6-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="66ea6-170">Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**</span><span class="sxs-lookup"><span data-stu-id="66ea6-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="66ea6-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66ea6-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66ea6-172">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="66ea6-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="66ea6-173">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="66ea6-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="66ea6-174">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="66ea6-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="66ea6-175">A habilitação de comentários XML fornece informações de depuração para os membros e os tipos públicos não documentados.</span><span class="sxs-lookup"><span data-stu-id="66ea6-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="66ea6-176">Os membros e tipos não documentados são indicados por mensagem de aviso.</span><span class="sxs-lookup"><span data-stu-id="66ea6-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="66ea6-177">Por exemplo, a seguinte mensagem indica uma violação do código de aviso 1591:</span><span class="sxs-lookup"><span data-stu-id="66ea6-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="66ea6-178">Para suprimir os avisos de todo o projeto, defina uma lista separada por ponto e vírgula dos códigos de aviso a serem ignorados no arquivo do projeto.</span><span class="sxs-lookup"><span data-stu-id="66ea6-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="66ea6-179">Acrescentar os códigos de aviso ao `$(NoWarn);` também aplica os [valores padrão C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="66ea6-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="66ea6-180">Para suprimir avisos somente para membros específicos, coloque o código nas diretivas de pré-processador [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning).</span><span class="sxs-lookup"><span data-stu-id="66ea6-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="66ea6-181">Essa abordagem é útil para o código que não deve ser exposto por meio dos documentos da API. No exemplo a seguir, o código de aviso CS1591 é ignorado para toda a classe `Program`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="66ea6-182">A imposição do código de aviso é restaurada no fechamento da definição de classe.</span><span class="sxs-lookup"><span data-stu-id="66ea6-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="66ea6-183">Especifique vários códigos de aviso com uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="66ea6-183">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="66ea6-184">Configure o Swagger para usar o arquivo XML gerado com as instruções anteriores.</span><span class="sxs-lookup"><span data-stu-id="66ea6-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="66ea6-185">Para sistemas operacionais Linux ou que não sejam Windows, os caminhos e nomes de arquivo podem diferenciar maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="66ea6-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="66ea6-186">Por exemplo, um arquivo *TodoApi.XML* é válido no Windows, mas não no CentOS.</span><span class="sxs-lookup"><span data-stu-id="66ea6-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="66ea6-187">No código anterior, a [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection) é usada para criar um nome de arquivo XML correspondente ao do projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="66ea6-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="66ea6-188">A propriedade [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) é usada para construir um caminho para o arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="66ea6-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="66ea6-189">Alguns recursos do Swagger (como os esquemas de parâmetros de entrada ou dos métodos HTTP e os códigos de resposta dos respectivos atributos) funcionam sem o uso de um arquivo de documentação XML.</span><span class="sxs-lookup"><span data-stu-id="66ea6-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="66ea6-190">Para a maioria dos recursos, ou seja, resumos de método e descrições dos parâmetros e códigos de resposta, é obrigatório o uso de um arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="66ea6-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="66ea6-191">Adicionar comentários de barra tripla a uma ação aprimora a interface do usuário do Swagger adicionando a descrição ao cabeçalho da seção.</span><span class="sxs-lookup"><span data-stu-id="66ea6-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="66ea6-192">Adicione um elemento [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) acima da ação `Delete`:</span><span class="sxs-lookup"><span data-stu-id="66ea6-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="66ea6-193">A interface do usuário do Swagger exibe o texto interno do elemento `<summary>` do código anterior:</span><span class="sxs-lookup"><span data-stu-id="66ea6-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![A interface do usuário do Swagger, mostrando o comentário XML 'Exclui um TodoItem específico'.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="66ea6-196">A interface do usuário é controlada pelo esquema JSON gerado:</span><span class="sxs-lookup"><span data-stu-id="66ea6-196">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="66ea6-197">Adicione um elemento [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) na documentação do método da ação `Create`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="66ea6-198">Ele complementa as informações especificadas no elemento `<summary>` e fornece uma interface de usuário do Swagger mais robusta.</span><span class="sxs-lookup"><span data-stu-id="66ea6-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="66ea6-199">O conteúdo do elemento `<remarks>` pode consistir em texto, JSON ou XML.</span><span class="sxs-lookup"><span data-stu-id="66ea6-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="66ea6-200">Observe os aprimoramentos da interface do usuário com esses comentários adicionais:</span><span class="sxs-lookup"><span data-stu-id="66ea6-200">Notice the UI enhancements with these additional comments:</span></span>

![Interface do usuário do Swagger com comentários adicionais mostrados](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="66ea6-202">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="66ea6-202">Data annotations</span></span>

<span data-ttu-id="66ea6-203">Decore o modelo com atributos, encontrados no namespace [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), para ajudar a gerar os componentes da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="66ea6-203">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="66ea6-204">Adicione o atributo `[Required]` à propriedade `Name` da classe `TodoItem`:</span><span class="sxs-lookup"><span data-stu-id="66ea6-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="66ea6-205">A presença desse atributo altera o comportamento da interface do usuário e altera o esquema JSON subjacente:</span><span class="sxs-lookup"><span data-stu-id="66ea6-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="66ea6-206">Adicione o atributo `[Produces("application/json")]` ao controlador da API.</span><span class="sxs-lookup"><span data-stu-id="66ea6-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="66ea6-207">Sua finalidade é declarar que as ações do controlador permitem o tipo de conteúdo de resposta *application/json*:</span><span class="sxs-lookup"><span data-stu-id="66ea6-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="66ea6-208">A lista suspensa **Tipo de Conteúdo de Resposta** seleciona esse tipo de conteúdo como o padrão para ações GET do controlador:</span><span class="sxs-lookup"><span data-stu-id="66ea6-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interface do usuário do Swagger com o tipo de conteúdo de resposta padrão](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="66ea6-210">À medida que aumenta o uso de anotações de dados na API Web, a interface do usuário e as páginas de ajuda da API se tornam mais descritivas e úteis.</span><span class="sxs-lookup"><span data-stu-id="66ea6-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="66ea6-211">Descrever os tipos de resposta</span><span class="sxs-lookup"><span data-stu-id="66ea6-211">Describe response types</span></span>

<span data-ttu-id="66ea6-212">Os desenvolvedores que usam uma API Web estão mais preocupados com o que é retornado&mdash;, especificamente, os tipos de resposta e os códigos de erro (se eles não forem padrão).</span><span class="sxs-lookup"><span data-stu-id="66ea6-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="66ea6-213">Os tipos de resposta e os códigos de erro são indicados nos comentários XML e nas anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="66ea6-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="66ea6-214">A ação `Create` retorna um código de status HTTP 201 em caso de sucesso.</span><span class="sxs-lookup"><span data-stu-id="66ea6-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="66ea6-215">Um código de status HTTP 400 é retornado quando o corpo da solicitação postada é nulo.</span><span class="sxs-lookup"><span data-stu-id="66ea6-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="66ea6-216">Sem a documentação adequada na interface do usuário do Swagger, o consumidor não tem conhecimento desses resultados esperados.</span><span class="sxs-lookup"><span data-stu-id="66ea6-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="66ea6-217">Corrija esse problema adicionando as linhas realçadas no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="66ea6-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="66ea6-218">A interface do usuário do Swagger agora documenta claramente os códigos de resposta HTTP esperados:</span><span class="sxs-lookup"><span data-stu-id="66ea6-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![A interface do usuário do Swagger mostra a descrição da classe de resposta POST, 'Retorna o item de tarefa pendente recém-criado' e '400 – se o item for nulo' para o código de status e o motivo em Mensagens de Resposta](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="66ea6-220">No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="66ea6-221">Para obter mais informações, consulte <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="66ea6-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="66ea6-222">Personalizar a interface do usuário</span><span class="sxs-lookup"><span data-stu-id="66ea6-222">Customize the UI</span></span>

<span data-ttu-id="66ea6-223">A interface do usuário de estoque é apresentável e funcional.</span><span class="sxs-lookup"><span data-stu-id="66ea6-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="66ea6-224">No entanto, as páginas de documentação da API devem representar a sua marca ou o seu tema.</span><span class="sxs-lookup"><span data-stu-id="66ea6-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="66ea6-225">Para inserir sua marca nos componentes do Swashbuckle é necessário adicionar recursos para atender aos arquivos estáticos e criar a estrutura de pasta para hospedar esses arquivos.</span><span class="sxs-lookup"><span data-stu-id="66ea6-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="66ea6-226">Se você estiver direcionando ao .NET Framework ou ao .NET Core 1.x, adicione o pacote do NuGet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) ao projeto:</span><span class="sxs-lookup"><span data-stu-id="66ea6-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="66ea6-227">O pacote do NuGet anterior já estará instalado se você estiver direcionando ao .NET Core 2.x e usando o [metapackage](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="66ea6-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="66ea6-228">Habilitar o middleware de arquivos estáticos:</span><span class="sxs-lookup"><span data-stu-id="66ea6-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="66ea6-229">Adquira o conteúdo da pasta *dist* do [repositório GitHub da interface do usuário do Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="66ea6-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="66ea6-230">Essa pasta contém os ativos necessários para a página da interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="66ea6-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="66ea6-231">Crie uma pasta *swagger/wwwroot/ui* e copie para ela o conteúdo da pasta *dist*.</span><span class="sxs-lookup"><span data-stu-id="66ea6-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="66ea6-232">Crie um arquivo *custom.css* em *swagger/wwwroot/ui*, com o seguinte CSS para personalizar o cabeçalho da página:</span><span class="sxs-lookup"><span data-stu-id="66ea6-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="66ea6-233">Referencie *custom.css* no arquivo *index.html* dentro da pasta da interface do usuário depois de todos os outros arquivos CSS:</span><span class="sxs-lookup"><span data-stu-id="66ea6-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="66ea6-234">Navegue até a página *index.html* em `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="66ea6-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="66ea6-235">Digite `https://localhost:<port>/swagger/v1/swagger.json` na caixa de texto do cabeçalho e clique no botão **Explorar**.</span><span class="sxs-lookup"><span data-stu-id="66ea6-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="66ea6-236">A página resultante será semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="66ea6-236">The resulting page looks as follows:</span></span>

![Interface do usuário do Swagger com o título do cabeçalho personalizado](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="66ea6-238">Há muito mais que você pode fazer com a página.</span><span class="sxs-lookup"><span data-stu-id="66ea6-238">There's much more you can do with the page.</span></span> <span data-ttu-id="66ea6-239">Consulte as funcionalidades completas para os recursos de interface do usuário no [repositório GitHub da interface do usuário do Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="66ea6-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
