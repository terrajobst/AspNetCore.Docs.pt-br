---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: b5235f4e31f6edd21fb410824fb215ab2d4a41b6
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727293"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="88bd8-103">Criar interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="88bd8-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="88bd8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88bd8-105">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="88bd8-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="88bd8-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="88bd8-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="88bd8-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="88bd8-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="88bd8-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="88bd8-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88bd8-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="88bd8-110">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="88bd8-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="88bd8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88bd8-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="88bd8-112">No Visual Studio, selecione **criar novo um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="88bd8-113">Selecione **biblioteca de classes Razor** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="88bd8-114">Nomeie a biblioteca (por exemplo, "RazorClassLib"), > **criar**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="88bd8-115">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="88bd8-116">Selecione **páginas de suporte e exibições** se você precisar dar suporte a exibições.</span><span class="sxs-lookup"><span data-stu-id="88bd8-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="88bd8-117">Por padrão, há suporte apenas para Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="88bd8-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="88bd8-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-118">Select **Create**.</span></span>

<span data-ttu-id="88bd8-119">O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="88bd8-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="88bd8-120">A opção **páginas e exibições de suporte** oferece suporte a páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="88bd8-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="88bd8-121">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88bd8-122">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="88bd8-123">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="88bd8-124">O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="88bd8-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="88bd8-125">Passe a opção de `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) para fornecer suporte para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="88bd8-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="88bd8-126">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="88bd8-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="88bd8-127">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="88bd8-128">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="88bd8-129">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="88bd8-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="88bd8-130">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="88bd8-131">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="88bd8-131">Reference RCL content</span></span>

<span data-ttu-id="88bd8-132">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="88bd8-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="88bd8-133">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="88bd8-133">NuGet package.</span></span> <span data-ttu-id="88bd8-134">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="88bd8-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="88bd8-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="88bd8-136">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="88bd8-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="88bd8-137">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="88bd8-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="88bd8-138">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="88bd8-139">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="88bd8-140">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="88bd8-141">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="88bd8-142">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="88bd8-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="88bd8-143">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="88bd8-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="88bd8-144">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="88bd8-144">RCL Pages layout</span></span>

<span data-ttu-id="88bd8-145">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="88bd8-146">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="88bd8-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="88bd8-147">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="88bd8-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="88bd8-148">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="88bd8-149">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="88bd8-150">Criar um RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="88bd8-150">Create an RCL with static assets</span></span>

<span data-ttu-id="88bd8-151">Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo aplicativo de consumo do RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-151">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="88bd8-152">ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="88bd8-153">Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="88bd8-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="88bd8-154">Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote.</span><span class="sxs-lookup"><span data-stu-id="88bd8-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="88bd8-155">Excluir ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="88bd8-155">Exclude static assets</span></span>

<span data-ttu-id="88bd8-156">Para excluir ativos estáticos, adicione o caminho de exclusão desejado ao grupo de propriedades `$(DefaultItemExcludes)` no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="88bd8-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="88bd8-157">Separe as entradas com um ponto e vírgula (`;`).</span><span class="sxs-lookup"><span data-stu-id="88bd8-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="88bd8-158">No exemplo a seguir, a folha de estilo *lib. css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída no RCL publicado:</span><span class="sxs-lookup"><span data-stu-id="88bd8-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="88bd8-159">Integração do typescript</span><span class="sxs-lookup"><span data-stu-id="88bd8-159">Typescript integration</span></span>

<span data-ttu-id="88bd8-160">Para incluir arquivos TypeScript em um RCL:</span><span class="sxs-lookup"><span data-stu-id="88bd8-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="88bd8-161">Coloque os arquivos TypeScript ( *. TS*) fora da pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="88bd8-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="88bd8-162">Por exemplo, coloque os arquivos em uma pasta de *cliente* .</span><span class="sxs-lookup"><span data-stu-id="88bd8-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="88bd8-163">Configure a saída de compilação do TypeScript para a pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="88bd8-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="88bd8-164">Defina a propriedade `TypescriptOutDir` dentro de uma `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="88bd8-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="88bd8-165">Inclua o destino TypeScript como uma dependência do destino de `ResolveCurrentProjectStaticWebAssets` adicionando o seguinte destino dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="88bd8-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="88bd8-166">Consumir conteúdo de um RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="88bd8-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="88bd8-167">Os arquivos incluídos na pasta *wwwroot* do RCL são expostos ao aplicativo de consumo sob o prefixo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-167">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="88bd8-168">Por exemplo, uma biblioteca chamada *Razor. class. lib* resulta em um caminho para conteúdo estático em `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="88bd8-169">Ao produzir um pacote NuGet e o nome do assembly não for igual à ID do pacote, use a ID do pacote para `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="88bd8-170">O aplicativo de consumo faz referência a ativos estáticos fornecidos pela biblioteca com `<script>`, `<style>`, `<img>`e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="88bd8-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="88bd8-171">O aplicativo de consumo deve ter o [suporte de arquivo estático](xref:fundamentals/static-files) habilitado no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="88bd8-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="88bd8-172">Ao executar o aplicativo de consumo da saída da compilação (`dotnet run`), os ativos da Web estáticos são habilitados por padrão no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="88bd8-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="88bd8-173">Para dar suporte a ativos em outros ambientes ao executar a partir da saída da compilação, chame `UseStaticWebAssets` no construtor de hosts em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88bd8-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="88bd8-174">A chamada de `UseStaticWebAssets` não é necessária ao executar um aplicativo da saída publicada (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="88bd8-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="88bd8-175">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="88bd8-175">Multi-project development flow</span></span>

