---
title: Estrutura do diretório do ASP.NET Core
author: guardrex
description: Saiba mais sobre a estrutura do diretório de aplicativos publicados do ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ba5cb96dfdcdca10034299e3bbe662ce056af791
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870260"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="632c6-103">Estrutura do diretório do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="632c6-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="632c6-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="632c6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="632c6-105">O diretório *publish* contém os ativos implantáveis do aplicativo produzidos pelo comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="632c6-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="632c6-106">O diretório contém:</span><span class="sxs-lookup"><span data-stu-id="632c6-106">The directory contains:</span></span>

* <span data-ttu-id="632c6-107">Arquivos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="632c6-107">Application files</span></span>
* <span data-ttu-id="632c6-108">Arquivos de configuração</span><span class="sxs-lookup"><span data-stu-id="632c6-108">Configuration files</span></span>
* <span data-ttu-id="632c6-109">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="632c6-109">Static assets</span></span>
* <span data-ttu-id="632c6-110">Pacotes</span><span class="sxs-lookup"><span data-stu-id="632c6-110">Packages</span></span>
* <span data-ttu-id="632c6-111">Um runtime ([somente implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd))</span><span class="sxs-lookup"><span data-stu-id="632c6-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="632c6-112">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="632c6-112">App Type</span></span> | <span data-ttu-id="632c6-113">Estrutura de diretórios</span><span class="sxs-lookup"><span data-stu-id="632c6-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="632c6-114">Executável dependente de estrutura (FDE)</span><span class="sxs-lookup"><span data-stu-id="632c6-114">Framework-dependent Executable (FDE)</span></span>](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li><span data-ttu-id="632c6-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="632c6-115">publish&dagger;</span></span><ul><li><span data-ttu-id="632c6-116">Exibições&dagger; aplicativos MVC; Se as exibições não forem pré-compiladas</span><span class="sxs-lookup"><span data-stu-id="632c6-116">Views&dagger; MVC apps; if views aren't precompiled</span></span></li><li><span data-ttu-id="632c6-117">Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</span><span class="sxs-lookup"><span data-stu-id="632c6-117">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="632c6-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="632c6-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="632c6-119">*arquivos. dll</li><li>{nome do assembly}. deps. json</li><li>{nome do assembly}. dll</li><li>{nome do assembly} {. Extensão da extensão} *. exe* no Windows, nenhuma extensão no MacOS ou Linux</li><li>{nome do assembly}. pdb</li><li>{nome do assembly}. Views. dll</li><li>{nome do ASSEMBLY}. Views. pdb</li><li>{nome do ASSEMBLY}. runtimeconfig. JSON</li><li>Web. config (implantações do IIS)</li><li>createdump ([utilitário createdump do Linux](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* . portanto (biblioteca de objetos compartilhados do Linux)</span><span class="sxs-lookup"><span data-stu-id="632c6-119">*.dll files</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}{.EXTENSION} *.exe* extension on Windows, no extension on macOS or Linux</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config (IIS deployments)</li><li>createdump ([Linux createdump utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>*.so (Linux shared object library)</span></span></li><li><span data-ttu-id="632c6-120">*. a (arquivo MacOS)</li><li>* . dylib (biblioteca dinâmica MacOS)</span><span class="sxs-lookup"><span data-stu-id="632c6-120">*.a (macOS archive)</li><li>*.dylib (macOS dynamic library)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="632c6-121">Implantação independente (SCD)</span><span class="sxs-lookup"><span data-stu-id="632c6-121">Self-contained Deployment (SCD)</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="632c6-122">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="632c6-122">publish&dagger;</span></span><ul><li><span data-ttu-id="632c6-123">Exibições&dagger; aplicativos MVC, se as exibições não forem pré-compiladas</span><span class="sxs-lookup"><span data-stu-id="632c6-123">Views&dagger; MVC apps, if views aren't precompiled</span></span></li><li><span data-ttu-id="632c6-124">Páginas&dagger; aplicativos MVC ou Razor Pages, se as páginas não forem pré-compiladas</span><span class="sxs-lookup"><span data-stu-id="632c6-124">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="632c6-125">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="632c6-125">wwwroot&dagger;</span></span></li><li><span data-ttu-id="632c6-126">arquivos \*. dll</span><span class="sxs-lookup"><span data-stu-id="632c6-126">\*.dll files</span></span></li><li><span data-ttu-id="632c6-127">{NOME DO ASSEMBLY}.deps.json</span><span class="sxs-lookup"><span data-stu-id="632c6-127">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="632c6-128">{NOME DO ASSEMBLY}.dll</span><span class="sxs-lookup"><span data-stu-id="632c6-128">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="632c6-129">{NOME DO ASSEMBLY}.exe</span><span class="sxs-lookup"><span data-stu-id="632c6-129">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="632c6-130">{NOME DO ASSEMBLY}.pdb</span><span class="sxs-lookup"><span data-stu-id="632c6-130">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="632c6-131">{NOME DO ASSEMBLY}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="632c6-131">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="632c6-132">{NOME DO ASSEMBLY}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="632c6-132">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="632c6-133">{NOME DO ASSEMBLY}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="632c6-133">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="632c6-134">web.config (implantações do IIS)</span><span class="sxs-lookup"><span data-stu-id="632c6-134">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="632c6-135">&dagger;Indica um diretório</span><span class="sxs-lookup"><span data-stu-id="632c6-135">&dagger;Indicates a directory</span></span>

<span data-ttu-id="632c6-136">O diretório *publish* representa o *caminho raiz de conteúdo* (também chamado de *caminho base do aplicativo*) da implantação.</span><span class="sxs-lookup"><span data-stu-id="632c6-136">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="632c6-137">Qualquer que seja o nome fornecido para o diretório *publish* do aplicativo implantado no servidor, o local dele serve como o caminho físico do servidor para o aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="632c6-137">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="632c6-138">O diretório *wwwroot*, se presente, contém somente ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="632c6-138">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="632c6-139">Criar uma pasta *Logs* é útil para o [log de depuração aprimorado do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="632c6-139">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="632c6-140">As pastas no caminho fornecido para o valor `<handlerSetting>` não são criadas automaticamente pelo módulo e devem existir previamente na implantação para permitir que o módulo grave o log de depuração.</span><span class="sxs-lookup"><span data-stu-id="632c6-140">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="632c6-141">Um diretório *Logs* pode ser criado para a implantação usando uma das duas abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="632c6-141">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="632c6-142">Adicione o seguinte elemento `<Target>` ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="632c6-142">Add the following `<Target>` element to the project file:</span></span>

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   <span data-ttu-id="632c6-143">O elemento `<MakeDir>` cria uma pasta *Logs* vazia na saída publicada.</span><span class="sxs-lookup"><span data-stu-id="632c6-143">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="632c6-144">O elemento usa a propriedade `PublishDir` para determinar o local de destino no qual criar a pasta.</span><span class="sxs-lookup"><span data-stu-id="632c6-144">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="632c6-145">Vários métodos de implantação, como a Implantação da Web, ignoram pastas vazias durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="632c6-145">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="632c6-146">O elemento `<WriteLinesToFile>` gera um arquivo na pasta *Logs*, o que garante a implantação da pasta no servidor.</span><span class="sxs-lookup"><span data-stu-id="632c6-146">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="632c6-147">A criação de pasta usando essa abordagem poderá falhar se o processo de trabalho não tiver acesso de gravação para a pasta de destino.</span><span class="sxs-lookup"><span data-stu-id="632c6-147">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="632c6-148">Crie fisicamente o diretório *Logs* no servidor na implantação.</span><span class="sxs-lookup"><span data-stu-id="632c6-148">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="632c6-149">O diretório de implantação requer permissões de leitura/execução.</span><span class="sxs-lookup"><span data-stu-id="632c6-149">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="632c6-150">O diretório *Logs* requer permissões de leitura/gravação.</span><span class="sxs-lookup"><span data-stu-id="632c6-150">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="632c6-151">Diretórios adicionais em que os arquivos são gravados exigem permissões de leitura/gravação.</span><span class="sxs-lookup"><span data-stu-id="632c6-151">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="632c6-152">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="632c6-152">Additional resources</span></span>

* [<span data-ttu-id="632c6-153">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="632c6-153">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="632c6-154">Implantação de aplicativos do .NET Core</span><span class="sxs-lookup"><span data-stu-id="632c6-154">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="632c6-155">Estruturas de destino</span><span class="sxs-lookup"><span data-stu-id="632c6-155">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="632c6-156">Catálogo de RIDs do .NET Core</span><span class="sxs-lookup"><span data-stu-id="632c6-156">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
