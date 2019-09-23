---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187456"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="17ea0-103">Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI</span><span class="sxs-lookup"><span data-stu-id="17ea0-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="17ea0-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="17ea0-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="17ea0-105">`Microsoft.dotnet-openapi`é uma ferramenta global .NET Core para gerenciar referências de [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.</span><span class="sxs-lookup"><span data-stu-id="17ea0-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="17ea0-106">Instalação</span><span class="sxs-lookup"><span data-stu-id="17ea0-106">Installation</span></span>

<span data-ttu-id="17ea0-107">Para instalar `Microsoft.dotnet-openapi`o, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="17ea0-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="17ea0-108">`Microsoft.dotnet-openapi`é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools).</span><span class="sxs-lookup"><span data-stu-id="17ea0-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="17ea0-109">Adicionar</span><span class="sxs-lookup"><span data-stu-id="17ea0-109">Add</span></span>

<span data-ttu-id="17ea0-110">Adicionar uma referência de openapi usando qualquer um dos comandos nessa página adiciona um `<OpenApiReference />` elemento semelhante ao seguinte ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="17ea0-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="17ea0-111">A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="17ea0-111">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="17ea0-112">Adicionar arquivo</span><span class="sxs-lookup"><span data-stu-id="17ea0-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="17ea0-113">Opções</span><span class="sxs-lookup"><span data-stu-id="17ea0-113">Options</span></span>

| <span data-ttu-id="17ea0-114">Opção curta</span><span class="sxs-lookup"><span data-stu-id="17ea0-114">Short option</span></span>| <span data-ttu-id="17ea0-115">Opção Long</span><span class="sxs-lookup"><span data-stu-id="17ea0-115">Long option</span></span>| <span data-ttu-id="17ea0-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-116">Description</span></span> | <span data-ttu-id="17ea0-117">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="17ea0-118">-v</span><span class="sxs-lookup"><span data-stu-id="17ea0-118">-v</span></span>|<span data-ttu-id="17ea0-119">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="17ea0-119">--verbose</span></span> | <span data-ttu-id="17ea0-120">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="17ea0-120">Show verbose output.</span></span> |<span data-ttu-id="17ea0-121">dotnet openapi Adicionar arquivo *-v* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="17ea0-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="17ea0-122">-p</span><span class="sxs-lookup"><span data-stu-id="17ea0-122">-p</span></span>|<span data-ttu-id="17ea0-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="17ea0-123">--updateProject</span></span> | <span data-ttu-id="17ea0-124">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="17ea0-124">The project to operate on.</span></span> |<span data-ttu-id="17ea0-125">dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="17ea0-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="17ea0-126">-c</span><span class="sxs-lookup"><span data-stu-id="17ea0-126">-c</span></span>|<span data-ttu-id="17ea0-127">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="17ea0-127">--code-generator</span></span>| <span data-ttu-id="17ea0-128">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="17ea0-129">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="17ea0-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="17ea0-130">Se `--code-generator` não for especificado, as ferramentas padrão serão `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="17ea0-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="17ea0-131">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="17ea0-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="17ea0-132">-h</span><span class="sxs-lookup"><span data-stu-id="17ea0-132">-h</span></span>|<span data-ttu-id="17ea0-133">--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-133">--help</span></span>|<span data-ttu-id="17ea0-134">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-134">Show help information</span></span>|<span data-ttu-id="17ea0-135">dotnet openapi Adicionar arquivo--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="17ea0-136">Arguments</span><span class="sxs-lookup"><span data-stu-id="17ea0-136">Arguments</span></span>

|  <span data-ttu-id="17ea0-137">Argumento</span><span class="sxs-lookup"><span data-stu-id="17ea0-137">Argument</span></span>  | <span data-ttu-id="17ea0-138">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-138">Description</span></span> | <span data-ttu-id="17ea0-139">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="17ea0-140">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="17ea0-140">source-file</span></span> | <span data-ttu-id="17ea0-141">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-141">The source to create a reference from.</span></span> <span data-ttu-id="17ea0-142">Deve ser um arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="17ea0-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="17ea0-143">dotnet openapi Adicionar arquivo *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="17ea0-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="17ea0-144">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="17ea0-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="17ea0-145">Opções</span><span class="sxs-lookup"><span data-stu-id="17ea0-145">Options</span></span>

| <span data-ttu-id="17ea0-146">Opção curta</span><span class="sxs-lookup"><span data-stu-id="17ea0-146">Short option</span></span>| <span data-ttu-id="17ea0-147">Opção Long</span><span class="sxs-lookup"><span data-stu-id="17ea0-147">Long option</span></span>| <span data-ttu-id="17ea0-148">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-148">Description</span></span> | <span data-ttu-id="17ea0-149">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="17ea0-150">-v</span><span class="sxs-lookup"><span data-stu-id="17ea0-150">-v</span></span>|<span data-ttu-id="17ea0-151">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="17ea0-151">--verbose</span></span> | <span data-ttu-id="17ea0-152">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="17ea0-152">Show verbose output.</span></span> |<span data-ttu-id="17ea0-153">dotnet openapi Adicionar URL *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="17ea0-154">-p</span><span class="sxs-lookup"><span data-stu-id="17ea0-154">-p</span></span>|<span data-ttu-id="17ea0-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="17ea0-155">--updateProject</span></span> | <span data-ttu-id="17ea0-156">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="17ea0-156">The project to operate on.</span></span> |<span data-ttu-id="17ea0-157">dotnet openapi Adicionar URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="17ea0-158">-o</span><span class="sxs-lookup"><span data-stu-id="17ea0-158">-o</span></span>|<span data-ttu-id="17ea0-159">--arquivo de saída</span><span class="sxs-lookup"><span data-stu-id="17ea0-159">--output-file</span></span> | <span data-ttu-id="17ea0-160">Onde posicionar a cópia local do arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="17ea0-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="17ea0-161">dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--saída-arquivo myclient. JSON*</span><span class="sxs-lookup"><span data-stu-id="17ea0-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="17ea0-162">-c</span><span class="sxs-lookup"><span data-stu-id="17ea0-162">-c</span></span>|<span data-ttu-id="17ea0-163">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="17ea0-163">--code-generator</span></span>| <span data-ttu-id="17ea0-164">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="17ea0-165">As opções `NSwagCSharp` são `NSwagTypeScript`e.</span><span class="sxs-lookup"><span data-stu-id="17ea0-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="17ea0-166">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="17ea0-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="17ea0-167">-h</span><span class="sxs-lookup"><span data-stu-id="17ea0-167">-h</span></span>|<span data-ttu-id="17ea0-168">--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-168">--help</span></span>|<span data-ttu-id="17ea0-169">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-169">Show help information</span></span>|<span data-ttu-id="17ea0-170">dotnet openapi Adicionar URL--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="17ea0-171">Arguments</span><span class="sxs-lookup"><span data-stu-id="17ea0-171">Arguments</span></span>

|  <span data-ttu-id="17ea0-172">Argumento</span><span class="sxs-lookup"><span data-stu-id="17ea0-172">Argument</span></span>  | <span data-ttu-id="17ea0-173">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-173">Description</span></span> | <span data-ttu-id="17ea0-174">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="17ea0-175">origem-URL</span><span class="sxs-lookup"><span data-stu-id="17ea0-175">source-URL</span></span> | <span data-ttu-id="17ea0-176">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-176">The source to create a reference from.</span></span> <span data-ttu-id="17ea0-177">Deve ser uma URL.</span><span class="sxs-lookup"><span data-stu-id="17ea0-177">Must be a URL.</span></span> |<span data-ttu-id="17ea0-178">dotnet openapi Adicionar URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="17ea0-179">Remover</span><span class="sxs-lookup"><span data-stu-id="17ea0-179">Remove</span></span>

<span data-ttu-id="17ea0-180">Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido.</span><span class="sxs-lookup"><span data-stu-id="17ea0-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="17ea0-181">Quando a referência de OpenAPI for removida, os clientes não serão gerados.</span><span class="sxs-lookup"><span data-stu-id="17ea0-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="17ea0-182">Os arquivos local *. JSON* e *. YAML* são excluídos.</span><span class="sxs-lookup"><span data-stu-id="17ea0-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="17ea0-183">Opções</span><span class="sxs-lookup"><span data-stu-id="17ea0-183">Options</span></span>

| <span data-ttu-id="17ea0-184">Opção curta</span><span class="sxs-lookup"><span data-stu-id="17ea0-184">Short option</span></span>| <span data-ttu-id="17ea0-185">Opção Long</span><span class="sxs-lookup"><span data-stu-id="17ea0-185">Long option</span></span>| <span data-ttu-id="17ea0-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-186">Description</span></span>| <span data-ttu-id="17ea0-187">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="17ea0-188">-v</span><span class="sxs-lookup"><span data-stu-id="17ea0-188">-v</span></span>|<span data-ttu-id="17ea0-189">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="17ea0-189">--verbose</span></span> | <span data-ttu-id="17ea0-190">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="17ea0-190">Show verbose output.</span></span> |<span data-ttu-id="17ea0-191">dotnet openapi remover *-v*</span><span class="sxs-lookup"><span data-stu-id="17ea0-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="17ea0-192">-p</span><span class="sxs-lookup"><span data-stu-id="17ea0-192">-p</span></span>|<span data-ttu-id="17ea0-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="17ea0-193">--updateProject</span></span> | <span data-ttu-id="17ea0-194">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="17ea0-194">The project to operate on.</span></span> |<span data-ttu-id="17ea0-195">dotnet openapi remover *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="17ea0-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="17ea0-196">-h</span><span class="sxs-lookup"><span data-stu-id="17ea0-196">-h</span></span>|<span data-ttu-id="17ea0-197">--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-197">--help</span></span>|<span data-ttu-id="17ea0-198">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-198">Show help information</span></span>|<span data-ttu-id="17ea0-199">dotnet openapi remover--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="17ea0-200">Arguments</span><span class="sxs-lookup"><span data-stu-id="17ea0-200">Arguments</span></span>

|  <span data-ttu-id="17ea0-201">Argumento</span><span class="sxs-lookup"><span data-stu-id="17ea0-201">Argument</span></span>  | <span data-ttu-id="17ea0-202">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-202">Description</span></span>| <span data-ttu-id="17ea0-203">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="17ea0-204">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="17ea0-204">source-file</span></span> | <span data-ttu-id="17ea0-205">A origem para a qual remover a referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-205">The source to remove the reference to.</span></span> |<span data-ttu-id="17ea0-206">dotnet openapi remover *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="17ea0-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="17ea0-207">Atualizar</span><span class="sxs-lookup"><span data-stu-id="17ea0-207">Refresh</span></span>

<span data-ttu-id="17ea0-208">Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.</span><span class="sxs-lookup"><span data-stu-id="17ea0-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="17ea0-209">Opções</span><span class="sxs-lookup"><span data-stu-id="17ea0-209">Options</span></span>

| <span data-ttu-id="17ea0-210">Opção curta</span><span class="sxs-lookup"><span data-stu-id="17ea0-210">Short option</span></span>| <span data-ttu-id="17ea0-211">Opção Long</span><span class="sxs-lookup"><span data-stu-id="17ea0-211">Long option</span></span>| <span data-ttu-id="17ea0-212">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-212">Description</span></span> | <span data-ttu-id="17ea0-213">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="17ea0-214">-v</span><span class="sxs-lookup"><span data-stu-id="17ea0-214">-v</span></span>|<span data-ttu-id="17ea0-215">--Detalhado</span><span class="sxs-lookup"><span data-stu-id="17ea0-215">--verbose</span></span> | <span data-ttu-id="17ea0-216">Mostrar saída detalhada.</span><span class="sxs-lookup"><span data-stu-id="17ea0-216">Show verbose output.</span></span> | <span data-ttu-id="17ea0-217">dotnet openapi atualização *– v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="17ea0-218">-p</span><span class="sxs-lookup"><span data-stu-id="17ea0-218">-p</span></span>|<span data-ttu-id="17ea0-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="17ea0-219">--updateProject</span></span> | <span data-ttu-id="17ea0-220">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="17ea0-220">The project to operate on.</span></span> | <span data-ttu-id="17ea0-221">dotnet openapi atualização *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="17ea0-222">-h</span><span class="sxs-lookup"><span data-stu-id="17ea0-222">-h</span></span>|<span data-ttu-id="17ea0-223">--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-223">--help</span></span>|<span data-ttu-id="17ea0-224">Mostrar informações de ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-224">Show help information</span></span>|<span data-ttu-id="17ea0-225">dotnet openapi atualização--ajuda</span><span class="sxs-lookup"><span data-stu-id="17ea0-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="17ea0-226">Arguments</span><span class="sxs-lookup"><span data-stu-id="17ea0-226">Arguments</span></span>

|  <span data-ttu-id="17ea0-227">Argumento</span><span class="sxs-lookup"><span data-stu-id="17ea0-227">Argument</span></span>  | <span data-ttu-id="17ea0-228">Descrição</span><span class="sxs-lookup"><span data-stu-id="17ea0-228">Description</span></span> | <span data-ttu-id="17ea0-229">Exemplo</span><span class="sxs-lookup"><span data-stu-id="17ea0-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="17ea0-230">origem-URL</span><span class="sxs-lookup"><span data-stu-id="17ea0-230">source-URL</span></span> | <span data-ttu-id="17ea0-231">A URL da qual atualizar a referência.</span><span class="sxs-lookup"><span data-stu-id="17ea0-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="17ea0-232">atualização do dotnet openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="17ea0-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
