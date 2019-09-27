---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317768"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="2edb8-103">Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI</span><span class="sxs-lookup"><span data-stu-id="2edb8-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="2edb8-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="2edb8-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="2edb8-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.</span><span class="sxs-lookup"><span data-stu-id="2edb8-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="2edb8-106">Instalação</span><span class="sxs-lookup"><span data-stu-id="2edb8-106">Installation</span></span>

<span data-ttu-id="2edb8-107">Para instalar `Microsoft.dotnet-openapi`o, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2edb8-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="2edb8-108">Adicionar</span><span class="sxs-lookup"><span data-stu-id="2edb8-108">Add</span></span>

<span data-ttu-id="2edb8-109">Adicionar uma referência de openapi usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="2edb8-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="2edb8-110">A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="2edb8-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="2edb8-111">Adicionar arquivo</span><span class="sxs-lookup"><span data-stu-id="2edb8-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="2edb8-112">Opções</span><span class="sxs-lookup"><span data-stu-id="2edb8-112">Options</span></span>

| <span data-ttu-id="2edb8-113">Opção curta</span><span class="sxs-lookup"><span data-stu-id="2edb8-113">Short option</span></span>| <span data-ttu-id="2edb8-114">Opção Long</span><span class="sxs-lookup"><span data-stu-id="2edb8-114">Long option</span></span>| <span data-ttu-id="2edb8-115">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-115">Description</span></span> | <span data-ttu-id="2edb8-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="2edb8-117">-v</span><span class="sxs-lookup"><span data-stu-id="2edb8-117">-v</span></span>|<span data-ttu-id="2edb8-118">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="2edb8-118">--verbose</span></span> | <span data-ttu-id="2edb8-119">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="2edb8-119">Show verbose output.</span></span> |<span data-ttu-id="2edb8-120">dotnet openapi Adicionar arquivo *-v* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="2edb8-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="2edb8-121">-p</span><span class="sxs-lookup"><span data-stu-id="2edb8-121">-p</span></span>|<span data-ttu-id="2edb8-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2edb8-122">--updateProject</span></span> | <span data-ttu-id="2edb8-123">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="2edb8-123">The project to operate on.</span></span> |<span data-ttu-id="2edb8-124">dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="2edb8-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="2edb8-125">-c</span><span class="sxs-lookup"><span data-stu-id="2edb8-125">-c</span></span>|<span data-ttu-id="2edb8-126">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="2edb8-126">--code-generator</span></span>| <span data-ttu-id="2edb8-127">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="2edb8-128">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="2edb8-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="2edb8-129">Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="2edb8-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="2edb8-130">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="2edb8-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="2edb8-131">-h</span><span class="sxs-lookup"><span data-stu-id="2edb8-131">-h</span></span>|<span data-ttu-id="2edb8-132">--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-132">--help</span></span>|<span data-ttu-id="2edb8-133">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-133">Show help information</span></span>|<span data-ttu-id="2edb8-134">dotnet openapi Adicionar arquivo--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="2edb8-135">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2edb8-135">Arguments</span></span>

|  <span data-ttu-id="2edb8-136">Argumento</span><span class="sxs-lookup"><span data-stu-id="2edb8-136">Argument</span></span>  | <span data-ttu-id="2edb8-137">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-137">Description</span></span> | <span data-ttu-id="2edb8-138">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="2edb8-139">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="2edb8-139">source-file</span></span> | <span data-ttu-id="2edb8-140">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-140">The source to create a reference from.</span></span> <span data-ttu-id="2edb8-141">Deve ser um arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="2edb8-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="2edb8-142">dotnet openapi Adicionar arquivo *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="2edb8-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="2edb8-143">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="2edb8-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="2edb8-144">Opções</span><span class="sxs-lookup"><span data-stu-id="2edb8-144">Options</span></span>

