---
title: Provedores de arquivos no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 2ce40ea0d576d08a6b42c3eb6693754f2a0bddce
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809215"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="66d77-103">Provedores de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66d77-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="66d77-104">Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66d77-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="66d77-105">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="66d77-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="66d77-106">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="66d77-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="66d77-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) expõe o conteúdo raiz do aplicativo e a raiz da Web como tipos `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="66d77-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="66d77-108">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="66d77-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="66d77-109">[Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="66d77-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="66d77-110">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="66d77-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="66d77-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66d77-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="66d77-112">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="66d77-112">File Provider interfaces</span></span>

<span data-ttu-id="66d77-113">A interface principal é [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="66d77-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="66d77-114">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="66d77-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="66d77-115">Obter informações sobre o arquivo ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span><span class="sxs-lookup"><span data-stu-id="66d77-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="66d77-116">Obter informações sobre o diretório ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span><span class="sxs-lookup"><span data-stu-id="66d77-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="66d77-117">Configurar notificações de alteração (usando um [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span><span class="sxs-lookup"><span data-stu-id="66d77-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="66d77-118">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="66d77-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="66d77-119">Exists</span><span class="sxs-lookup"><span data-stu-id="66d77-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="66d77-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="66d77-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="66d77-121">Nome</span><span class="sxs-lookup"><span data-stu-id="66d77-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="66d77-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (em bytes)</span><span class="sxs-lookup"><span data-stu-id="66d77-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="66d77-123">Data de [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)</span><span class="sxs-lookup"><span data-stu-id="66d77-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="66d77-124">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream).</span><span class="sxs-lookup"><span data-stu-id="66d77-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="66d77-125">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="66d77-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="66d77-126">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="66d77-126">File Provider implementations</span></span>

<span data-ttu-id="66d77-127">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="66d77-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="66d77-128">Implementação</span><span class="sxs-lookup"><span data-stu-id="66d77-128">Implementation</span></span> | <span data-ttu-id="66d77-129">Descrição</span><span class="sxs-lookup"><span data-stu-id="66d77-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="66d77-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="66d77-131">O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="66d77-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="66d77-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="66d77-133">O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies.</span><span class="sxs-lookup"><span data-stu-id="66d77-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="66d77-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="66d77-135">O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores.</span><span class="sxs-lookup"><span data-stu-id="66d77-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="66d77-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-136">PhysicalFileProvider</span></span>

<span data-ttu-id="66d77-137">O [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="66d77-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="66d77-138">O `PhysicalFileProvider` usa o tipo [System.IO.File](/dotnet/api/system.io.file) (para o provedor físico) e delimita todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="66d77-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="66d77-139">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="66d77-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="66d77-140">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="66d77-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="66d77-141">Você pode criar uma instância de um provedor `PhysicalFileProvider` diretamente, ou pode solicitar um `IFileProvider` em um construtor por meio de uma [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="66d77-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="66d77-142">**Tipos estáticos**</span><span class="sxs-lookup"><span data-stu-id="66d77-142">**Static types**</span></span>

<span data-ttu-id="66d77-143">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="66d77-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="66d77-144">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="66d77-144">Types in the preceding example:</span></span>

* <span data-ttu-id="66d77-145">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="66d77-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="66d77-146">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="66d77-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="66d77-147">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="66d77-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="66d77-148">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="66d77-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="66d77-149">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="66d77-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="66d77-150">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span><span class="sxs-lookup"><span data-stu-id="66d77-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="66d77-151">**Obter tipos de provedor de arquivos com injeção de dependência**</span><span class="sxs-lookup"><span data-stu-id="66d77-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="66d77-152">Injete o provedor em qualquer construtor de classe e atribua-o a um campo local.</span><span class="sxs-lookup"><span data-stu-id="66d77-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="66d77-153">Use o campo em todos os métodos da classe para acessar arquivos.</span><span class="sxs-lookup"><span data-stu-id="66d77-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="66d77-154">No aplicativo de amostra, a classe `IndexModel` recebe uma instância `IFileProvider` para obter o conteúdo do diretório para o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="66d77-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="66d77-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66d77-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="66d77-156">Os `IDirectoryContents` são iterados na página.</span><span class="sxs-lookup"><span data-stu-id="66d77-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="66d77-157">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66d77-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="66d77-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="66d77-159">O [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="66d77-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="66d77-160">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="66d77-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="66d77-161">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="66d77-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="66d77-162">Especifique os arquivos para inserir com [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="66d77-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="66d77-163">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="66d77-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="66d77-164">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="66d77-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="66d77-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="66d77-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="66d77-166">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="66d77-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="66d77-167">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="66d77-167">Specify a relative file path.</span></span>
* <span data-ttu-id="66d77-168">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="66d77-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="66d77-169">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="66d77-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="66d77-170">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="66d77-170">Overload</span></span> | <span data-ttu-id="66d77-171">Descrição</span><span class="sxs-lookup"><span data-stu-id="66d77-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="66d77-172">ManifestEmbeddedFileProvider(Assembly, String)</span><span class="sxs-lookup"><span data-stu-id="66d77-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="66d77-173">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="66d77-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="66d77-174">Especifique o `root` para delimitar as chamadas para [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) para os recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="66d77-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="66d77-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="66d77-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="66d77-176">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` ([DateTimeOffset](/dotnet/api/system.datetimeoffset)).</span><span class="sxs-lookup"><span data-stu-id="66d77-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="66d77-177">A data de `lastModified` tem como escopo a data da última modificação para as instâncias de [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) retornadas pelo [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="66d77-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="66d77-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="66d77-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="66d77-179">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="66d77-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="66d77-180">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="66d77-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="66d77-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="66d77-181">CompositeFileProvider</span></span>

<span data-ttu-id="66d77-182">O [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="66d77-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="66d77-183">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="66d77-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="66d77-184">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="66d77-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="66d77-185">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="66d77-185">Watch for changes</span></span>

<span data-ttu-id="66d77-186">O método [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="66d77-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="66d77-187">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="66d77-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="66d77-188">`Watch` retorna um [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span><span class="sxs-lookup"><span data-stu-id="66d77-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="66d77-189">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="66d77-189">The change token exposes:</span></span>

* <span data-ttu-id="66d77-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Uma propriedade que poderá ser inspecionada para determinar se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="66d77-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="66d77-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Chamada quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="66d77-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="66d77-192">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="66d77-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="66d77-193">Para permitir o monitoramento constante, use um [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="66d77-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="66d77-194">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="66d77-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="66d77-195">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="66d77-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="66d77-196">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="66d77-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="66d77-197">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="66d77-197">Glob patterns</span></span>

<span data-ttu-id="66d77-198">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="66d77-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="66d77-199">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="66d77-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="66d77-200">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="66d77-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="66d77-201">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="66d77-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="66d77-202">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="66d77-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="66d77-203">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="66d77-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="66d77-204">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="66d77-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="66d77-205">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="66d77-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="66d77-206">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="66d77-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="66d77-207">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="66d77-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="66d77-208">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="66d77-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="66d77-209">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="66d77-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
