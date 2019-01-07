---
title: Arquivos estáticos no ASP.NET Core
author: rick-anderson
description: Saiba como fornecer e proteger arquivos estáticos e configurar comportamentos do middleware de hospedagem de arquivos estáticos em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/static-files
ms.openlocfilehash: 4c08d65cc1f658ef08a9b4b362ac7f8a3a243557
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637768"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="75fa6-103">Arquivos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75fa6-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="75fa6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="75fa6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="75fa6-105">Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core fornece diretamente para os clientes.</span><span class="sxs-lookup"><span data-stu-id="75fa6-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="75fa6-106">Algumas etapas de configuração são necessárias para habilitar o fornecimento desses arquivos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="75fa6-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75fa6-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="75fa6-108">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="75fa6-108">Serve static files</span></span>

<span data-ttu-id="75fa6-109">Os arquivos estáticos são armazenados no diretório raiz Web do projeto.</span><span class="sxs-lookup"><span data-stu-id="75fa6-109">Static files are stored within your project's web root directory.</span></span> <span data-ttu-id="75fa6-110">O diretório padrão é *\<content_root>/wwwroot*, mas pode ser alterado por meio do método [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_).</span><span class="sxs-lookup"><span data-stu-id="75fa6-110">The default directory is *\<content_root>/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="75fa6-111">Consulte [Raiz de conteúdo](xref:fundamentals/index#content-root) e [Diretório base](xref:fundamentals/index#web-root-webroot) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="75fa6-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root-webroot) for more information.</span></span>