| <span data-ttu-id="2edb8-145">Opção curta</span><span class="sxs-lookup"><span data-stu-id="2edb8-145">Short option</span></span>| <span data-ttu-id="2edb8-146">Opção Long</span><span class="sxs-lookup"><span data-stu-id="2edb8-146">Long option</span></span>| <span data-ttu-id="2edb8-147">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-147">Description</span></span> | <span data-ttu-id="2edb8-148">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="2edb8-149">-v</span><span class="sxs-lookup"><span data-stu-id="2edb8-149">-v</span></span>|<span data-ttu-id="2edb8-150">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="2edb8-150">--verbose</span></span> | <span data-ttu-id="2edb8-151">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="2edb8-151">Show verbose output.</span></span> |<span data-ttu-id="2edb8-152">dotnet openapi Adicionar URL *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2edb8-153">-p</span><span class="sxs-lookup"><span data-stu-id="2edb8-153">-p</span></span>|<span data-ttu-id="2edb8-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2edb8-154">--updateProject</span></span> | <span data-ttu-id="2edb8-155">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="2edb8-155">The project to operate on.</span></span> |<span data-ttu-id="2edb8-156">dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2edb8-157">-o</span><span class="sxs-lookup"><span data-stu-id="2edb8-157">-o</span></span>|<span data-ttu-id="2edb8-158">--arquivo de saída</span><span class="sxs-lookup"><span data-stu-id="2edb8-158">--output-file</span></span> | <span data-ttu-id="2edb8-159">Onde posicionar a cópia local do arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="2edb8-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="2edb8-160">dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--saída-arquivo myclient. JSON*</span><span class="sxs-lookup"><span data-stu-id="2edb8-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="2edb8-161">-c</span><span class="sxs-lookup"><span data-stu-id="2edb8-161">-c</span></span>|<span data-ttu-id="2edb8-162">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="2edb8-162">--code-generator</span></span>| <span data-ttu-id="2edb8-163">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="2edb8-164">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="2edb8-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="2edb8-165">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="2edb8-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="2edb8-166">-h</span><span class="sxs-lookup"><span data-stu-id="2edb8-166">-h</span></span>|<span data-ttu-id="2edb8-167">--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-167">--help</span></span>|<span data-ttu-id="2edb8-168">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-168">Show help information</span></span>|<span data-ttu-id="2edb8-169">dotnet openapi Adicionar URL--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="2edb8-170">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2edb8-170">Arguments</span></span>

|  <span data-ttu-id="2edb8-171">Argumento</span><span class="sxs-lookup"><span data-stu-id="2edb8-171">Argument</span></span>  | <span data-ttu-id="2edb8-172">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-172">Description</span></span> | <span data-ttu-id="2edb8-173">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="2edb8-174">origem-URL</span><span class="sxs-lookup"><span data-stu-id="2edb8-174">source-URL</span></span> | <span data-ttu-id="2edb8-175">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-175">The source to create a reference from.</span></span> <span data-ttu-id="2edb8-176">Deve ser uma URL.</span><span class="sxs-lookup"><span data-stu-id="2edb8-176">Must be a URL.</span></span> |<span data-ttu-id="2edb8-177">dotnet openapi Adicionar URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="2edb8-178">Remover</span><span class="sxs-lookup"><span data-stu-id="2edb8-178">Remove</span></span>

<span data-ttu-id="2edb8-179">Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido.</span><span class="sxs-lookup"><span data-stu-id="2edb8-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="2edb8-180">Quando a referência de OpenAPI for removida, os clientes não serão gerados.</span><span class="sxs-lookup"><span data-stu-id="2edb8-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="2edb8-181">Os arquivos local *. JSON* e *. YAML* são excluídos.</span><span class="sxs-lookup"><span data-stu-id="2edb8-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="2edb8-182">Opções</span><span class="sxs-lookup"><span data-stu-id="2edb8-182">Options</span></span>

