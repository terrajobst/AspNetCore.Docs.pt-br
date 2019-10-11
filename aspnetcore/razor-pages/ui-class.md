---
title: Interface do usuário do Razor reutilizável em bibliotecas de classes com o ASP.NET Core
author: Rick-Anderson
description: Explica como criar reutilizáveis Razor da interface do usuário usando as exibições parciais em uma biblioteca de classe no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/08/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: dcd24f7dafd198f88cdf84d1ab67c84f45428a95
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179330"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="4bfae-103">Criar interface do usuário reutilizável usando o projeto de biblioteca de classes Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="4bfae-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bfae-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bfae-105">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="4bfae-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="4bfae-106">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="4bfae-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="4bfae-107">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="4bfae-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="4bfae-108">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="4bfae-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bfae-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="4bfae-110">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="4bfae-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bfae-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bfae-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bfae-112">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-112">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4bfae-113">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4bfae-114">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-114">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="4bfae-115">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="4bfae-116">Verifique se **ASP.NET Core 3,0** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-116">Verify **ASP.NET Core 3.0** or later is selected.</span></span>
* <span data-ttu-id="4bfae-117">Selecione **biblioteca de classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-117">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="4bfae-118">O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="4bfae-118">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="4bfae-119">Uma opção de modelo no Visual Studio fornece suporte de modelo para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="4bfae-119">A template option in Visual Studio provides template support for pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4bfae-120">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4bfae-121">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="4bfae-122">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="4bfae-123">O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão.</span><span class="sxs-lookup"><span data-stu-id="4bfae-123">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="4bfae-124">Passe a opção `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`) para fornecer suporte para páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="4bfae-124">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="4bfae-125">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="4bfae-125">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="4bfae-126">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-126">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="4bfae-127">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-127">Add Razor files to the RCL.</span></span>

<span data-ttu-id="4bfae-128">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="4bfae-128">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="4bfae-129">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-129">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="4bfae-130">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="4bfae-130">Reference RCL content</span></span>

<span data-ttu-id="4bfae-131">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="4bfae-131">The RCL can be referenced by:</span></span>

* <span data-ttu-id="4bfae-132">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="4bfae-132">NuGet package.</span></span> <span data-ttu-id="4bfae-133">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="4bfae-133">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="4bfae-134">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-134">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="4bfae-135">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="4bfae-135">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="4bfae-136">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="4bfae-136">Override views, partial views, and pages</span></span>

<span data-ttu-id="4bfae-137">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-137">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="4bfae-138">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-138">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="4bfae-139">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-139">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="4bfae-140">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-140">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="4bfae-141">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="4bfae-141">Update the markup to indicate the new location.</span></span> <span data-ttu-id="4bfae-142">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="4bfae-142">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="4bfae-143">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="4bfae-143">RCL Pages layout</span></span>

<span data-ttu-id="4bfae-144">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-144">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="4bfae-145">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="4bfae-145">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="4bfae-146">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="4bfae-146">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="4bfae-147">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-147">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="4bfae-148">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-148">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="4bfae-149">Criar um RCL com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="4bfae-149">Create an RCL with static assets</span></span>

<span data-ttu-id="4bfae-150">Um RCL pode exigir ativos estáticos complementares que podem ser referenciados pelo aplicativo de consumo do RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-150">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="4bfae-151">ASP.NET Core permite criar RCLs que incluem ativos estáticos disponíveis para um aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-151">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="4bfae-152">Para incluir ativos complementares como parte de um RCL, crie uma pasta *wwwroot* na biblioteca de classes e inclua todos os arquivos necessários nessa pasta.</span><span class="sxs-lookup"><span data-stu-id="4bfae-152">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="4bfae-153">Ao empacotar um RCL, todos os ativos complementares na pasta *wwwroot* são incluídos automaticamente no pacote.</span><span class="sxs-lookup"><span data-stu-id="4bfae-153">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="4bfae-154">Excluir ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="4bfae-154">Exclude static assets</span></span>