<span data-ttu-id="88bd8-176">Quando o aplicativo de consumo for executado:</span><span class="sxs-lookup"><span data-stu-id="88bd8-176">When the consuming app runs:</span></span>

* <span data-ttu-id="88bd8-177">Os ativos no RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="88bd8-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="88bd8-178">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="88bd8-179">Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="88bd8-180">Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web.</span><span class="sxs-lookup"><span data-stu-id="88bd8-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="88bd8-181">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="88bd8-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="88bd8-182">Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="88bd8-183">Publicar</span><span class="sxs-lookup"><span data-stu-id="88bd8-183">Publish</span></span>

<span data-ttu-id="88bd8-184">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88bd8-185">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="88bd8-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="88bd8-186">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="88bd8-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="88bd8-187">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="88bd8-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="88bd8-188">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="88bd8-189">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88bd8-189">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="88bd8-190">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="88bd8-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="88bd8-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88bd8-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="88bd8-192">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="88bd8-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="88bd8-193">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="88bd8-194">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="88bd8-195">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="88bd8-196">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="88bd8-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="88bd8-197">Selecione **biblioteca de classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="88bd8-198">Um RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="88bd8-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="88bd8-199">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88bd8-200">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="88bd8-201">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="88bd8-202">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="88bd8-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="88bd8-203">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="88bd8-204">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="88bd8-205">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="88bd8-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="88bd8-206">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="88bd8-207">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="88bd8-207">Reference RCL content</span></span>

<span data-ttu-id="88bd8-208">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="88bd8-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="88bd8-209">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="88bd8-209">NuGet package.</span></span> <span data-ttu-id="88bd8-210">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="88bd8-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="88bd8-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="88bd8-212">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="88bd8-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="88bd8-213">Walkthrough: criar um projeto RCL e usar de um projeto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="88bd8-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="88bd8-214">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-214">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="88bd8-215">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="88bd8-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="88bd8-216">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-216">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="88bd8-217">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="88bd8-217">Test the download app</span></span>

<span data-ttu-id="88bd8-218">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="88bd8-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="88bd8-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88bd8-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="88bd8-220">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="88bd8-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="88bd8-221">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-221">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="88bd8-222">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88bd8-223">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="88bd8-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="88bd8-224">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="88bd8-225">Siga as instruções em [Testar WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="88bd8-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="88bd8-226">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="88bd8-226">Create an RCL</span></span>

<span data-ttu-id="88bd8-227">Nesta seção, um RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="88bd8-227">In this section, an RCL is created.</span></span> <span data-ttu-id="88bd8-228">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="88bd8-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88bd8-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="88bd8-230">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="88bd8-230">Create the RCL project:</span></span>

* <span data-ttu-id="88bd8-231">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="88bd8-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="88bd8-232">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="88bd8-233">Nomeie o aplicativo **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="88bd8-234">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="88bd8-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="88bd8-235">Selecione **biblioteca de classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="88bd8-236">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="88bd8-237">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88bd8-238">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="88bd8-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="88bd8-239">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="88bd8-239">The preceding commands:</span></span>

* <span data-ttu-id="88bd8-240">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="88bd8-241">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="88bd8-242">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="88bd8-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="88bd8-243">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="88bd8-244">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="88bd8-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="88bd8-245">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="88bd8-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="88bd8-246">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="88bd8-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="88bd8-247">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="88bd8-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="88bd8-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="88bd8-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="88bd8-249">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="88bd8-250">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="88bd8-251">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="88bd8-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="88bd8-252">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="88bd8-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="88bd8-253">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="88bd8-254">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="88bd8-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="88bd8-255">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="88bd8-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="88bd8-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88bd8-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="88bd8-257">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="88bd8-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="88bd8-258">Em **Gerenciador de soluções**, clique com o botão direito do mouse na solução > **Adicionar** >**novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="88bd8-259">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="88bd8-260">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="88bd8-261">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="88bd8-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="88bd8-262">Selecione o **aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="88bd8-263">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="88bd8-264">Em **Gerenciador de soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **criar dependências** > **dependências do projeto**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="88bd8-265">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="88bd8-266">Em **Gerenciador de soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** **referência**de >.</span><span class="sxs-lookup"><span data-stu-id="88bd8-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="88bd8-267">Na caixa de diálogo **Gerenciador de referências** , verifique **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="88bd8-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="88bd8-268">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="88bd8-268">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="88bd8-269">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="88bd8-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="88bd8-270">Crie um aplicativo Web Razor Pages e um arquivo de solução que contenha o aplicativo Razor Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="88bd8-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="88bd8-271">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="88bd8-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="88bd8-272">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="88bd8-272">Test WebApp1</span></span>

<span data-ttu-id="88bd8-273">Navegue até `/MyFeature/Page1` para verificar se a biblioteca de classes da interface do usuário do Razor está em uso.</span><span class="sxs-lookup"><span data-stu-id="88bd8-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="88bd8-274">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="88bd8-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="88bd8-275">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="88bd8-276">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="88bd8-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="88bd8-277">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="88bd8-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="88bd8-278">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="88bd8-279">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="88bd8-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="88bd8-280">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="88bd8-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="88bd8-281">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="88bd8-281">RCL Pages layout</span></span>

<span data-ttu-id="88bd8-282">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="88bd8-283">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="88bd8-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="88bd8-284">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="88bd8-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="88bd8-285">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88bd8-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="88bd8-286">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="88bd8-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
