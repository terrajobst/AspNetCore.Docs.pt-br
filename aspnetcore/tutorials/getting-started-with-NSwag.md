---
title: Introdução ao NSwag e ao ASP.NET Core
author: zuckerthoben
description: Saiba como usar o NSwag para gerar a documentação e as páginas de ajuda para uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8af5bed1e042c4f6d83043b05084c51b3064a548
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595354"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="5c2a8-103">Introdução ao NSwag e ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c2a8-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="5c2a8-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben) e [Rico Suter](https://rsuter.com)</span><span class="sxs-lookup"><span data-stu-id="5c2a8-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://rsuter.com)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5c2a8-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c2a8-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5c2a8-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c2a8-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="5c2a8-107">Registre os middlewares NSwag para:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-107">Register the NSwag middlewares to:</span></span>

* <span data-ttu-id="5c2a8-108">Gerar a especificação do Swagger para a API Web implementada.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-108">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="5c2a8-109">Oferecer a interface do usuário do Swagger para navegar e testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-109">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="5c2a8-110">Para usar os middlewares ASP.NET Core do [NSwag](https://github.com/RSuter/NSwag), instale o pacote do NuGet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-110">To use the [NSwag](https://github.com/RSuter/NSwag) ASP.NET Core middlewares, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="5c2a8-111">Este pacote contém os middlewares para gerar e oferecer a especificação do Swagger, interface do usuário do Swagger (v2 e v3) e a [interface do usuário do ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-111">This package contains the middlewares to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="5c2a8-112">Além disso, é altamente recomendável usar as funcionalidades de geração de código do NSwag.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-112">Additionally, it's highly recommended to make use of NSwag's code generation capabilities.</span></span> <span data-ttu-id="5c2a8-113">Escolha uma das seguintes opções para usar as funcionalidades de geração de código:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-113">Choose one of the following options to use the code generation capabilities:</span></span>

* <span data-ttu-id="5c2a8-114">Use o [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), um aplicativo da área de trabalho do Windows para a geração de código do cliente em C# e em TypeScript para sua API.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-114">Use [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), a Windows desktop app for generating client code in C# and TypeScript for your API.</span></span>
* <span data-ttu-id="5c2a8-115">Use os pacotes do NuGet [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) ou [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) para a geração de código dentro do projeto.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-115">Use the [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages to do code generation inside your project.</span></span>
* <span data-ttu-id="5c2a8-116">Use o NSwag na [linha de comando](https://github.com/NSwag/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-116">Use NSwag from the [command line](https://github.com/NSwag/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="5c2a8-117">Use o pacote NuGet [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-117">Use the [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) NuGet package.</span></span>

## <a name="features"></a><span data-ttu-id="5c2a8-118">Recursos</span><span class="sxs-lookup"><span data-stu-id="5c2a8-118">Features</span></span>

<span data-ttu-id="5c2a8-119">O principal motivo para usar o NSwag é que, além de apresentar a interface do usuário do Swagger e o gerador do Swagger, também é possível usar recursos flexíveis para geração de código.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-119">The main reason to use NSwag is the ability to not only introduce the Swagger UI and Swagger generator, but to also make use of the flexible code generation capabilities.</span></span> <span data-ttu-id="5c2a8-120">Você não precisa de uma API existente. É possível usar APIs de terceiros que incorporam o Swagger e permitem que o NSwag gere uma implementação de cliente.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-120">You don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and let NSwag generate a client implementation.</span></span> <span data-ttu-id="5c2a8-121">De qualquer forma, o ciclo de desenvolvimento é acelerado e você pode se adaptar às alterações da API com mais facilidade.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-121">Either way, the development cycle is expedited and you can more easily adapt to API changes.</span></span>

## <a name="package-installation"></a><span data-ttu-id="5c2a8-122">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="5c2a8-122">Package installation</span></span>

<span data-ttu-id="5c2a8-123">O pacote do NuGet do NSwag pode ser adicionado com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-123">The NSwag NuGet package can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c2a8-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c2a8-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c2a8-125">Da janela **Console do Gerenciador de Pacotes**:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-125">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="5c2a8-126">Acesse **Exibição** > **Outras Janelas** > **Console do Gerenciador de Pacotes**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-126">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="5c2a8-127">Navegue para o diretório no qual o arquivo *TodoApi.csproj* está localizado</span><span class="sxs-lookup"><span data-stu-id="5c2a8-127">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="5c2a8-128">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-128">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="5c2a8-129">Da caixa de diálogo **Gerenciar Pacotes NuGet**:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-129">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="5c2a8-130">Clique com o botão direito do mouse no projeto em **Gerenciador de Soluções** > **Gerenciar Pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-130">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="5c2a8-131">Defina a **Origem do pacote** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="5c2a8-131">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="5c2a8-132">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="5c2a8-132">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="5c2a8-133">Selecione o pacote "NSwag.AspNetCore" na guia **Procurar** e clique em **Instalar**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-133">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5c2a8-134">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5c2a8-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5c2a8-135">Clique com o botão direito do mouse na pasta *Pacotes* em **Painel de Soluções** > **Adicionar Pacotes...**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-135">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="5c2a8-136">Defina a lista suspensa **Origem** da janela **Adicionar Pacotes** para "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="5c2a8-136">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="5c2a8-137">Insira "NSwag.AspNetCore" na caixa de pesquisa</span><span class="sxs-lookup"><span data-stu-id="5c2a8-137">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="5c2a8-138">Selecione o pacote "NSwag.AspNetCore" no painel de resultados e clique em **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-138">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c2a8-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c2a8-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5c2a8-140">Execute o comando a seguir do **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-140">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5c2a8-141">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="5c2a8-141">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5c2a8-142">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-142">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="5c2a8-143">Adicionar e configurar o middleware do Swagger</span><span class="sxs-lookup"><span data-stu-id="5c2a8-143">Add and configure Swagger middleware</span></span>

<span data-ttu-id="5c2a8-144">Importe os seguintes namespaces na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-144">Import the following namespaces in the `Startup` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

<span data-ttu-id="5c2a8-145">No método `Startup.ConfigureServices`, registre os serviços obrigatórios do Swagger:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-145">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span> 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="5c2a8-146">No método `Startup.Configure`, habilite o middleware para atender à especificação do Swagger gerado e à interface do usuário do Swagger v3:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-146">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI v3:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

<span data-ttu-id="5c2a8-147">Inicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-147">Launch the app.</span></span> <span data-ttu-id="5c2a8-148">Navegue até `http://localhost:<port>/swagger` para exibir a interface do usuário do Swagger.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-148">Navigate to `http://localhost:<port>/swagger` to view the Swagger UI.</span></span> <span data-ttu-id="5c2a8-149">Navegue até `http://localhost:<port>/swagger/v1/swagger.json` para exibir a especificação do Swagger.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-149">Navigate to `http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="5c2a8-150">Geração de código</span><span class="sxs-lookup"><span data-stu-id="5c2a8-150">Code generation</span></span>

### <a name="via-nswagstudio"></a><span data-ttu-id="5c2a8-151">Por meio do NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="5c2a8-151">Via NSwagStudio</span></span>

* <span data-ttu-id="5c2a8-152">Instale o NSwagStudio por meio do [repositório GitHub](https://github.com/RSuter/NSwag/wiki/NSwagStudio) oficial.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-152">Install NSwagStudio from the official [GitHub repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="5c2a8-153">Inicie o NSwagStudio.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-153">Launch NSwagStudio.</span></span> <span data-ttu-id="5c2a8-154">Insira a URL do arquivo *swagger.json* na caixa de texto **URL de Especificação do Swagger** e clique no botão **Criar Cópia local**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-154">Enter the *swagger.json* file URL in the **Swagger Specification URL** textbox, and click the **Create local Copy** button.</span></span>
* <span data-ttu-id="5c2a8-155">Selecione o tipo de saída do cliente **Cliente do CSharp**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-155">Select the **CSharp Client** client output type.</span></span> <span data-ttu-id="5c2a8-156">Outras opções incluem **Cliente do TypeScript** e **Controlador da API Web do CSharp**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-156">Other options include **TypeScript Client** and **CSharp Web API Controller**.</span></span> <span data-ttu-id="5c2a8-157">O uso de um controlador da API Web é basicamente uma geração inversa.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-157">Using a Web API Controller is basically a reverse generation.</span></span> <span data-ttu-id="5c2a8-158">Ele usa uma especificação de um serviço para recriar o serviço.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-158">It uses a specification of a service to rebuild the service.</span></span>
* <span data-ttu-id="5c2a8-159">Clique no botão **Gerar Saídas**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-159">Click the **Generate Outputs** button.</span></span> <span data-ttu-id="5c2a8-160">Uma implementação completa de cliente em C# do projeto *TodoApi.NSwag* é produzida.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-160">A complete C# client implementation of the *TodoApi.NSwag* project is produced.</span></span> <span data-ttu-id="5c2a8-161">Clique na guia **Cliente do CSharp** da seção **Saídas** para ver o código de cliente gerado:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-161">Click the **CSharp Client** tab of the **Outputs** section to see the generated client code:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable // Disable all warnings

    [System.CodeDom.Compiler.GeneratedCode("NSwag",
        "11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "http://localhost:50499";
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient()
        {
            _settings = new System.Lazy
                <Newtonsoft.Json.JsonSerializerSettings>(() =>
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
> <span data-ttu-id="5c2a8-162">O código de cliente em C# é gerado com base nas configurações definidas na guia **Configurações** da guia **Cliente do CSharp**. Modifique as configurações para executar tarefas como geração de método síncrono e renomeação de namespace padrão.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-162">The C# client code is generated based on settings defined in the **Settings** tab of the **CSharp Client** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="5c2a8-163">Copie o código C# gerado para um arquivo em um projeto de cliente (por exemplo, um aplicativo [Xamarin.Forms](/xamarin/xamarin-forms/)).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-163">Copy the generated C# code into a file in a client project (for example, a [Xamarin.Forms](/xamarin/xamarin-forms/) app).</span></span>
* <span data-ttu-id="5c2a8-164">Inicie o consumo da API Web:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-164">Start consuming the web API:</span></span>

```csharp
var todoClient = new TodoClient();

// Gets all to-dos from the API
var allTodos = await todoClient.GetAllAsync();

// Create a new TodoItem, and save it in the API
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

> [!NOTE]
> <span data-ttu-id="5c2a8-165">Você pode injetar uma URL base e/ou um cliente HTTP no cliente da API.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-165">You can inject a base URL and/or a HTTP client into the API client.</span></span> <span data-ttu-id="5c2a8-166">A melhor prática é sempre [reutilizar o HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-166">The best practice is to always [reuse the HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).</span></span>

### <a name="other-ways-to-generate-client-code"></a><span data-ttu-id="5c2a8-167">Outras maneiras de gerar o código do cliente</span><span class="sxs-lookup"><span data-stu-id="5c2a8-167">Other ways to generate client code</span></span>

<span data-ttu-id="5c2a8-168">Você pode gerar o código de cliente de outras maneiras mais adequadas ao seu fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-168">You can generate the client code in other ways, more suited to your workflow:</span></span>

* [<span data-ttu-id="5c2a8-169">MSBuild</span><span class="sxs-lookup"><span data-stu-id="5c2a8-169">MSBuild</span></span>](https://www.nuget.org/packages/NSwag.MSBuild/)

* [<span data-ttu-id="5c2a8-170">No código</span><span class="sxs-lookup"><span data-stu-id="5c2a8-170">In code</span></span>](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [<span data-ttu-id="5c2a8-171">Modelos T4</span><span class="sxs-lookup"><span data-stu-id="5c2a8-171">T4 templates</span></span>](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a><span data-ttu-id="5c2a8-172">Personalizar</span><span class="sxs-lookup"><span data-stu-id="5c2a8-172">Customize</span></span>

<span data-ttu-id="5c2a8-173">O Swagger fornece opções para documentar o modelo de objeto para facilitar o consumo da API Web.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-173">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="5c2a8-174">Descrição e informações da API</span><span class="sxs-lookup"><span data-stu-id="5c2a8-174">API info and description</span></span>

<span data-ttu-id="5c2a8-175">No método `Startup.Configure`, uma ação de configuração passada para o método `UseSwagger` adiciona informações como o autor, a licença e a descrição:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-175">In the `Startup.Configure` method, a configuration action passed to the `UseSwagger` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

<span data-ttu-id="5c2a8-176">A interface do usuário do Swagger exibe as informações da versão:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-176">The Swagger UI displays the version's information:</span></span>

![Interface do usuário do Swagger com informações de versão](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="5c2a8-178">comentários XML</span><span class="sxs-lookup"><span data-stu-id="5c2a8-178">XML comments</span></span>

<span data-ttu-id="5c2a8-179">Os comentários XML podem ser habilitados com as seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-179">XML comments are enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio-xml"></a>[<span data-ttu-id="5c2a8-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c2a8-180">Visual Studio</span></span>](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="5c2a8-181">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Editar <nome_do_projeto>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-181">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="5c2a8-182">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="5c2a8-183">Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-183">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="5c2a8-184">Marque a caixa **Arquivo de documentação XML** na seção **Saída** da guia **Build**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-184">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[<span data-ttu-id="5c2a8-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5c2a8-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="5c2a8-186">No *Painel de Soluções*, pressione **control** e clique no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-186">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="5c2a8-187">Navegue até **Ferramentas** > **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-187">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="5c2a8-188">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="5c2a8-189">Abra a caixa de diálogo **Opções do Projeto** > **Compilar**>**Compilador**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-189">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="5c2a8-190">Marque a caixa **Gerar documentação XML** na seção **Opções Gerais**</span><span class="sxs-lookup"><span data-stu-id="5c2a8-190">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[<span data-ttu-id="5c2a8-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c2a8-191">Visual Studio Code</span></span>](#tab/visual-studio-code-xml/)

<span data-ttu-id="5c2a8-192">Manualmente, adicione as linhas destacadas ao arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-192">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="5c2a8-193">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="5c2a8-193">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="5c2a8-194">O NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) e o tipo de retorno recomendado para as ações da API Web é [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-194">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span></span> <span data-ttu-id="5c2a8-195">Consequentemente, o NSwag não consegue inferir o que a sua ação está executando e o que ela retorna.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-195">Consequently, NSwag can't infer what your action is doing and what it returns.</span></span> <span data-ttu-id="5c2a8-196">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="5c2a8-197">A ação anterior retorna `IActionResult`, mas dentro da ação, ela está retornando [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) ou [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-197">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) or [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest).</span></span> <span data-ttu-id="5c2a8-198">As anotações de dados são usadas para informar os clientes de quais códigos de status HTTP essa ação deverá retornar.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-198">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="5c2a8-199">Decore a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-199">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="5c2a8-200">O NSwag usa [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) e o tipo de retorno recomendado para as ações da API Web é [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-200">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span></span> <span data-ttu-id="5c2a8-201">Consequentemente, o NSwag pode inferir apenas o tipo de retorno definido por `T`.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-201">Consequently, NSwag can only infer the return type defined by `T`.</span></span> <span data-ttu-id="5c2a8-202">Outros tipos de retorno possíveis na ação não podem ser inferidos.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-202">Other possible return types in the action cannot be inferred.</span></span> <span data-ttu-id="5c2a8-203">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-203">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="5c2a8-204">A ação anterior retorna `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-204">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="5c2a8-205">Dentro da ação, ela está retornando [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-205">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="5c2a8-206">Como o controlador está decorado com o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute), uma resposta [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) também é possível.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-206">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="5c2a8-207">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-207">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="5c2a8-208">As anotações de dados são usadas para informar os clientes de quais códigos de status HTTP essa ação deverá retornar.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-208">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="5c2a8-209">Decore a ação com os seguintes atributos:</span><span class="sxs-lookup"><span data-stu-id="5c2a8-209">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="5c2a8-210">No ASP.NET Core 2.2 ou posterior, as convenções podem ser usadas como uma alternativa para decorar explicitamente as ações individuais com `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-210">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="5c2a8-211">Para obter mais informações, consulte <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-211">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="5c2a8-212">O gerador do Swagger agora pode descrever essa ação precisamente e os clientes gerados conseguem saber o que recebem ao chamar o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-212">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="5c2a8-213">É altamente recomendável decorar todas as ações com esses atributos.</span><span class="sxs-lookup"><span data-stu-id="5c2a8-213">Decorating all actions with these attributes is highly recommended.</span></span> <span data-ttu-id="5c2a8-214">Para obter diretrizes sobre quais respostas HTTP as ações da API devem retornar, confira a [Especificação RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="5c2a8-214">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