| <span data-ttu-id="2edb8-183">Opção curta</span><span class="sxs-lookup"><span data-stu-id="2edb8-183">Short option</span></span>| <span data-ttu-id="2edb8-184">Opção Long</span><span class="sxs-lookup"><span data-stu-id="2edb8-184">Long option</span></span>| <span data-ttu-id="2edb8-185">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-185">Description</span></span>| <span data-ttu-id="2edb8-186">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="2edb8-187">-v</span><span class="sxs-lookup"><span data-stu-id="2edb8-187">-v</span></span>|<span data-ttu-id="2edb8-188">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="2edb8-188">--verbose</span></span> | <span data-ttu-id="2edb8-189">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="2edb8-189">Show verbose output.</span></span> |<span data-ttu-id="2edb8-190">dotnet openapi remover *-v*</span><span class="sxs-lookup"><span data-stu-id="2edb8-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="2edb8-191">-p</span><span class="sxs-lookup"><span data-stu-id="2edb8-191">-p</span></span>|<span data-ttu-id="2edb8-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2edb8-192">--updateProject</span></span> | <span data-ttu-id="2edb8-193">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="2edb8-193">The project to operate on.</span></span> |<span data-ttu-id="2edb8-194">dotnet openapi remover *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="2edb8-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="2edb8-195">-h</span><span class="sxs-lookup"><span data-stu-id="2edb8-195">-h</span></span>|<span data-ttu-id="2edb8-196">--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-196">--help</span></span>|<span data-ttu-id="2edb8-197">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-197">Show help information</span></span>|<span data-ttu-id="2edb8-198">dotnet openapi remover--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="2edb8-199">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2edb8-199">Arguments</span></span>

|  <span data-ttu-id="2edb8-200">Argumento</span><span class="sxs-lookup"><span data-stu-id="2edb8-200">Argument</span></span>  | <span data-ttu-id="2edb8-201">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-201">Description</span></span>| <span data-ttu-id="2edb8-202">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="2edb8-203">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="2edb8-203">source-file</span></span> | <span data-ttu-id="2edb8-204">A origem para a qual remover a referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-204">The source to remove the reference to.</span></span> |<span data-ttu-id="2edb8-205">dotnet openapi remover *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="2edb8-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="2edb8-206">Atualizar</span><span class="sxs-lookup"><span data-stu-id="2edb8-206">Refresh</span></span>

<span data-ttu-id="2edb8-207">Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.</span><span class="sxs-lookup"><span data-stu-id="2edb8-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="2edb8-208">Opções</span><span class="sxs-lookup"><span data-stu-id="2edb8-208">Options</span></span>

| <span data-ttu-id="2edb8-209">Opção curta</span><span class="sxs-lookup"><span data-stu-id="2edb8-209">Short option</span></span>| <span data-ttu-id="2edb8-210">Opção Long</span><span class="sxs-lookup"><span data-stu-id="2edb8-210">Long option</span></span>| <span data-ttu-id="2edb8-211">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-211">Description</span></span> | <span data-ttu-id="2edb8-212">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="2edb8-213">-v</span><span class="sxs-lookup"><span data-stu-id="2edb8-213">-v</span></span>|<span data-ttu-id="2edb8-214">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="2edb8-214">--verbose</span></span> | <span data-ttu-id="2edb8-215">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="2edb8-215">Show verbose output.</span></span> | <span data-ttu-id="2edb8-216">dotnet openapi atualização *– v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2edb8-217">-p</span><span class="sxs-lookup"><span data-stu-id="2edb8-217">-p</span></span>|<span data-ttu-id="2edb8-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="2edb8-218">--updateProject</span></span> | <span data-ttu-id="2edb8-219">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="2edb8-219">The project to operate on.</span></span> | <span data-ttu-id="2edb8-220">dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="2edb8-221">-h</span><span class="sxs-lookup"><span data-stu-id="2edb8-221">-h</span></span>|<span data-ttu-id="2edb8-222">--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-222">--help</span></span>|<span data-ttu-id="2edb8-223">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-223">Show help information</span></span>|<span data-ttu-id="2edb8-224">dotnet openapi atualização--ajuda</span><span class="sxs-lookup"><span data-stu-id="2edb8-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="2edb8-225">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2edb8-225">Arguments</span></span>

|  <span data-ttu-id="2edb8-226">Argumento</span><span class="sxs-lookup"><span data-stu-id="2edb8-226">Argument</span></span>  | <span data-ttu-id="2edb8-227">Descrição</span><span class="sxs-lookup"><span data-stu-id="2edb8-227">Description</span></span> | <span data-ttu-id="2edb8-228">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2edb8-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="2edb8-229">origem-URL</span><span class="sxs-lookup"><span data-stu-id="2edb8-229">source-URL</span></span> | <span data-ttu-id="2edb8-230">A URL da qual atualizar a referência.</span><span class="sxs-lookup"><span data-stu-id="2edb8-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="2edb8-231">atualização do dotnet openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="2edb8-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