<span data-ttu-id="4bfae-155">Para excluir ativos estáticos, adicione o caminho de exclusão desejado ao grupo de propriedades `$(DefaultItemExcludes)` no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="4bfae-155">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="4bfae-156">Separe as entradas com um ponto e vírgula (`;`).</span><span class="sxs-lookup"><span data-stu-id="4bfae-156">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="4bfae-157">No exemplo a seguir, a folha de estilo *lib. css* na pasta *wwwroot* não é considerada um ativo estático e não está incluída no RCL publicado:</span><span class="sxs-lookup"><span data-stu-id="4bfae-157">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="4bfae-158">Integração do typescript</span><span class="sxs-lookup"><span data-stu-id="4bfae-158">Typescript integration</span></span>

<span data-ttu-id="4bfae-159">Para incluir arquivos TypeScript em um RCL:</span><span class="sxs-lookup"><span data-stu-id="4bfae-159">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="4bfae-160">Coloque os arquivos TypeScript ( *. TS*) fora da pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="4bfae-160">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="4bfae-161">Por exemplo, coloque os arquivos em uma pasta de *cliente* .</span><span class="sxs-lookup"><span data-stu-id="4bfae-161">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="4bfae-162">Configure a saída de compilação do TypeScript para a pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="4bfae-162">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="4bfae-163">Defina a propriedade `TypescriptOutDir` dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="4bfae-163">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="4bfae-164">Inclua o destino TypeScript como uma dependência do destino `ResolveCurrentProjectStaticWebAssets` adicionando o seguinte destino dentro de um `PropertyGroup` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="4bfae-164">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
  <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
    CompileTypeScript;
    $(ResolveCurrentProjectStaticWebAssetsInputs)
  </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="4bfae-165">Consumir conteúdo de um RCL referenciado</span><span class="sxs-lookup"><span data-stu-id="4bfae-165">Consume content from a referenced RCL</span></span>

