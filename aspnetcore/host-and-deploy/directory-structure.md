---
title: Estrutura do diretório do ASP.NET Core
author: guardrex
description: Saiba mais sobre a estrutura do diretório de aplicativos publicados do ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: host-and-deploy/directory-structure
ms.openlocfilehash: f1df047decc7a0a6b7dcee57a690c55eea428b05
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166974"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="f7f77-103">Estrutura do diretório do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f77-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="f7f77-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7f77-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7f77-105">O diretório *publish* contém os ativos implantáveis do aplicativo produzidos pelo comando [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="f7f77-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f7f77-106">O diretório contém:</span><span class="sxs-lookup"><span data-stu-id="f7f77-106">The directory contains:</span></span>

* <span data-ttu-id="f7f77-107">Arquivos de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f7f77-107">Application files</span></span>
* <span data-ttu-id="f7f77-108">Arquivos de configuração</span><span class="sxs-lookup"><span data-stu-id="f7f77-108">Configuration files</span></span>
* <span data-ttu-id="f7f77-109">Ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="f7f77-109">Static assets</span></span>
* <span data-ttu-id="f7f77-110">Pacotes</span><span class="sxs-lookup"><span data-stu-id="f7f77-110">Packages</span></span>
* <span data-ttu-id="f7f77-111">Um tempo de execução ([somente implantação autocontida](/dotnet/core/deploying/#self-contained-deployments-scd))</span><span class="sxs-lookup"><span data-stu-id="f7f77-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="f7f77-112">Tipo de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f7f77-112">App Type</span></span> | <span data-ttu-id="f7f77-113">Estrutura de diretórios</span><span class="sxs-lookup"><span data-stu-id="f7f77-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="f7f77-114">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="f7f77-114">Framework-dependent Deployment</span></span>](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li><span data-ttu-id="f7f77-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="f7f77-115">publish&dagger;</span></span><ul><li><span data-ttu-id="f7f77-116">Exibições&dagger; (aplicativos MVC; se as exibições não são pré-compiladas)</span><span class="sxs-lookup"><span data-stu-id="f7f77-116">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="f7f77-117">Páginas&dagger; (aplicativos de Páginas do Razor ou MVC, se as páginas não são pré-compiladas)</span><span class="sxs-lookup"><span data-stu-id="f7f77-117">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="f7f77-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="f7f77-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="f7f77-119">arquivos \*\.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-119">\*\.dll files</span></span></li><li><span data-ttu-id="f7f77-120">{NOME DO ASSEMBLY}.deps.json</span><span class="sxs-lookup"><span data-stu-id="f7f77-120">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="f7f77-121">{NOME DO ASSEMBLY}.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-121">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="f7f77-122">{NOME DO ASSEMBLY}.pdb</span><span class="sxs-lookup"><span data-stu-id="f7f77-122">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="f7f77-123">{NOME DO ASSEMBLY}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-123">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="f7f77-124">{NOME DO ASSEMBLY}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="f7f77-124">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="f7f77-125">{NOME DO ASSEMBLY}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="f7f77-125">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="f7f77-126">web.config (implantações do IIS)</span><span class="sxs-lookup"><span data-stu-id="f7f77-126">web.config (IIS deployments)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="f7f77-127">Implantação autossuficiente</span><span class="sxs-lookup"><span data-stu-id="f7f77-127">Self-contained Deployment</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="f7f77-128">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="f7f77-128">publish&dagger;</span></span><ul><li><span data-ttu-id="f7f77-129">Exibições&dagger; (aplicativos MVC; se as exibições não são pré-compiladas)</span><span class="sxs-lookup"><span data-stu-id="f7f77-129">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="f7f77-130">Páginas&dagger; (aplicativos de Páginas do Razor ou MVC, se as páginas não são pré-compiladas)</span><span class="sxs-lookup"><span data-stu-id="f7f77-130">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="f7f77-131">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="f7f77-131">wwwroot&dagger;</span></span></li><li><span data-ttu-id="f7f77-132">arquivos \*.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-132">\*.dll files</span></span></li><li><span data-ttu-id="f7f77-133">{NOME DO ASSEMBLY}.deps.json</span><span class="sxs-lookup"><span data-stu-id="f7f77-133">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="f7f77-134">{NOME DO ASSEMBLY}.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-134">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="f7f77-135">{NOME DO ASSEMBLY}.exe</span><span class="sxs-lookup"><span data-stu-id="f7f77-135">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="f7f77-136">{NOME DO ASSEMBLY}.pdb</span><span class="sxs-lookup"><span data-stu-id="f7f77-136">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="f7f77-137">{NOME DO ASSEMBLY}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="f7f77-137">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="f7f77-138">{NOME DO ASSEMBLY}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="f7f77-138">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="f7f77-139">{NOME DO ASSEMBLY}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="f7f77-139">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="f7f77-140">web.config (implantações do IIS)</span><span class="sxs-lookup"><span data-stu-id="f7f77-140">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="f7f77-141">&dagger;Indica um diretório</span><span class="sxs-lookup"><span data-stu-id="f7f77-141">&dagger;Indicates a directory</span></span>

<span data-ttu-id="f7f77-142">O diretório *publish* representa o *caminho raiz de conteúdo* (também chamado de *caminho base do aplicativo*) da implantação.</span><span class="sxs-lookup"><span data-stu-id="f7f77-142">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="f7f77-143">Qualquer que seja o nome fornecido para o diretório *publish* do aplicativo implantado no servidor, o local dele serve como o caminho físico do servidor para o aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="f7f77-143">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="f7f77-144">O diretório *wwwroot*, se presente, contém somente ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="f7f77-144">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7f77-145">Criar uma pasta *Logs* é útil para o [log de depuração aprimorado do Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="f7f77-145">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="f7f77-146">As pastas no caminho fornecido para o valor `<handlerSetting>` não são criadas automaticamente pelo módulo e devem existir previamente na implantação para permitir que o módulo grave o log de depuração.</span><span class="sxs-lookup"><span data-stu-id="f7f77-146">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="f7f77-147">Um diretório *Logs* pode ser criado para a implantação usando uma das duas abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="f7f77-147">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="f7f77-148">Adicione o seguinte elemento `<Target>` ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="f7f77-148">Add the following `<Target>` element to the project file:</span></span>

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

   <span data-ttu-id="f7f77-149">O elemento `<MakeDir>` cria uma pasta *Logs* vazia na saída publicada.</span><span class="sxs-lookup"><span data-stu-id="f7f77-149">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="f7f77-150">O elemento usa a propriedade `PublishDir` para determinar o local de destino no qual criar a pasta.</span><span class="sxs-lookup"><span data-stu-id="f7f77-150">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="f7f77-151">Vários métodos de implantação, como a Implantação da Web, ignoram pastas vazias durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="f7f77-151">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="f7f77-152">O elemento `<WriteLinesToFile>` gera um arquivo na pasta *Logs*, o que garante a implantação da pasta no servidor.</span><span class="sxs-lookup"><span data-stu-id="f7f77-152">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="f7f77-153">A criação de pasta usando essa abordagem poderá falhar se o processo de trabalho não tiver acesso de gravação para a pasta de destino.</span><span class="sxs-lookup"><span data-stu-id="f7f77-153">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="f7f77-154">Crie fisicamente o diretório *Logs* no servidor na implantação.</span><span class="sxs-lookup"><span data-stu-id="f7f77-154">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="f7f77-155">O diretório de implantação requer permissões de leitura/execução.</span><span class="sxs-lookup"><span data-stu-id="f7f77-155">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="f7f77-156">O diretório *Logs* requer permissões de leitura/gravação.</span><span class="sxs-lookup"><span data-stu-id="f7f77-156">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="f7f77-157">Diretórios adicionais em que os arquivos são gravados exigem permissões de leitura/gravação.</span><span class="sxs-lookup"><span data-stu-id="f7f77-157">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f7f77-158">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7f77-158">Additional resources</span></span>

* [<span data-ttu-id="f7f77-159">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="f7f77-159">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="f7f77-160">Implantação de aplicativos do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f77-160">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="f7f77-161">Estruturas de destino</span><span class="sxs-lookup"><span data-stu-id="f7f77-161">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="f7f77-162">Catálogo de RIDs do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f77-162">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