<span data-ttu-id="75fa6-112">O host Web do aplicativo deve ser informado do diretório raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="75fa6-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="75fa6-113">O método `WebHost.CreateDefaultBuilder` define a raiz do conteúdo como o diretório atual:</span><span class="sxs-lookup"><span data-stu-id="75fa6-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="75fa6-114">Defina a raiz do conteúdo com o diretório atual invocando [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) dentro de `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="75fa6-115">Os arquivos estáticos são acessíveis por meio de um caminho relativo ao diretório base.</span><span class="sxs-lookup"><span data-stu-id="75fa6-115">Static files are accessible via a path relative to the web root.</span></span> <span data-ttu-id="75fa6-116">Por exemplo, o modelo de projeto do **Aplicativo Web** contém várias pastas dentro da pasta *wwwroot*:</span><span class="sxs-lookup"><span data-stu-id="75fa6-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="75fa6-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="75fa6-117">**wwwroot**</span></span>
  * <span data-ttu-id="75fa6-118">**css**</span><span class="sxs-lookup"><span data-stu-id="75fa6-118">**css**</span></span>
  * <span data-ttu-id="75fa6-119">**images**</span><span class="sxs-lookup"><span data-stu-id="75fa6-119">**images**</span></span>
  * <span data-ttu-id="75fa6-120">**js**</span><span class="sxs-lookup"><span data-stu-id="75fa6-120">**js**</span></span>

<span data-ttu-id="75fa6-121">O formato de URI para acessar um arquivo na subpasta *images* é *http://\<server_address>/images/\<image_file_name>*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="75fa6-122">Por exemplo, *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="75fa6-123">Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="75fa6-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="75fa6-124">Se você estiver direcionando para o .NET Core, o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluirá esse pacote.</span><span class="sxs-lookup"><span data-stu-id="75fa6-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="75fa6-125">Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="75fa6-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="75fa6-126">Se estiver direcionando o .NET Core, o [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) incluirá esse pacote.</span><span class="sxs-lookup"><span data-stu-id="75fa6-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="75fa6-127">Adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="75fa6-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span>

::: moniker-end

<span data-ttu-id="75fa6-128">Configure o [middleware](xref:fundamentals/middleware/index) que permite o fornecimento de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="75fa6-129">Fornecer arquivos dentro do diretório base</span><span class="sxs-lookup"><span data-stu-id="75fa6-129">Serve files inside of web root</span></span>

<span data-ttu-id="75fa6-130">Invoque o método [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dentro de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="75fa6-131">A sobrecarga do método `UseStaticFiles` sem parâmetro marca os arquivos no diretório base como fornecíveis.</span><span class="sxs-lookup"><span data-stu-id="75fa6-131">The parameterless `UseStaticFiles` method overload marks the files in web root as servable.</span></span> <span data-ttu-id="75fa6-132">A seguinte marcação referencia *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="75fa6-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="75fa6-133">No código anterior, o caractere til `~/` aponta para o diretório base.</span><span class="sxs-lookup"><span data-stu-id="75fa6-133">In the preceding code, the tilde character `~/` points to webroot.</span></span> <span data-ttu-id="75fa6-134">Para obter mais informações, confira [Diretório base](xref:fundamentals/index#web-root-webroot).</span><span class="sxs-lookup"><span data-stu-id="75fa6-134">For more information, see [Web root](xref:fundamentals/index#web-root-webroot).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="75fa6-135">Fornecer arquivos fora do diretório base</span><span class="sxs-lookup"><span data-stu-id="75fa6-135">Serve files outside of web root</span></span>

<span data-ttu-id="75fa6-136">Considere uma hierarquia de diretórios na qual os arquivos estáticos a serem atendidos residam fora do diretório base:</span><span class="sxs-lookup"><span data-stu-id="75fa6-136">Consider a directory hierarchy in which the static files to be served reside outside of the web root:</span></span>

* <span data-ttu-id="75fa6-137">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="75fa6-137">**wwwroot**</span></span>
  * <span data-ttu-id="75fa6-138">**css**</span><span class="sxs-lookup"><span data-stu-id="75fa6-138">**css**</span></span>
  * <span data-ttu-id="75fa6-139">**images**</span><span class="sxs-lookup"><span data-stu-id="75fa6-139">**images**</span></span>
  * <span data-ttu-id="75fa6-140">**js**</span><span class="sxs-lookup"><span data-stu-id="75fa6-140">**js**</span></span>
* <span data-ttu-id="75fa6-141">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="75fa6-141">**MyStaticFiles**</span></span>
  * <span data-ttu-id="75fa6-142">**images**</span><span class="sxs-lookup"><span data-stu-id="75fa6-142">**images**</span></span>
      * <span data-ttu-id="75fa6-143">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="75fa6-143">*banner1.svg*</span></span>

<span data-ttu-id="75fa6-144">Uma solicitação pode acessar o arquivo *banner1.svg* configurando o middleware de arquivos estáticos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="75fa6-144">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="75fa6-145">No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-145">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="75fa6-146">Uma solicitação para *http://\<server_address>/StaticFiles/images/banner1.svg* atende ao arquivo *banner1.svg*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-146">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="75fa6-147">A seguinte marcação referencia *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="75fa6-147">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="75fa6-148">Definir cabeçalhos de resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="75fa6-148">Set HTTP response headers</span></span>

<span data-ttu-id="75fa6-149">Um objeto [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) pode ser usado para definir cabeçalhos de resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="75fa6-149">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="75fa6-150">Além de configurar o fornecimento de arquivos estáticos do diretório base, o seguinte código define o cabeçalho `Cache-Control`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-150">In addition to configuring static file serving from the web root, the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="75fa6-151">O método [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) existe no pacote [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="75fa6-151">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="75fa6-152">Os arquivos se tornaram armazenáveis em cache publicamente por 10 minutos (600 segundos) no ambiente de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="75fa6-152">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="75fa6-154">Autorização de arquivo estático</span><span class="sxs-lookup"><span data-stu-id="75fa6-154">Static file authorization</span></span>

<span data-ttu-id="75fa6-155">O middleware de arquivos estáticos não fornece verificações de autorização.</span><span class="sxs-lookup"><span data-stu-id="75fa6-155">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="75fa6-156">Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão acessíveis publicamente.</span><span class="sxs-lookup"><span data-stu-id="75fa6-156">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="75fa6-157">Para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="75fa6-157">To serve files based on authorization:</span></span>

* <span data-ttu-id="75fa6-158">Armazene-os fora do *wwwroot* e de qualquer diretório acessível ao middleware de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-158">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="75fa6-159">Forneça-os por meio de um método de ação ao qual a autorização é aplicada.</span><span class="sxs-lookup"><span data-stu-id="75fa6-159">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="75fa6-160">Retorne um objeto [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):</span><span class="sxs-lookup"><span data-stu-id="75fa6-160">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="75fa6-161">Habilitar navegação no diretório</span><span class="sxs-lookup"><span data-stu-id="75fa6-161">Enable directory browsing</span></span>

<span data-ttu-id="75fa6-162">A navegação no diretório permite que os usuários do aplicativo Web vejam uma listagem de diretório e arquivos em um diretório especificado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-162">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="75fa6-163">A navegação no diretório está desabilitada por padrão por motivos de segurança (consulte [Considerações](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="75fa6-163">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="75fa6-164">Habilite a navegação no diretório invocando o método [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-164">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="75fa6-165">Adicione os serviços necessários invocando o método [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-165">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="75fa6-166">O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL *http://\<server_address>/MyImages*, com links para cada arquivo e pasta:</span><span class="sxs-lookup"><span data-stu-id="75fa6-166">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![navegação no diretório](static-files/_static/dir-browse.png)

<span data-ttu-id="75fa6-168">Consulte [Considerações](#considerations) para saber mais sobre os riscos de segurança ao habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="75fa6-168">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="75fa6-169">Observe as duas chamadas `UseStaticFiles` no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="75fa6-169">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="75fa6-170">A primeira chamada permite o fornecimento de arquivos estáticos na pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-170">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="75fa6-171">A segunda chamada habilita a navegação no diretório da pasta *wwwroot/images* usando a URL *http://\<server_address>/MyImages*:</span><span class="sxs-lookup"><span data-stu-id="75fa6-171">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="75fa6-172">Fornecer um documento padrão</span><span class="sxs-lookup"><span data-stu-id="75fa6-172">Serve a default document</span></span>

<span data-ttu-id="75fa6-173">Definir uma home page padrão fornece ao visitantes um ponto de partida lógico de ao visitar o site.</span><span class="sxs-lookup"><span data-stu-id="75fa6-173">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="75fa6-174">Para fornecer uma página padrão sem qualificar totalmente o URI do usuário, chame o [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) método de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-174">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="75fa6-175">`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="75fa6-175">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="75fa6-176">`UseDefaultFiles` é um rewriter de URL que, na verdade, não fornece o arquivo.</span><span class="sxs-lookup"><span data-stu-id="75fa6-176">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="75fa6-177">Habilite o middleware de arquivos estáticos por meio de `UseStaticFiles` para fornecer o arquivo.</span><span class="sxs-lookup"><span data-stu-id="75fa6-177">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="75fa6-178">Com `UseDefaultFiles`, as solicitações para uma pasta pesquisam:</span><span class="sxs-lookup"><span data-stu-id="75fa6-178">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="75fa6-179">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="75fa6-179">*default.htm*</span></span>
* <span data-ttu-id="75fa6-180">*default.html*</span><span class="sxs-lookup"><span data-stu-id="75fa6-180">*default.html*</span></span>
* <span data-ttu-id="75fa6-181">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="75fa6-181">*index.htm*</span></span>
* <span data-ttu-id="75fa6-182">*index.html*</span><span class="sxs-lookup"><span data-stu-id="75fa6-182">*index.html*</span></span>

