---
title: Desenvolver ASP.NET Core aplicativos usando o OpenAPI
author: ryanbrandenburg
description: Demonstra como usar a ferramenta ' Microsoft. dotnet-openapi ' para adicionar referências a arquivos OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 079e36511b63c186ffa7726bdb1e3c3bcbda9d34
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829251"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="b6302-103">Desenvolver ASP.NET Core aplicativos usando ferramentas OpenAPI</span><span class="sxs-lookup"><span data-stu-id="b6302-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="b6302-104">Por Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="b6302-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="b6302-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) é uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools) para gerenciar referências do [openapi](https://github.com/OAI/OpenAPI-Specification) em um projeto.</span><span class="sxs-lookup"><span data-stu-id="b6302-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="b6302-106">Instalação</span><span class="sxs-lookup"><span data-stu-id="b6302-106">Installation</span></span>

<span data-ttu-id="b6302-107">Para instalar o `Microsoft.dotnet-openapi`, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="b6302-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="b6302-108">{1&gt;{2&gt;Adicionar&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="b6302-108">Add</span></span>

<span data-ttu-id="b6302-109">Adicionar uma referência de OpenAPI usando qualquer um dos comandos nessa página adiciona um elemento `<OpenApiReference />` semelhante ao seguinte para o arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="b6302-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="b6302-110">A referência anterior é necessária para que o aplicativo chame o código do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="b6302-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="b6302-111">Adicionar Arquivo</span><span class="sxs-lookup"><span data-stu-id="b6302-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="b6302-112">Opções</span><span class="sxs-lookup"><span data-stu-id="b6302-112">Options</span></span>

| <span data-ttu-id="b6302-113">Opção curta</span><span class="sxs-lookup"><span data-stu-id="b6302-113">Short option</span></span>| <span data-ttu-id="b6302-114">Opção Long</span><span class="sxs-lookup"><span data-stu-id="b6302-114">Long option</span></span>| <span data-ttu-id="b6302-115">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-115">Description</span></span> | <span data-ttu-id="b6302-116">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="b6302-117">-p</span><span class="sxs-lookup"><span data-stu-id="b6302-117">-p</span></span>|<span data-ttu-id="b6302-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6302-118">--updateProject</span></span> | <span data-ttu-id="b6302-119">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="b6302-119">The project to operate on.</span></span> |<span data-ttu-id="b6302-120">dotnet openapi Adicionar arquivo *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="b6302-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b6302-121">-c</span><span class="sxs-lookup"><span data-stu-id="b6302-121">-c</span></span>|<span data-ttu-id="b6302-122">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="b6302-122">--code-generator</span></span>| <span data-ttu-id="b6302-123">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="b6302-124">As opções são `NSwagCSharp` e `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="b6302-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="b6302-125">Se `--code-generator` não for especificado, o padrão das ferramentas será `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="b6302-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="b6302-126">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="b6302-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b6302-127">-h</span><span class="sxs-lookup"><span data-stu-id="b6302-127">-h</span></span>|<span data-ttu-id="b6302-128">--help</span><span class="sxs-lookup"><span data-stu-id="b6302-128">--help</span></span>|<span data-ttu-id="b6302-129">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-129">Show help information</span></span>|<span data-ttu-id="b6302-130">dotnet openapi Adicionar arquivo--ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b6302-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="b6302-131">Arguments</span></span>

|  <span data-ttu-id="b6302-132">Argument</span><span class="sxs-lookup"><span data-stu-id="b6302-132">Argument</span></span>  | <span data-ttu-id="b6302-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-133">Description</span></span> | <span data-ttu-id="b6302-134">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b6302-135">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="b6302-135">source-file</span></span> | <span data-ttu-id="b6302-136">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-136">The source to create a reference from.</span></span> <span data-ttu-id="b6302-137">Deve ser um arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b6302-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="b6302-138">dotnet openapi Adicionar arquivo *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="b6302-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="b6302-139">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="b6302-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="b6302-140">Opções</span><span class="sxs-lookup"><span data-stu-id="b6302-140">Options</span></span>

| <span data-ttu-id="b6302-141">Opção curta</span><span class="sxs-lookup"><span data-stu-id="b6302-141">Short option</span></span>| <span data-ttu-id="b6302-142">Opção Long</span><span class="sxs-lookup"><span data-stu-id="b6302-142">Long option</span></span>| <span data-ttu-id="b6302-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-143">Description</span></span> | <span data-ttu-id="b6302-144">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b6302-145">-p</span><span class="sxs-lookup"><span data-stu-id="b6302-145">-p</span></span>|<span data-ttu-id="b6302-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6302-146">--updateProject</span></span> | <span data-ttu-id="b6302-147">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="b6302-147">The project to operate on.</span></span> |<span data-ttu-id="b6302-148">dotnet openapi Adicionar URL *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6302-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b6302-149">-o</span><span class="sxs-lookup"><span data-stu-id="b6302-149">-o</span></span>|<span data-ttu-id="b6302-150">--arquivo de saída</span><span class="sxs-lookup"><span data-stu-id="b6302-150">--output-file</span></span> | <span data-ttu-id="b6302-151">Onde posicionar a cópia local do arquivo OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="b6302-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="b6302-152">dotnet openapi Adicionar URL `https://contoso.com/openapi.json` *--arquivo de saída myclient. JSON*</span><span class="sxs-lookup"><span data-stu-id="b6302-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="b6302-153">-c</span><span class="sxs-lookup"><span data-stu-id="b6302-153">-c</span></span>|<span data-ttu-id="b6302-154">--gerador de código</span><span class="sxs-lookup"><span data-stu-id="b6302-154">--code-generator</span></span>| <span data-ttu-id="b6302-155">O gerador de código a ser aplicado à referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="b6302-156">As opções são `NSwagCSharp` e `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="b6302-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="b6302-157">dotnet openapi Adicionar arquivo .\OpenApi.json--gerador de código</span><span class="sxs-lookup"><span data-stu-id="b6302-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="b6302-158">-h</span><span class="sxs-lookup"><span data-stu-id="b6302-158">-h</span></span>|<span data-ttu-id="b6302-159">--help</span><span class="sxs-lookup"><span data-stu-id="b6302-159">--help</span></span>|<span data-ttu-id="b6302-160">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-160">Show help information</span></span>|<span data-ttu-id="b6302-161">dotnet openapi Adicionar URL--ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="b6302-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="b6302-162">Arguments</span></span>

|  <span data-ttu-id="b6302-163">Argument</span><span class="sxs-lookup"><span data-stu-id="b6302-163">Argument</span></span>  | <span data-ttu-id="b6302-164">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-164">Description</span></span> | <span data-ttu-id="b6302-165">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="b6302-166">origem-URL</span><span class="sxs-lookup"><span data-stu-id="b6302-166">source-URL</span></span> | <span data-ttu-id="b6302-167">A origem da qual criar uma referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-167">The source to create a reference from.</span></span> <span data-ttu-id="b6302-168">Deve ser uma URL.</span><span class="sxs-lookup"><span data-stu-id="b6302-168">Must be a URL.</span></span> |<span data-ttu-id="b6302-169">dotnet openapi Adicionar URL `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6302-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="b6302-170">Remover</span><span class="sxs-lookup"><span data-stu-id="b6302-170">Remove</span></span>

<span data-ttu-id="b6302-171">Remove a referência OpenAPI correspondente ao nome do arquivo *. csproj* fornecido.</span><span class="sxs-lookup"><span data-stu-id="b6302-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="b6302-172">Quando a referência de OpenAPI for removida, os clientes não serão gerados.</span><span class="sxs-lookup"><span data-stu-id="b6302-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="b6302-173">Os arquivos local *. JSON* e *. YAML* são excluídos.</span><span class="sxs-lookup"><span data-stu-id="b6302-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="b6302-174">Opções</span><span class="sxs-lookup"><span data-stu-id="b6302-174">Options</span></span>

| <span data-ttu-id="b6302-175">Opção curta</span><span class="sxs-lookup"><span data-stu-id="b6302-175">Short option</span></span>| <span data-ttu-id="b6302-176">Opção Long</span><span class="sxs-lookup"><span data-stu-id="b6302-176">Long option</span></span>| <span data-ttu-id="b6302-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-177">Description</span></span>| <span data-ttu-id="b6302-178">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="b6302-179">-p</span><span class="sxs-lookup"><span data-stu-id="b6302-179">-p</span></span>|<span data-ttu-id="b6302-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6302-180">--updateProject</span></span> | <span data-ttu-id="b6302-181">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="b6302-181">The project to operate on.</span></span> |<span data-ttu-id="b6302-182">dotnet openapi remover *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="b6302-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="b6302-183">-h</span><span class="sxs-lookup"><span data-stu-id="b6302-183">-h</span></span>|<span data-ttu-id="b6302-184">--help</span><span class="sxs-lookup"><span data-stu-id="b6302-184">--help</span></span>|<span data-ttu-id="b6302-185">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-185">Show help information</span></span>|<span data-ttu-id="b6302-186">dotnet openapi remover--ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b6302-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="b6302-187">Arguments</span></span>

|  <span data-ttu-id="b6302-188">Argument</span><span class="sxs-lookup"><span data-stu-id="b6302-188">Argument</span></span>  | <span data-ttu-id="b6302-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-189">Description</span></span>| <span data-ttu-id="b6302-190">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="b6302-191">arquivo de origem</span><span class="sxs-lookup"><span data-stu-id="b6302-191">source-file</span></span> | <span data-ttu-id="b6302-192">A origem para a qual remover a referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-192">The source to remove the reference to.</span></span> |<span data-ttu-id="b6302-193">dotnet openapi remover *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="b6302-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="b6302-194">Atualizar</span><span class="sxs-lookup"><span data-stu-id="b6302-194">Refresh</span></span>

<span data-ttu-id="b6302-195">Atualiza a versão local de um arquivo que foi baixado usando o conteúdo mais recente da URL de download.</span><span class="sxs-lookup"><span data-stu-id="b6302-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="b6302-196">Opções</span><span class="sxs-lookup"><span data-stu-id="b6302-196">Options</span></span>

| <span data-ttu-id="b6302-197">Opção curta</span><span class="sxs-lookup"><span data-stu-id="b6302-197">Short option</span></span>| <span data-ttu-id="b6302-198">Opção Long</span><span class="sxs-lookup"><span data-stu-id="b6302-198">Long option</span></span>| <span data-ttu-id="b6302-199">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-199">Description</span></span> | <span data-ttu-id="b6302-200">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="b6302-201">-p</span><span class="sxs-lookup"><span data-stu-id="b6302-201">-p</span></span>|<span data-ttu-id="b6302-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="b6302-202">--updateProject</span></span> | <span data-ttu-id="b6302-203">O projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="b6302-203">The project to operate on.</span></span> | <span data-ttu-id="b6302-204">dotnet openapi atualização *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6302-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="b6302-205">-h</span><span class="sxs-lookup"><span data-stu-id="b6302-205">-h</span></span>|<span data-ttu-id="b6302-206">--help</span><span class="sxs-lookup"><span data-stu-id="b6302-206">--help</span></span>|<span data-ttu-id="b6302-207">Mostra informações da Ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-207">Show help information</span></span>|<span data-ttu-id="b6302-208">dotnet openapi atualização--ajuda</span><span class="sxs-lookup"><span data-stu-id="b6302-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="b6302-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="b6302-209">Arguments</span></span>

|  <span data-ttu-id="b6302-210">Argument</span><span class="sxs-lookup"><span data-stu-id="b6302-210">Argument</span></span>  | <span data-ttu-id="b6302-211">Descrição</span><span class="sxs-lookup"><span data-stu-id="b6302-211">Description</span></span> | <span data-ttu-id="b6302-212">Exemplo</span><span class="sxs-lookup"><span data-stu-id="b6302-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="b6302-213">origem-URL</span><span class="sxs-lookup"><span data-stu-id="b6302-213">source-URL</span></span> | <span data-ttu-id="b6302-214">A URL da qual atualizar a referência.</span><span class="sxs-lookup"><span data-stu-id="b6302-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="b6302-215">dotnet openapi atualizar `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="b6302-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
