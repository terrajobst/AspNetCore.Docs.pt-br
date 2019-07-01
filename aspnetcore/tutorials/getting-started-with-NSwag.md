---
title: Introdução ao NSwag e ao ASP.NET Core
author: zuckerthoben
description: Saiba como usar o NSwag para gerar a documentação e as páginas de ajuda para uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: c5b2dc47328d6d3c271a87579fa8c300109bd734
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316552"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="8cdc8-103">Introdução ao NSwag e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8cdc8-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="8cdc8-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) e [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="8cdc8-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8cdc8-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8cdc8-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8cdc8-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8cdc8-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="8cdc8-107">NSwag oferece os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="8cdc8-108">A capacidade de utilizar a interface do usuário do Swagger e o gerador do Swagger.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="8cdc8-109">Recursos flexíveis de geração de código.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="8cdc8-110">Com o NSwag, você não precisa de uma API existente&mdash;, pois pode usar APIs de terceiros que incorporam o Swagger e geram uma implementação de cliente.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="8cdc8-111">O NSwag permite acelerar o ciclo de desenvolvimento e adaptar-se facilmente às alterações na API.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="8cdc8-112">Registrar o middleware do NSwag</span><span class="sxs-lookup"><span data-stu-id="8cdc8-112">Register the NSwag middleware</span></span>

<span data-ttu-id="8cdc8-113">Registre o middleware do NSwag para:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="8cdc8-114">Gerar a especificação do Swagger para a API Web implementada.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="8cdc8-115">Oferecer a interface do usuário do Swagger para navegar e testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="8cdc8-116">Para usar o middleware ASP.NET Core do [NSwag](https://github.com/RicoSuter/NSwag), instale o pacote do NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="8cdc8-117">Este pacote contém o middleware para gerar e oferecer a especificação do Swagger, interface do usuário do Swagger (v2 e v3) e a [interface do usuário do ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="8cdc8-118">Use uma das seguintes abordagens para instalar o pacote do NuGet do NSwag:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8cdc8-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cdc8-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8cdc8-120">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="8cdc8-121">Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="8cdc8-122">Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado</span><span class="sxs-lookup"><span data-stu-id="8cdc8-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="8cdc8-123">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="8cdc8-124">Da caixa de diálogo **Gerenciar Pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="8cdc8-125">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="8cdc8-126">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="8cdc8-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="8cdc8-127">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="8cdc8-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="8cdc8-128">Selecione o pacote "NSwag.AspNetCore" na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8cdc8-129">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8cdc8-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8cdc8-130">Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="8cdc8-131">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="8cdc8-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="8cdc8-132">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="8cdc8-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="8cdc8-133">Selecione o pacote "NSwag.AspNetCore" no painel de resultados e clique em **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8cdc8-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8cdc8-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8cdc8-135">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-135">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8cdc8-136">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="8cdc8-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8cdc8-137">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-137">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="8cdc8-138">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="8cdc8-138">Add and configure Swagger middleware</span></span>

<span data-ttu-id="8cdc8-139">Realize estas etapas para adicionar e configurar o Swagger em seu aplicativo ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-139">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="8cdc8-140">No método `Startup.ConfigureServices`, registre os serviços obrigatórios do Swagger:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-140">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="8cdc8-141">No método `Startup.Configure`, habilite o middleware para atender à especificação do Swagger gerado e à interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-141">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="8cdc8-142">Inicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-142">Launch the app.</span></span> <span data-ttu-id="8cdc8-143">Navegue até:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-143">Navigate to:</span></span>
  * <span data-ttu-id="8cdc8-144">`http://localhost:<port>/swagger` para exibir a interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-144">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="8cdc8-145">`http://localhost:<port>/swagger/v1/swagger.json` para exibir a especificação do Swagger.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-145">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="8cdc8-146">Geração de código</span><span class="sxs-lookup"><span data-stu-id="8cdc8-146">Code generation</span></span>

<span data-ttu-id="8cdc8-147">Você pode tirar proveito dos recursos de geração de código do NSwag, escolhendo uma das opções a seguir:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-147">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="8cdc8-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; um aplicativo da área de trabalho do Windows para geração do código do cliente da API em C# ou TypeScript.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="8cdc8-149">Pacotes NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para a geração de código dentro do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-149">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="8cdc8-150">NSwag na [linha de comando](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-150">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="8cdc8-151">O pacote NuGet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-151">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild) NuGet package.</span></span>
* <span data-ttu-id="8cdc8-152">O [Serviço Conectado do Unchase OpenAPI (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; um Serviço Conectado do Visual Studio para a geração de código do cliente de API em C# ou em TypeScript.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-152">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="8cdc8-153">Também gera controladores C# para serviços de OpenAPI com o NSwag.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-153">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="8cdc8-154">Gerar o código com NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="8cdc8-154">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="8cdc8-155">Instale o NSwagStudio, seguindo as instruções no [repositório GitHub do NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-155">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="8cdc8-156">Inicie o NSwagStudio e insira a URL do arquivo *swagger.json* na caixa de texto **URL de Especificação do Swagger**.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-156">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="8cdc8-157">Por exemplo, *http://localhost:44354/swagger/v1/swagger.json* .</span><span class="sxs-lookup"><span data-stu-id="8cdc8-157">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="8cdc8-158">Clique no botão **Criar Cópia local** para gerar uma representação JSON de sua especificação do Swagger.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-158">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Criar uma cópia local da especificação do Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="8cdc8-160">Na área **Saídas**, marque a caixa de seleção **Cliente CSharp**.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-160">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="8cdc8-161">Dependendo do seu projeto, você também pode escolher **Cliente TypeScript** ou **Controlador da API Web CSharp**.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-161">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="8cdc8-162">Se você selecionar **Controlador da API Web do CSharp**, uma especificação de serviço recompilará o serviço, que servirá como uma geração inversa.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-162">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="8cdc8-163">Clique em **Gerar Saídas** para produzir uma implementação completa de cliente em C# do projeto *TodoApi.NSwag*.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-163">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="8cdc8-164">Para ver o código de cliente gerado, clique na guia **Cliente do CSharp**:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-164">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="8cdc8-165">O código do cliente em C# é gerado com base em seleções na guia **Configurações**. Modifique as configurações para executar tarefas como geração de método síncrono e renomeação de namespace padrão.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-165">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="8cdc8-166">Copie o código C# gerado em um arquivo no projeto do cliente que consumirá a API.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-166">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="8cdc8-167">Inicie o consumo da API Web:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-167">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="8cdc8-168">Personalizar a documentação da API</span><span class="sxs-lookup"><span data-stu-id="8cdc8-168">Customize API documentation</span></span>

<span data-ttu-id="8cdc8-169">O Swagger fornece opções para documentar o modelo de objeto para facilitar o consumo da API Web.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-169">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="8cdc8-170">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="8cdc8-170">API info and description</span></span>

<span data-ttu-id="8cdc8-171">No método `Startup.ConfigureServices`, uma ação de configuração passada para o método `AddSwaggerDocument` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-171">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="8cdc8-172">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-172">The Swagger UI displays the version's information:</span></span>

![Interface do usuário do Swagger com informações de versão](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="8cdc8-174">comentários XML</span><span class="sxs-lookup"><span data-stu-id="8cdc8-174">XML comments</span></span>

<span data-ttu-id="8cdc8-175">Para habilitar os comentários XML, execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-175">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8cdc8-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cdc8-176">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="8cdc8-177">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-177">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="8cdc8-178">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-178">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="8cdc8-179">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-179">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="8cdc8-180">Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-180">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8cdc8-181">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8cdc8-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="8cdc8-182">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-182">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="8cdc8-183">Navegue até **Ferramentas** > **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-183">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="8cdc8-184">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-184">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="8cdc8-185">Abra a caixa de diálogo **Opções do Projeto** > **Compilar**>**Compilador**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-185">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="8cdc8-186">Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**</span><span class="sxs-lookup"><span data-stu-id="8cdc8-186">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="8cdc8-187">Visual Studio Code/CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="8cdc8-187">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="8cdc8-188">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="8cdc8-189">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="8cdc8-189">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8cdc8-190">Como o NSwag usa [Reflexão](/dotnet/csharp/programming-guide/concepts/reflection), e o tipo recomendado de retorno para ações da API Web é [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), ele não consegue inferir o que sua ação está fazendo e o que ela retorna.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-190">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="8cdc8-191">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-191">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="8cdc8-192">A ação anterior retorna `IActionResult`, mas dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) ou [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-192">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="8cdc8-193">Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-193">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="8cdc8-194">Decore a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-194">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 <span data-ttu-id="8cdc8-195">Como o NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), e o tipo de retorno recomendado para as ações da API Web é [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), ele só consegue inferir o tipo de retorno definido por `T`.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-195">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="8cdc8-196">Não é possível inferir automaticamente outros tipos de retorno possíveis.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-196">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="8cdc8-197">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-197">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="8cdc8-198">A ação anterior retorna `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-198">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="8cdc8-199">Dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-199">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="8cdc8-200">Como o controlador está decorado com o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute), uma resposta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) também é possível.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-200">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="8cdc8-201">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-201">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="8cdc8-202">Use anotações de dados para informar aos clientes quais códigos de status HTTP esta ação costuma retornar.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-202">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="8cdc8-203">Decore a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="8cdc8-203">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="8cdc8-204">No ASP.NET Core 2.2 ou posterior, é possível usar as convenções em vez de decorar explicitamente as ações individuais com `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-204">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="8cdc8-205">Para obter mais informações, consulte <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-205">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="8cdc8-206">O gerador do Swagger agora pode descrever essa ação precisamente e os clientes gerados conseguem saber o que recebem ao chamar o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-206">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="8cdc8-207">Recomendamos decorar todas as ações com esses atributos.</span><span class="sxs-lookup"><span data-stu-id="8cdc8-207">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="8cdc8-208">Para obter diretrizes sobre quais respostas HTTP as ações da API devem retornar, confira a [Especificação RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="8cdc8-208">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