<span data-ttu-id="75fa6-183">O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-183">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="75fa6-184">A URL do navegador continua refletindo o URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-184">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="75fa6-185">O seguinte código altera o nome de arquivo padrão para *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="75fa6-185">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="75fa6-186">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="75fa6-186">UseFileServer</span></span>

<span data-ttu-id="75fa6-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combina a funcionalidade de `UseStaticFiles`, `UseDefaultFiles` e `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="75fa6-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="75fa6-188">O código a seguir permite o fornecimento de arquivos estáticos e do arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="75fa6-188">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="75fa6-189">A navegação no diretório não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="75fa6-189">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="75fa6-190">O seguinte código baseia-se na sobrecarga sem parâmetros, habilitando a navegação no diretório:</span><span class="sxs-lookup"><span data-stu-id="75fa6-190">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="75fa6-191">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="75fa6-191">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="75fa6-192">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="75fa6-192">**wwwroot**</span></span>
  * <span data-ttu-id="75fa6-193">**css**</span><span class="sxs-lookup"><span data-stu-id="75fa6-193">**css**</span></span>
  * <span data-ttu-id="75fa6-194">**images**</span><span class="sxs-lookup"><span data-stu-id="75fa6-194">**images**</span></span>
  * <span data-ttu-id="75fa6-195">**js**</span><span class="sxs-lookup"><span data-stu-id="75fa6-195">**js**</span></span>
