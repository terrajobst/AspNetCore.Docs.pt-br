---
title: Gerenciar referências de Protobuf com dotnet-grpc
author: juntaoluo
description: Saiba mais sobre como adicionar, atualizar, remover e listar referências de Protobuf com a ferramenta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667331"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="7f032-103">Gerenciar referências de Protobuf com dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="7f032-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="7f032-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7f032-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7f032-105">`dotnet-grpc` é uma ferramenta global do .NET Core para gerenciar referências de [Protobuf ( *. proto*)](xref:grpc/basics#proto-file) em um projeto gRPC do .net.</span><span class="sxs-lookup"><span data-stu-id="7f032-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="7f032-106">A ferramenta pode ser usada para adicionar, atualizar, remover e listar referências de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="7f032-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="7f032-107">Installation</span></span>

<span data-ttu-id="7f032-108">Para instalar a [ferramenta Global `dotnet-grpc` .NET Core](/dotnet/core/tools/global-tools), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7f032-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="7f032-109">Adicionar referências</span><span class="sxs-lookup"><span data-stu-id="7f032-109">Add references</span></span>

<span data-ttu-id="7f032-110">`dotnet-grpc` pode ser usado para adicionar referências de Protobuf como `<Protobuf />` itens ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="7f032-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="7f032-111">As referências de Protobuf são usadas para gerar C# os ativos de cliente e/ou servidor.</span><span class="sxs-lookup"><span data-stu-id="7f032-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="7f032-112">A ferramenta `dotnet-grpc` pode:</span><span class="sxs-lookup"><span data-stu-id="7f032-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="7f032-113">Crie uma referência de Protobuf de arquivos locais em disco.</span><span class="sxs-lookup"><span data-stu-id="7f032-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="7f032-114">Crie uma referência de Protobuf de um arquivo remoto especificado por uma URL.</span><span class="sxs-lookup"><span data-stu-id="7f032-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="7f032-115">Verifique se as dependências do pacote gRPC corretas foram adicionadas ao projeto.</span><span class="sxs-lookup"><span data-stu-id="7f032-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="7f032-116">Por exemplo, o pacote de `Grpc.AspNetCore` é adicionado a um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="7f032-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="7f032-117">o `Grpc.AspNetCore` contém bibliotecas de cliente e de servidor gRPC e suporte a ferramentas.</span><span class="sxs-lookup"><span data-stu-id="7f032-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="7f032-118">Como alternativa, os pacotes `Grpc.Net.Client`, `Grpc.Tools` e `Google.Protobuf`, que contêm apenas as bibliotecas de cliente gRPC e o suporte de ferramentas, são adicionados a um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="7f032-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="7f032-119">Adicionar arquivo</span><span class="sxs-lookup"><span data-stu-id="7f032-119">Add file</span></span>

<span data-ttu-id="7f032-120">O comando `add-file` é usado para adicionar arquivos locais no disco como referências de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="7f032-121">Os caminhos de arquivo fornecidos:</span><span class="sxs-lookup"><span data-stu-id="7f032-121">The file paths provided:</span></span>

* <span data-ttu-id="7f032-122">Pode ser relativo ao diretório atual ou aos caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="7f032-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="7f032-123">Pode conter curingas para o [mascaramento](https://wikipedia.org/wiki/Glob_(programming))de arquivo baseado em padrão.</span><span class="sxs-lookup"><span data-stu-id="7f032-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="7f032-124">Se algum arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta `Protos` no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f032-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="7f032-125">Uso</span><span class="sxs-lookup"><span data-stu-id="7f032-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="7f032-126">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7f032-126">Arguments</span></span>

| <span data-ttu-id="7f032-127">{1&gt;Argumento&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-127">Argument</span></span> | <span data-ttu-id="7f032-128">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-128">Description</span></span> |
|-|-|
| <span data-ttu-id="7f032-129">files</span><span class="sxs-lookup"><span data-stu-id="7f032-129">files</span></span> | <span data-ttu-id="7f032-130">O arquivo protobuf faz referência a.</span><span class="sxs-lookup"><span data-stu-id="7f032-130">The protobuf file references.</span></span> <span data-ttu-id="7f032-131">Eles podem ser um caminho para glob para arquivos protobuf locais.</span><span class="sxs-lookup"><span data-stu-id="7f032-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="7f032-132">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-132">Options</span></span>

| <span data-ttu-id="7f032-133">Opção curta</span><span class="sxs-lookup"><span data-stu-id="7f032-133">Short option</span></span> | <span data-ttu-id="7f032-134">Opção Long</span><span class="sxs-lookup"><span data-stu-id="7f032-134">Long option</span></span> | <span data-ttu-id="7f032-135">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7f032-136">-p</span><span class="sxs-lookup"><span data-stu-id="7f032-136">-p</span></span> | <span data-ttu-id="7f032-137">--projeto</span><span class="sxs-lookup"><span data-stu-id="7f032-137">--project</span></span> | <span data-ttu-id="7f032-138">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="7f032-138">The path to the project file to operate on.</span></span> <span data-ttu-id="7f032-139">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="7f032-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="7f032-140">-s</span><span class="sxs-lookup"><span data-stu-id="7f032-140">-s</span></span> | <span data-ttu-id="7f032-141">--serviços</span><span class="sxs-lookup"><span data-stu-id="7f032-141">--services</span></span> | <span data-ttu-id="7f032-142">O tipo de serviços gRPCs que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="7f032-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="7f032-143">Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web.</span><span class="sxs-lookup"><span data-stu-id="7f032-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="7f032-144">Os valores aceitos são `Both`, `Client`, `Default`, `None``Server`.</span><span class="sxs-lookup"><span data-stu-id="7f032-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="7f032-145">-i</span><span class="sxs-lookup"><span data-stu-id="7f032-145">-i</span></span> | <span data-ttu-id="7f032-146">--Additional-importar-dirs</span><span class="sxs-lookup"><span data-stu-id="7f032-146">--additional-import-dirs</span></span> | <span data-ttu-id="7f032-147">Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="7f032-148">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="7f032-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="7f032-149">--acesso</span><span class="sxs-lookup"><span data-stu-id="7f032-149">--access</span></span> | <span data-ttu-id="7f032-150">O modificador de acesso a ser usado C# para as classes geradas.</span><span class="sxs-lookup"><span data-stu-id="7f032-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="7f032-151">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="7f032-151">The default value is `Public`.</span></span> <span data-ttu-id="7f032-152">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="7f032-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="7f032-153">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="7f032-153">Add URL</span></span>

<span data-ttu-id="7f032-154">O comando `add-url` é usado para adicionar um arquivo remoto especificado por uma URL de origem como referência de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="7f032-155">Um caminho de arquivo deve ser fornecido para especificar onde baixar o arquivo remoto.</span><span class="sxs-lookup"><span data-stu-id="7f032-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="7f032-156">O caminho do arquivo pode ser relativo ao diretório atual ou a um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="7f032-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="7f032-157">Se o caminho do arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta virtual `Protos` no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7f032-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="7f032-158">Uso</span><span class="sxs-lookup"><span data-stu-id="7f032-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="7f032-159">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7f032-159">Arguments</span></span>

| <span data-ttu-id="7f032-160">{1&gt;Argumento&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-160">Argument</span></span> | <span data-ttu-id="7f032-161">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-161">Description</span></span> |
|-|-|
| <span data-ttu-id="7f032-162">url</span><span class="sxs-lookup"><span data-stu-id="7f032-162">url</span></span> | <span data-ttu-id="7f032-163">A URL para um arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="7f032-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="7f032-164">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-164">Options</span></span>

| <span data-ttu-id="7f032-165">Opção curta</span><span class="sxs-lookup"><span data-stu-id="7f032-165">Short option</span></span> | <span data-ttu-id="7f032-166">Opção Long</span><span class="sxs-lookup"><span data-stu-id="7f032-166">Long option</span></span> | <span data-ttu-id="7f032-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7f032-168">-o</span><span class="sxs-lookup"><span data-stu-id="7f032-168">-o</span></span> | <span data-ttu-id="7f032-169">--output</span><span class="sxs-lookup"><span data-stu-id="7f032-169">--output</span></span> | <span data-ttu-id="7f032-170">Especifica o caminho de download para o arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="7f032-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="7f032-171">Trata-se de uma opção obrigatória.</span><span class="sxs-lookup"><span data-stu-id="7f032-171">This is a required option.</span></span>
| <span data-ttu-id="7f032-172">-p</span><span class="sxs-lookup"><span data-stu-id="7f032-172">-p</span></span> | <span data-ttu-id="7f032-173">--projeto</span><span class="sxs-lookup"><span data-stu-id="7f032-173">--project</span></span> | <span data-ttu-id="7f032-174">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="7f032-174">The path to the project file to operate on.</span></span> <span data-ttu-id="7f032-175">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="7f032-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="7f032-176">-s</span><span class="sxs-lookup"><span data-stu-id="7f032-176">-s</span></span> | <span data-ttu-id="7f032-177">--serviços</span><span class="sxs-lookup"><span data-stu-id="7f032-177">--services</span></span> | <span data-ttu-id="7f032-178">O tipo de serviços gRPCs que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="7f032-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="7f032-179">Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web.</span><span class="sxs-lookup"><span data-stu-id="7f032-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="7f032-180">Os valores aceitos são `Both`, `Client`, `Default`, `None``Server`.</span><span class="sxs-lookup"><span data-stu-id="7f032-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="7f032-181">-i</span><span class="sxs-lookup"><span data-stu-id="7f032-181">-i</span></span> | <span data-ttu-id="7f032-182">--Additional-importar-dirs</span><span class="sxs-lookup"><span data-stu-id="7f032-182">--additional-import-dirs</span></span> | <span data-ttu-id="7f032-183">Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="7f032-184">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="7f032-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="7f032-185">--acesso</span><span class="sxs-lookup"><span data-stu-id="7f032-185">--access</span></span> | <span data-ttu-id="7f032-186">O modificador de acesso a ser usado C# para as classes geradas.</span><span class="sxs-lookup"><span data-stu-id="7f032-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="7f032-187">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="7f032-187">Default value is `Public`.</span></span> <span data-ttu-id="7f032-188">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="7f032-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="7f032-189">Remover</span><span class="sxs-lookup"><span data-stu-id="7f032-189">Remove</span></span>

<span data-ttu-id="7f032-190">O comando `remove` é usado para remover referências Protobuf do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="7f032-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="7f032-191">O comando aceita argumentos de caminho e URLs de origem como argumentos.</span><span class="sxs-lookup"><span data-stu-id="7f032-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="7f032-192">A ferramenta:</span><span class="sxs-lookup"><span data-stu-id="7f032-192">The tool:</span></span>

* <span data-ttu-id="7f032-193">Remove apenas a referência Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7f032-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="7f032-194">Não exclui o arquivo *. proto* , mesmo que ele tenha sido originalmente baixado de uma URL remota.</span><span class="sxs-lookup"><span data-stu-id="7f032-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="7f032-195">Uso</span><span class="sxs-lookup"><span data-stu-id="7f032-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="7f032-196">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7f032-196">Arguments</span></span>

| <span data-ttu-id="7f032-197">{1&gt;Argumento&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-197">Argument</span></span> | <span data-ttu-id="7f032-198">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-198">Description</span></span> |
|-|-|
| <span data-ttu-id="7f032-199">referências</span><span class="sxs-lookup"><span data-stu-id="7f032-199">references</span></span> | <span data-ttu-id="7f032-200">As URLs ou caminhos de arquivo das referências de protobuf a serem removidas.</span><span class="sxs-lookup"><span data-stu-id="7f032-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="7f032-201">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-201">Options</span></span>

| <span data-ttu-id="7f032-202">Opção curta</span><span class="sxs-lookup"><span data-stu-id="7f032-202">Short option</span></span> | <span data-ttu-id="7f032-203">Opção Long</span><span class="sxs-lookup"><span data-stu-id="7f032-203">Long option</span></span> | <span data-ttu-id="7f032-204">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7f032-205">-p</span><span class="sxs-lookup"><span data-stu-id="7f032-205">-p</span></span> | <span data-ttu-id="7f032-206">--projeto</span><span class="sxs-lookup"><span data-stu-id="7f032-206">--project</span></span> | <span data-ttu-id="7f032-207">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="7f032-207">The path to the project file to operate on.</span></span> <span data-ttu-id="7f032-208">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="7f032-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="7f032-209">Atualizar</span><span class="sxs-lookup"><span data-stu-id="7f032-209">Refresh</span></span>

<span data-ttu-id="7f032-210">O comando `refresh` é usado para atualizar uma referência remota com o conteúdo mais recente da URL de origem.</span><span class="sxs-lookup"><span data-stu-id="7f032-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="7f032-211">O caminho do arquivo de download e a URL de origem podem ser usados para especificar a referência a ser atualizada.</span><span class="sxs-lookup"><span data-stu-id="7f032-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="7f032-212">Observação:</span><span class="sxs-lookup"><span data-stu-id="7f032-212">Note:</span></span>

* <span data-ttu-id="7f032-213">Os hashes do conteúdo do arquivo são comparados para determinar se o arquivo local deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="7f032-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="7f032-214">Nenhuma informação de timestamp é comparada.</span><span class="sxs-lookup"><span data-stu-id="7f032-214">No timestamp information is compared.</span></span>

<span data-ttu-id="7f032-215">A ferramenta sempre substituirá o arquivo local pelo arquivo remoto se uma atualização for necessária.</span><span class="sxs-lookup"><span data-stu-id="7f032-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="7f032-216">Uso</span><span class="sxs-lookup"><span data-stu-id="7f032-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="7f032-217">Argumentos</span><span class="sxs-lookup"><span data-stu-id="7f032-217">Arguments</span></span>

| <span data-ttu-id="7f032-218">{1&gt;Argumento&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-218">Argument</span></span> | <span data-ttu-id="7f032-219">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-219">Description</span></span> |
|-|-|
| <span data-ttu-id="7f032-220">referências</span><span class="sxs-lookup"><span data-stu-id="7f032-220">references</span></span> | <span data-ttu-id="7f032-221">As URLs ou caminhos de arquivo para referências de protobuf remotas que devem ser atualizadas.</span><span class="sxs-lookup"><span data-stu-id="7f032-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="7f032-222">Deixe esse argumento vazio para atualizar todas as referências remotas.</span><span class="sxs-lookup"><span data-stu-id="7f032-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="7f032-223">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-223">Options</span></span>

| <span data-ttu-id="7f032-224">Opção curta</span><span class="sxs-lookup"><span data-stu-id="7f032-224">Short option</span></span> | <span data-ttu-id="7f032-225">Opção Long</span><span class="sxs-lookup"><span data-stu-id="7f032-225">Long option</span></span> | <span data-ttu-id="7f032-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7f032-227">-p</span><span class="sxs-lookup"><span data-stu-id="7f032-227">-p</span></span> | <span data-ttu-id="7f032-228">--projeto</span><span class="sxs-lookup"><span data-stu-id="7f032-228">--project</span></span> | <span data-ttu-id="7f032-229">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="7f032-229">The path to the project file to operate on.</span></span> <span data-ttu-id="7f032-230">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="7f032-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="7f032-231">--execução seca</span><span class="sxs-lookup"><span data-stu-id="7f032-231">--dry-run</span></span> | <span data-ttu-id="7f032-232">Gera uma lista de arquivos que seriam atualizados sem baixar nenhum novo conteúdo.</span><span class="sxs-lookup"><span data-stu-id="7f032-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="7f032-233">Lista</span><span class="sxs-lookup"><span data-stu-id="7f032-233">List</span></span>

<span data-ttu-id="7f032-234">O comando `list` é usado para exibir todas as referências de Protobuf no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="7f032-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="7f032-235">Se todos os valores de uma coluna forem valores padrão, a coluna poderá ser omitida.</span><span class="sxs-lookup"><span data-stu-id="7f032-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="7f032-236">Uso</span><span class="sxs-lookup"><span data-stu-id="7f032-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="7f032-237">{1&gt;Opções&lt;1}</span><span class="sxs-lookup"><span data-stu-id="7f032-237">Options</span></span>

| <span data-ttu-id="7f032-238">Opção curta</span><span class="sxs-lookup"><span data-stu-id="7f032-238">Short option</span></span> | <span data-ttu-id="7f032-239">Opção Long</span><span class="sxs-lookup"><span data-stu-id="7f032-239">Long option</span></span> | <span data-ttu-id="7f032-240">Descrição</span><span class="sxs-lookup"><span data-stu-id="7f032-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7f032-241">-p</span><span class="sxs-lookup"><span data-stu-id="7f032-241">-p</span></span> | <span data-ttu-id="7f032-242">--projeto</span><span class="sxs-lookup"><span data-stu-id="7f032-242">--project</span></span> | <span data-ttu-id="7f032-243">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="7f032-243">The path to the project file to operate on.</span></span> <span data-ttu-id="7f032-244">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="7f032-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f032-245">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7f032-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
