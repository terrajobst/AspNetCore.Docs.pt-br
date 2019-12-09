---
title: Provedores de arquivos no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: a454ca394546184968222ca2ca44d7159b19a12a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944302"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="67908-103">Provedores de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67908-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="67908-104">Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="67908-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67908-105">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="67908-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="67908-106">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="67908-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="67908-107">`IWebHostEnvironment` expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como tipos de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="67908-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="67908-108">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="67908-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="67908-109">[Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="67908-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="67908-110">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="67908-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="67908-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67908-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="67908-112">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="67908-112">File Provider interfaces</span></span>

<span data-ttu-id="67908-113">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="67908-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="67908-114">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="67908-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="67908-115">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="67908-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="67908-116">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="67908-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="67908-117">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="67908-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="67908-118">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="67908-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="67908-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="67908-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="67908-120">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="67908-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="67908-121">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="67908-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="67908-122">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67908-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="67908-123">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="67908-123">File Provider implementations</span></span>

<span data-ttu-id="67908-124">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="67908-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="67908-125">Implementação</span><span class="sxs-lookup"><span data-stu-id="67908-125">Implementation</span></span> | <span data-ttu-id="67908-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="67908-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="67908-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="67908-128">O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="67908-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="67908-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="67908-130">O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies.</span><span class="sxs-lookup"><span data-stu-id="67908-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="67908-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="67908-132">O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores.</span><span class="sxs-lookup"><span data-stu-id="67908-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="67908-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-133">PhysicalFileProvider</span></span>

<span data-ttu-id="67908-134">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="67908-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="67908-135">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="67908-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="67908-136">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="67908-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="67908-137">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67908-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67908-138">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="67908-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="67908-139">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="67908-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="67908-140">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="67908-140">Types in the preceding example:</span></span>