* <span data-ttu-id="75fa6-196">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="75fa6-196">**MyStaticFiles**</span></span>
  * <span data-ttu-id="75fa6-197">**images**</span><span class="sxs-lookup"><span data-stu-id="75fa6-197">**images**</span></span>
      * <span data-ttu-id="75fa6-198">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="75fa6-198">*banner1.svg*</span></span>
  * <span data-ttu-id="75fa6-199">*default.html*</span><span class="sxs-lookup"><span data-stu-id="75fa6-199">*default.html*</span></span>

<span data-ttu-id="75fa6-200">O seguinte código habilita arquivos estáticos, arquivos padrão e a navegação no diretório de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-200">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="75fa6-201">`AddDirectoryBrowser` precisa ser chamado quando o valor da propriedade `EnableDirectoryBrowsing` é `true`:</span><span class="sxs-lookup"><span data-stu-id="75fa6-201">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="75fa6-202">Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="75fa6-202">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="75fa6-203">URI</span><span class="sxs-lookup"><span data-stu-id="75fa6-203">URI</span></span>            |                             <span data-ttu-id="75fa6-204">Resposta</span><span class="sxs-lookup"><span data-stu-id="75fa6-204">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="75fa6-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="75fa6-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="75fa6-206">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="75fa6-206">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="75fa6-207">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="75fa6-207">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="75fa6-208">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="75fa6-208">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="75fa6-209">Se nenhum arquivo nomeado como padrão existir no diretório *MyStaticFiles*, *http://\<server_address>/StaticFiles* retornará a listagem de diretórios com links clicáveis:</span><span class="sxs-lookup"><span data-stu-id="75fa6-209">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de arquivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="75fa6-211">`UseDefaultFiles` e `UseDirectoryBrowser` usam a URL *http://\<server_address>/StaticFiles* sem a barra "\" à direita para disparar um redirecionamento do lado do cliente para *http://\<server_address>/StaticFiles/*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-211">`UseDefaultFiles` and `UseDirectoryBrowser` use the URL *http://\<server_address>/StaticFiles* without the trailing slash to trigger a client-side redirect to *http://\<server_address>/StaticFiles/*.</span></span> <span data-ttu-id="75fa6-212">Observe a adição da barra "\" à direita.</span><span class="sxs-lookup"><span data-stu-id="75fa6-212">Notice the addition of the trailing slash.</span></span> <span data-ttu-id="75fa6-213">URLs relativas dentro dos documentos são consideradas inválidas sem uma barra "\" à direita.</span><span class="sxs-lookup"><span data-stu-id="75fa6-213">Relative URLs within the documents are deemed invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="75fa6-214">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="75fa6-214">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="75fa6-215">A classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contém uma propriedade `Mappings` que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME.</span><span class="sxs-lookup"><span data-stu-id="75fa6-215">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="75fa6-216">Na amostra a seguir, várias extensões de arquivo são registradas para tipos MIME conhecidos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-216">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="75fa6-217">A extensão *.rtf* é substituída, e *.mp4* é removida.</span><span class="sxs-lookup"><span data-stu-id="75fa6-217">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="75fa6-218">Consulte [Tipos de conteúdo MIME](http://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="75fa6-218">See [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="75fa6-219">Tipos de conteúdo não padrão</span><span class="sxs-lookup"><span data-stu-id="75fa6-219">Non-standard content types</span></span>