<span data-ttu-id="4bfae-166">Os arquivos incluídos na pasta *wwwroot* do RCL são expostos ao aplicativo de consumo no prefixo `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-166">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="4bfae-167">Por exemplo, uma biblioteca chamada *Razor. class. lib* resulta em um caminho para conteúdo estático em `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-167">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="4bfae-168">O aplicativo de consumo faz referência a ativos estáticos fornecidos pela biblioteca com `<script>`, `<style>`, `<img>` e outras marcas HTML.</span><span class="sxs-lookup"><span data-stu-id="4bfae-168">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="4bfae-169">O aplicativo de consumo deve ter o [suporte a arquivo estático](xref:fundamentals/static-files) habilitado no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="4bfae-169">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="4bfae-170">Ao executar o aplicativo de consumo da saída da compilação (`dotnet run`), os ativos da Web estáticos são habilitados por padrão no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="4bfae-170">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="4bfae-171">Para dar suporte a ativos em outros ambientes ao executar a partir da saída da compilação, chame `UseStaticWebAssets` no construtor de hosts em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4bfae-171">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="4bfae-172">Não é necessário chamar `UseStaticWebAssets` ao executar um aplicativo da saída publicada (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="4bfae-172">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="4bfae-173">Fluxo de desenvolvimento de vários projetos</span><span class="sxs-lookup"><span data-stu-id="4bfae-173">Multi-project development flow</span></span>

<span data-ttu-id="4bfae-174">Quando o aplicativo de consumo for executado:</span><span class="sxs-lookup"><span data-stu-id="4bfae-174">When the consuming app runs:</span></span>

* <span data-ttu-id="4bfae-175">Os ativos no RCL permanecem em suas pastas originais.</span><span class="sxs-lookup"><span data-stu-id="4bfae-175">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="4bfae-176">Os ativos não são movidos para o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-176">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="4bfae-177">Qualquer alteração na pasta *wwwroot* do RCL é refletida no aplicativo de consumo depois que o RCL é recriado e sem recriar o aplicativo de consumo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-177">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="4bfae-178">Quando o RCL é criado, é produzido um manifesto que descreve os locais estáticos de ativos da Web.</span><span class="sxs-lookup"><span data-stu-id="4bfae-178">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="4bfae-179">O aplicativo de consumo lê o manifesto em tempo de execução para consumir os ativos de projetos e pacotes referenciados.</span><span class="sxs-lookup"><span data-stu-id="4bfae-179">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="4bfae-180">Quando um novo ativo é adicionado a um RCL, o RCL deve ser recriado para atualizar seu manifesto antes que um aplicativo de consumo possa acessar o novo ativo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-180">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="4bfae-181">Publicar</span><span class="sxs-lookup"><span data-stu-id="4bfae-181">Publish</span></span>

<span data-ttu-id="4bfae-182">Quando o aplicativo é publicado, os ativos complementares de todos os projetos e pacotes referenciados são copiados para a pasta *wwwroot* do aplicativo publicado em `_content/{LIBRARY NAME}/`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-182">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bfae-183">Exibições do Razor, páginas, controladores, modelos de página, [componentes do Razor](xref:blazor/class-libraries), [componentes de exibição](xref:mvc/views/view-components)e modelos de dados podem ser criados em uma biblioteca de classes Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="4bfae-183">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="4bfae-184">A RCL pode ser e empacotada e reutilizada.</span><span class="sxs-lookup"><span data-stu-id="4bfae-184">The RCL can be packaged and reused.</span></span> <span data-ttu-id="4bfae-185">Os aplicativos podem incluir a RCL e substituir as exibições e as páginas que ela contém.</span><span class="sxs-lookup"><span data-stu-id="4bfae-185">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="4bfae-186">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-186">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="4bfae-187">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4bfae-187">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="4bfae-188">Criar uma biblioteca de classes contendo a interface do usuário do Razor</span><span class="sxs-lookup"><span data-stu-id="4bfae-188">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bfae-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bfae-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4bfae-190">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4bfae-191">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-191">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4bfae-192">Dê um nome à biblioteca (por exemplo, "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-192">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="4bfae-193">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-193">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="4bfae-194">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-194">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="4bfae-195">Selecione **biblioteca de classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-195">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="4bfae-196">Um RCL tem o seguinte arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="4bfae-196">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4bfae-197">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-197">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4bfae-198">Da linha de comando, execute `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-198">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="4bfae-199">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-199">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="4bfae-200">Para obter mais informações, confira [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="4bfae-200">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="4bfae-201">Para evitar uma colisão de nome de arquivo com a biblioteca de exibição gerada, verifique se o nome da biblioteca não termina em `.Views`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-201">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="4bfae-202">Adicione arquivos Razor na RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-202">Add Razor files to the RCL.</span></span>

<span data-ttu-id="4bfae-203">Os modelos do ASP.NET Core, suponha que o conteúdo da RCL está no *áreas* pasta.</span><span class="sxs-lookup"><span data-stu-id="4bfae-203">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="4bfae-204">Consulte [layout de páginas RCL](#rcl-pages-layout) para criar um RCL que expõe o conteúdo em `~/Pages` em vez de `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-204">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="4bfae-205">Conteúdo de RCL de referência</span><span class="sxs-lookup"><span data-stu-id="4bfae-205">Reference RCL content</span></span>

<span data-ttu-id="4bfae-206">A RCL pode ser referenciada por:</span><span class="sxs-lookup"><span data-stu-id="4bfae-206">The RCL can be referenced by:</span></span>

* <span data-ttu-id="4bfae-207">Pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="4bfae-207">NuGet package.</span></span> <span data-ttu-id="4bfae-208">Confira [Criando pacotes do NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) e [Criar e publicar um pacote do NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="4bfae-208">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="4bfae-209">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-209">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="4bfae-210">Confira [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="4bfae-210">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="4bfae-211">Passo a passo: Criar um projeto RCL e usar de um projeto Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4bfae-211">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="4bfae-212">Você pode baixar o [projeto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) e testá-lo em vez de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-212">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="4bfae-213">O download de exemplo contém um código adicional e links que facilitam o teste do projeto.</span><span class="sxs-lookup"><span data-stu-id="4bfae-213">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="4bfae-214">Você pode deixar comentários [nesse problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) com suas opiniões sobre os exemplos de download em relação às instruções passo a passo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-214">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="4bfae-215">Testar o aplicativo de download</span><span class="sxs-lookup"><span data-stu-id="4bfae-215">Test the download app</span></span>

<span data-ttu-id="4bfae-216">Se você não tiver baixado o aplicativo concluído e preferir criar o projeto do passo a passo, vá para a [próxima seção](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="4bfae-216">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bfae-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bfae-217">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bfae-218">Abra o arquivo *.sln* no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4bfae-218">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="4bfae-219">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-219">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4bfae-220">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-220">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4bfae-221">Em um prompt de comando no diretório *cli*, crie a RCL e o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="4bfae-221">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="4bfae-222">Acesse o diretório *WebApp1* e execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-222">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="4bfae-223">Siga as instruções em [Testar WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="4bfae-223">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="4bfae-224">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="4bfae-224">Create an RCL</span></span>

<span data-ttu-id="4bfae-225">Nesta seção, um RCL é criado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-225">In this section, an RCL is created.</span></span> <span data-ttu-id="4bfae-226">Arquivos Razor são adicionados à RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-226">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bfae-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bfae-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bfae-228">Crie o projeto da RCL:</span><span class="sxs-lookup"><span data-stu-id="4bfae-228">Create the RCL project:</span></span>

* <span data-ttu-id="4bfae-229">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-229">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4bfae-230">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-230">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4bfae-231">Nomeie o aplicativo **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-231">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="4bfae-232">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-232">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="4bfae-233">Selecione **biblioteca de classes Razor** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-233">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="4bfae-234">Adicione um arquivo Razor de exibição parcial chamado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-234">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4bfae-235">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-235">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4bfae-236">Na linha de comando, execute o seguinte:</span><span class="sxs-lookup"><span data-stu-id="4bfae-236">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="4bfae-237">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="4bfae-237">The preceding commands:</span></span>

* <span data-ttu-id="4bfae-238">Cria o `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-238">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="4bfae-239">Criam uma página Razor _Message e a adicionam à RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-239">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="4bfae-240">O parâmetro `-np` cria a página sem um `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="4bfae-240">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="4bfae-241">Cria uma [viewstart](xref:mvc/views/layout#running-code-before-each-view) de arquivo e o adiciona à RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-241">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="4bfae-242">O *viewstart* arquivo é necessário para usar o layout do projeto páginas Razor (que é adicionado na próxima seção).</span><span class="sxs-lookup"><span data-stu-id="4bfae-242">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="4bfae-243">Adicionar pastas e arquivos Razor ao projeto</span><span class="sxs-lookup"><span data-stu-id="4bfae-243">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="4bfae-244">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bfae-244">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="4bfae-245">Substitua a marcação em *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="4bfae-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="4bfae-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` é necessário para usar a exibição parcial (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="4bfae-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="4bfae-247">Em vez de incluir a diretiva `@addTagHelper`, você pode adicionar um arquivo *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-247">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4bfae-248">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-248">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="4bfae-249">Para obter mais informações sobre *viewimports. cshtml*, consulte [importando diretivas compartilhadas](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="4bfae-249">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="4bfae-250">Crie a biblioteca de classes para verificar se não há nenhum erro de compilador:</span><span class="sxs-lookup"><span data-stu-id="4bfae-250">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="4bfae-251">A saída do build contém *RazorUIClassLib.dll* e *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-251">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="4bfae-252">*RazorUIClassLib.Views.dll* contém o conteúdo Razor compilado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-252">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="4bfae-253">Use a biblioteca da interface do usuário do Razor de um projeto Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="4bfae-253">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4bfae-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4bfae-254">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4bfae-255">Crie aplicativo Web Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="4bfae-255">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="4bfae-256">Em **Gerenciador de soluções**, clique com o botão direito do mouse na solução > **adicionar** **novo projeto**>.</span><span class="sxs-lookup"><span data-stu-id="4bfae-256">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="4bfae-257">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-257">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="4bfae-258">Nomeie o aplicativo como **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-258">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="4bfae-259">Verifique se o **ASP.NET Core 2.1** ou posterior está selecionado.</span><span class="sxs-lookup"><span data-stu-id="4bfae-259">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="4bfae-260">Selecione o **aplicativo Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-260">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="4bfae-261">No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Definir como projeto de inicialização**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-261">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="4bfae-262">Em **Gerenciador de soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **criar dependências** > **dependências do projeto**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-262">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="4bfae-263">Marque **RazorUIClassLib** como uma dependência de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-263">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="4bfae-264">Em **Gerenciador de soluções**, clique com o botão direito do mouse em **WebApp1** e selecione **Adicionar** **referência**de >.</span><span class="sxs-lookup"><span data-stu-id="4bfae-264">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="4bfae-265">Na caixa de diálogo **Gerenciador de referências** , verifique **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="4bfae-265">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="4bfae-266">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4bfae-266">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4bfae-267">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4bfae-267">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4bfae-268">Crie um aplicativo Web Razor Pages e um arquivo de solução que contenha o aplicativo Razor Pages e o RCL:</span><span class="sxs-lookup"><span data-stu-id="4bfae-268">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="4bfae-269">Crie e execute o aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="4bfae-269">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="4bfae-270">Testar o WebApp1</span><span class="sxs-lookup"><span data-stu-id="4bfae-270">Test WebApp1</span></span>

<span data-ttu-id="4bfae-271">Navegue até `/MyFeature/Page1` para verificar se a biblioteca de classes da interface do usuário do Razor está em uso.</span><span class="sxs-lookup"><span data-stu-id="4bfae-271">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="4bfae-272">Substituir exibições, exibições parciais e páginas</span><span class="sxs-lookup"><span data-stu-id="4bfae-272">Override views, partial views, and pages</span></span>

<span data-ttu-id="4bfae-273">Quando uma exibição, uma exibição parcial ou uma página Razor for encontrada no aplicativo Web e na RCL, a marcação Razor (arquivo *.cshtml*) no aplicativo Web terá precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-273">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="4bfae-274">Por exemplo, adicione *WebApp1/areas/MyFeature/Pages/página1. cshtml* a WebApp1, e página1 no WebApp1 terá precedência sobre PÁGINA1 no RCL.</span><span class="sxs-lookup"><span data-stu-id="4bfae-274">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="4bfae-275">No download de exemplo, renomeie *WebApp1/Areas/MyFeature2* como *WebApp1/Areas/MyFeature* para testar a precedência.</span><span class="sxs-lookup"><span data-stu-id="4bfae-275">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="4bfae-276">Copie a exibição parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* para *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-276">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="4bfae-277">Atualize a marcação para indicar o novo local.</span><span class="sxs-lookup"><span data-stu-id="4bfae-277">Update the markup to indicate the new location.</span></span> <span data-ttu-id="4bfae-278">Crie e execute o aplicativo para verificar se a versão da parcial do aplicativo está sendo usada.</span><span class="sxs-lookup"><span data-stu-id="4bfae-278">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="4bfae-279">Layout de páginas RCL</span><span class="sxs-lookup"><span data-stu-id="4bfae-279">RCL Pages layout</span></span>

<span data-ttu-id="4bfae-280">A RCL como se fosse parte do aplicativo web de conteúdo de referência *páginas* pasta, crie o projeto da RCL com a seguinte estrutura de arquivo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-280">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="4bfae-281">*RazorUIClassLib/páginas*</span><span class="sxs-lookup"><span data-stu-id="4bfae-281">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="4bfae-282">*RazorUIClassLib/páginas/Shared*</span><span class="sxs-lookup"><span data-stu-id="4bfae-282">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="4bfae-283">Suponha *RazorUIClassLib/páginas/Shared* contém dois arquivos parciais: *_Header.cshtml* e *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="4bfae-283">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="4bfae-284">O `<partial>` marcas pode ser adicionadas ao *layout. cshtml* arquivo:</span><span class="sxs-lookup"><span data-stu-id="4bfae-284">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