* <span data-ttu-id="67908-141">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="67908-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="67908-142">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="67908-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="67908-143">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="67908-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="67908-144">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="67908-145">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="67908-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="67908-146">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="67908-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="67908-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="67908-148">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="67908-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="67908-149">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="67908-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="67908-150">Adicione uma referência de pacote ao projeto para o pacote [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded).</span><span class="sxs-lookup"><span data-stu-id="67908-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="67908-151">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="67908-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="67908-152">Especifique os arquivos a inserir com [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="67908-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="67908-153">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="67908-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="67908-154">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="67908-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="67908-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="67908-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="67908-156">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="67908-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="67908-157">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="67908-157">Specify a relative file path.</span></span>
* <span data-ttu-id="67908-158">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="67908-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="67908-159">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="67908-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="67908-160">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="67908-160">Overload</span></span> | <span data-ttu-id="67908-161">Descrição</span><span class="sxs-lookup"><span data-stu-id="67908-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="67908-162">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="67908-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="67908-163">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="67908-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="67908-164">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="67908-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="67908-165">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="67908-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="67908-166">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="67908-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="67908-167">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="67908-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="67908-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-168">CompositeFileProvider</span></span>

<span data-ttu-id="67908-169">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="67908-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="67908-170">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="67908-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="67908-171">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="67908-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="67908-172">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="67908-172">Watch for changes</span></span>

<span data-ttu-id="67908-173">O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="67908-174">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="67908-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="67908-175">`Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="67908-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="67908-176">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="67908-176">The change token exposes:</span></span>

* <span data-ttu-id="67908-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; Uma propriedade que pode ser inspecionada para determinar se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="67908-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="67908-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Chamada quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="67908-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="67908-179">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="67908-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="67908-180">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="67908-181">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="67908-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="67908-182">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="67908-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="67908-183">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="67908-184">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="67908-184">Glob patterns</span></span>

<span data-ttu-id="67908-185">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)* .</span><span class="sxs-lookup"><span data-stu-id="67908-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="67908-186">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="67908-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="67908-187">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="67908-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="67908-188">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="67908-189">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="67908-190">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="67908-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="67908-191">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="67908-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="67908-192">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="67908-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="67908-193">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="67908-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="67908-194">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="67908-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="67908-195">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="67908-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="67908-196">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="67908-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67908-197">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="67908-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="67908-198">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="67908-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="67908-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como tipos de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="67908-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="67908-200">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="67908-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="67908-201">[Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="67908-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="67908-202">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="67908-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="67908-203">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67908-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="67908-204">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="67908-204">File Provider interfaces</span></span>

<span data-ttu-id="67908-205">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="67908-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="67908-206">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="67908-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="67908-207">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="67908-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="67908-208">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="67908-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="67908-209">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="67908-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="67908-210">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="67908-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="67908-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="67908-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="67908-212">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="67908-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="67908-213">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="67908-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="67908-214">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67908-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="67908-215">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="67908-215">File Provider implementations</span></span>

<span data-ttu-id="67908-216">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="67908-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="67908-217">Implementação</span><span class="sxs-lookup"><span data-stu-id="67908-217">Implementation</span></span> | <span data-ttu-id="67908-218">Descrição</span><span class="sxs-lookup"><span data-stu-id="67908-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="67908-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="67908-220">O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="67908-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="67908-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="67908-222">O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies.</span><span class="sxs-lookup"><span data-stu-id="67908-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="67908-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="67908-224">O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores.</span><span class="sxs-lookup"><span data-stu-id="67908-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="67908-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-225">PhysicalFileProvider</span></span>

<span data-ttu-id="67908-226">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="67908-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="67908-227">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="67908-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="67908-228">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="67908-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="67908-229">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="67908-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67908-230">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="67908-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="67908-231">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="67908-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="67908-232">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="67908-232">Types in the preceding example:</span></span>

* <span data-ttu-id="67908-233">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="67908-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="67908-234">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="67908-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="67908-235">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="67908-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="67908-236">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="67908-237">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="67908-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="67908-238">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="67908-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="67908-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="67908-240">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="67908-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="67908-241">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="67908-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="67908-242">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="67908-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="67908-243">Especifique os arquivos para inserir com [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="67908-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="67908-244">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="67908-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="67908-245">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="67908-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="67908-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="67908-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="67908-247">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="67908-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="67908-248">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="67908-248">Specify a relative file path.</span></span>
* <span data-ttu-id="67908-249">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="67908-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="67908-250">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="67908-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="67908-251">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="67908-251">Overload</span></span> | <span data-ttu-id="67908-252">Descrição</span><span class="sxs-lookup"><span data-stu-id="67908-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="67908-253">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="67908-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="67908-254">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="67908-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="67908-255">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="67908-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="67908-256">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="67908-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="67908-257">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="67908-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="67908-258">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="67908-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="67908-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="67908-259">CompositeFileProvider</span></span>

<span data-ttu-id="67908-260">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="67908-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="67908-261">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="67908-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="67908-262">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="67908-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="67908-263">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="67908-263">Watch for changes</span></span>

<span data-ttu-id="67908-264">O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="67908-265">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="67908-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="67908-266">`Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="67908-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="67908-267">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="67908-267">The change token exposes:</span></span>

* <span data-ttu-id="67908-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; Uma propriedade que pode ser inspecionada para determinar se uma alteração ocorreu.</span><span class="sxs-lookup"><span data-stu-id="67908-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="67908-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Chamada quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="67908-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="67908-270">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="67908-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="67908-271">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="67908-272">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="67908-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="67908-273">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="67908-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="67908-274">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="67908-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="67908-275">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="67908-275">Glob patterns</span></span>

<span data-ttu-id="67908-276">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)* .</span><span class="sxs-lookup"><span data-stu-id="67908-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="67908-277">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="67908-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="67908-278">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="67908-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="67908-279">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="67908-280">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="67908-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="67908-281">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="67908-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="67908-282">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="67908-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="67908-283">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="67908-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="67908-284">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="67908-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="67908-285">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="67908-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="67908-286">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="67908-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="67908-287">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="67908-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