<span data-ttu-id="75fa6-220">O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-220">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="75fa6-221">Se o usuário solicitar um arquivo com um tipo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="75fa6-221">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="75fa6-222">Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada.</span><span class="sxs-lookup"><span data-stu-id="75fa6-222">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="75fa6-223">Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.</span><span class="sxs-lookup"><span data-stu-id="75fa6-223">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="75fa6-224">O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:</span><span class="sxs-lookup"><span data-stu-id="75fa6-224">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="75fa6-225">Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.</span><span class="sxs-lookup"><span data-stu-id="75fa6-225">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="75fa6-226">A habilitação de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="75fa6-226">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="75fa6-227">Ela está desabilitada por padrão, e seu uso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-227">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="75fa6-228">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.</span><span class="sxs-lookup"><span data-stu-id="75fa6-228">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="75fa6-229">Considerações</span><span class="sxs-lookup"><span data-stu-id="75fa6-229">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="75fa6-230">`UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-230">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="75fa6-231">A desabilitação da navegação no diretório em produção é altamente recomendada.</span><span class="sxs-lookup"><span data-stu-id="75fa6-231">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="75fa6-232">Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="75fa6-232">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="75fa6-233">Todo o diretório e seus subdiretórios se tornam publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="75fa6-233">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="75fa6-234">Armazene arquivos adequados para fornecimento ao público em um diretório dedicado, como *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="75fa6-234">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="75fa6-235">Separe esses arquivos das exibições MVC, Páginas do Razor (somente 2.x), arquivos de configuração, etc.</span><span class="sxs-lookup"><span data-stu-id="75fa6-235">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="75fa6-236">As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="75fa6-236">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="75fa6-237">Por exemplo, o Windows diferencia maiúsculas de minúsculas, o macOS e o Linux não.</span><span class="sxs-lookup"><span data-stu-id="75fa6-237">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="75fa6-238">Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="75fa6-238">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="75fa6-239">O manipulador de arquivos estáticos do IIS não é usado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-239">The IIS static file handler isn't used.</span></span> <span data-ttu-id="75fa6-240">Ele não tem nenhuma possibilidade de manipular as solicitações antes que elas sejam manipuladas pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="75fa6-240">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="75fa6-241">Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:</span><span class="sxs-lookup"><span data-stu-id="75fa6-241">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="75fa6-242">Navegue para o recurso **Módulos**.</span><span class="sxs-lookup"><span data-stu-id="75fa6-242">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="75fa6-243">Selecione **StaticFileModule** na lista.</span><span class="sxs-lookup"><span data-stu-id="75fa6-243">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="75fa6-244">Clique em **Remover** na barra lateral **Ações**.</span><span class="sxs-lookup"><span data-stu-id="75fa6-244">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="75fa6-245">Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos.</span><span class="sxs-lookup"><span data-stu-id="75fa6-245">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="75fa6-246">Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.</span><span class="sxs-lookup"><span data-stu-id="75fa6-246">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="75fa6-247">Coloque arquivos de código (incluindo *.cs* e *.cshtml*) fora do diretório base do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75fa6-247">Place code files (including *.cs* and *.cshtml*) outside of the app project's web root.</span></span> <span data-ttu-id="75fa6-248">Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor.</span><span class="sxs-lookup"><span data-stu-id="75fa6-248">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="75fa6-249">Isso impede a perda de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="75fa6-249">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75fa6-250">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="75fa6-250">Additional resources</span></span>

* [<span data-ttu-id="75fa6-251">Middleware</span><span class="sxs-lookup"><span data-stu-id="75fa6-251">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="75fa6-252">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75fa6-252">Introduction to ASP.NET Core</span></span>](xref:index)
