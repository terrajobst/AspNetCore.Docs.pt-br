---
title: Gerenciar referências de Protobuf com dotnet-grpc
author: juntaoluo
description: Saiba mais sobre como adicionar, atualizar, remover e listar referências de Protobuf com a ferramenta global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/24/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: ebd57419be24f7f4ed9765e36cf14189be8438b1
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290057"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="95e05-103">Gerenciar referências de Protobuf com dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="95e05-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="95e05-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="95e05-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="95e05-105">`dotnet-grpc` é uma ferramenta global do .NET Core para gerenciar referências Protobuf em um projeto .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="95e05-105">`dotnet-grpc` is a .NET Core Global Tool for managing Protobuf references within a .NET gRPC project.</span></span> <span data-ttu-id="95e05-106">A ferramenta pode ser usada para adicionar, atualizar, remover e listar referências de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="95e05-107">Instalação</span><span class="sxs-lookup"><span data-stu-id="95e05-107">Installation</span></span>

<span data-ttu-id="95e05-108">Para instalar a ferramenta global do `dotnet-grpc` [.NET Core](/dotnet/core/tools/global-tools), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="95e05-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="95e05-109">Adicionar referências</span><span class="sxs-lookup"><span data-stu-id="95e05-109">Add references</span></span>

<span data-ttu-id="95e05-110">`dotnet-grpc` pode ser usado para adicionar referências Protobuf como itens `<Protobuf />` ao arquivo *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="95e05-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="..\Proto\count.proto" GrpcServices="Server" Link="Protos\count.proto" />
```

<span data-ttu-id="95e05-111">As referências de Protobuf são usadas para gerar C# os ativos de cliente e/ou servidor.</span><span class="sxs-lookup"><span data-stu-id="95e05-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="95e05-112">O `dotnet-grpc`tool pode:</span><span class="sxs-lookup"><span data-stu-id="95e05-112">The `dotnet-grpc`tool can:</span></span>

* <span data-ttu-id="95e05-113">Crie uma referência de Protobuf de arquivos locais em disco.</span><span class="sxs-lookup"><span data-stu-id="95e05-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="95e05-114">Crie uma referência de Protobuf de um arquivo remoto especificado por uma URL.</span><span class="sxs-lookup"><span data-stu-id="95e05-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="95e05-115">Verifique se as dependências do pacote gRPC corretas foram adicionadas ao projeto.</span><span class="sxs-lookup"><span data-stu-id="95e05-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="95e05-116">Por exemplo, o pacote `Grpc.AspNetCore` é adicionado a um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="95e05-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="95e05-117">`Grpc.AspNetCore` contém bibliotecas de servidor e cliente gRPC e suporte a ferramentas.</span><span class="sxs-lookup"><span data-stu-id="95e05-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="95e05-118">Como alternativa, os pacotes `Grpc.Net.Client`, `Grpc.Tools` e `Google.Protobuf`, que contêm apenas as bibliotecas de cliente gRPC e o suporte de ferramentas, são adicionados a um aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="95e05-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="95e05-119">Adicionar arquivo</span><span class="sxs-lookup"><span data-stu-id="95e05-119">Add file</span></span>

<span data-ttu-id="95e05-120">O comando `add-file` é usado para adicionar arquivos locais no disco como referências de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="95e05-121">Os caminhos de arquivo fornecidos:</span><span class="sxs-lookup"><span data-stu-id="95e05-121">The file paths provided:</span></span>

* <span data-ttu-id="95e05-122">Pode ser relativo ao diretório atual ou aos caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="95e05-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="95e05-123">Pode conter curingas para o [mascaramento](https://wikipedia.org/wiki/Glob_(programming))de arquivo baseado em padrão.</span><span class="sxs-lookup"><span data-stu-id="95e05-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="95e05-124">Se algum arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta `Protos` no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="95e05-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="95e05-125">Uso</span><span class="sxs-lookup"><span data-stu-id="95e05-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="95e05-126">Argumentos</span><span class="sxs-lookup"><span data-stu-id="95e05-126">Arguments</span></span>

| <span data-ttu-id="95e05-127">Argumento</span><span class="sxs-lookup"><span data-stu-id="95e05-127">Argument</span></span> | <span data-ttu-id="95e05-128">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-128">Description</span></span> |
|-|-|
| <span data-ttu-id="95e05-129">files</span><span class="sxs-lookup"><span data-stu-id="95e05-129">files</span></span> | <span data-ttu-id="95e05-130">O arquivo protobuf faz referência a.</span><span class="sxs-lookup"><span data-stu-id="95e05-130">The protobuf file references.</span></span> <span data-ttu-id="95e05-131">Eles podem ser um caminho para glob para arquivos protobuf locais.</span><span class="sxs-lookup"><span data-stu-id="95e05-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="95e05-132">Opções</span><span class="sxs-lookup"><span data-stu-id="95e05-132">Options</span></span>

| <span data-ttu-id="95e05-133">Opção curta</span><span class="sxs-lookup"><span data-stu-id="95e05-133">Short option</span></span> | <span data-ttu-id="95e05-134">Opção Long</span><span class="sxs-lookup"><span data-stu-id="95e05-134">Long option</span></span> | <span data-ttu-id="95e05-135">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="95e05-136">-p</span><span class="sxs-lookup"><span data-stu-id="95e05-136">-p</span></span> | <span data-ttu-id="95e05-137">--projeto</span><span class="sxs-lookup"><span data-stu-id="95e05-137">--project</span></span> | <span data-ttu-id="95e05-138">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="95e05-138">The path to the project file to operate on.</span></span> <span data-ttu-id="95e05-139">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="95e05-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="95e05-140">-s</span><span class="sxs-lookup"><span data-stu-id="95e05-140">-s</span></span> | <span data-ttu-id="95e05-141">--serviços</span><span class="sxs-lookup"><span data-stu-id="95e05-141">--services</span></span> | <span data-ttu-id="95e05-142">O tipo de serviços gRPCs que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="95e05-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="95e05-143">Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web.</span><span class="sxs-lookup"><span data-stu-id="95e05-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="95e05-144">Os valores aceitos são `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="95e05-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="95e05-145">-i</span><span class="sxs-lookup"><span data-stu-id="95e05-145">-i</span></span> | <span data-ttu-id="95e05-146">--Additional-importar-dirs</span><span class="sxs-lookup"><span data-stu-id="95e05-146">--additional-import-dirs</span></span> | <span data-ttu-id="95e05-147">Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="95e05-148">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="95e05-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="95e05-149">--acesso</span><span class="sxs-lookup"><span data-stu-id="95e05-149">--access</span></span> | <span data-ttu-id="95e05-150">O modificador de acesso a ser usado C# para as classes geradas.</span><span class="sxs-lookup"><span data-stu-id="95e05-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="95e05-151">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="95e05-151">The default value is `Public`.</span></span> <span data-ttu-id="95e05-152">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="95e05-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="95e05-153">Adicionar URL</span><span class="sxs-lookup"><span data-stu-id="95e05-153">Add URL</span></span>

<span data-ttu-id="95e05-154">O comando `add-url` é usado para adicionar um arquivo remoto especificado por uma URL de origem como referência de Protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="95e05-155">Um caminho de arquivo deve ser fornecido para especificar onde baixar o arquivo remoto.</span><span class="sxs-lookup"><span data-stu-id="95e05-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="95e05-156">O caminho do arquivo pode ser relativo ao diretório atual ou a um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="95e05-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="95e05-157">Se o caminho do arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta virtual `Protos` no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="95e05-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="95e05-158">Uso</span><span class="sxs-lookup"><span data-stu-id="95e05-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="95e05-159">Argumentos</span><span class="sxs-lookup"><span data-stu-id="95e05-159">Arguments</span></span>

| <span data-ttu-id="95e05-160">Argumento</span><span class="sxs-lookup"><span data-stu-id="95e05-160">Argument</span></span> | <span data-ttu-id="95e05-161">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-161">Description</span></span> |
|-|-|
| <span data-ttu-id="95e05-162">url</span><span class="sxs-lookup"><span data-stu-id="95e05-162">url</span></span> | <span data-ttu-id="95e05-163">A URL para um arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="95e05-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="95e05-164">Opções</span><span class="sxs-lookup"><span data-stu-id="95e05-164">Options</span></span>

| <span data-ttu-id="95e05-165">Opção curta</span><span class="sxs-lookup"><span data-stu-id="95e05-165">Short option</span></span> | <span data-ttu-id="95e05-166">Opção Long</span><span class="sxs-lookup"><span data-stu-id="95e05-166">Long option</span></span> | <span data-ttu-id="95e05-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="95e05-168">-o</span><span class="sxs-lookup"><span data-stu-id="95e05-168">-o</span></span> | <span data-ttu-id="95e05-169">--saída</span><span class="sxs-lookup"><span data-stu-id="95e05-169">--output</span></span> | <span data-ttu-id="95e05-170">Especifica o caminho de download para o arquivo protobuf remoto.</span><span class="sxs-lookup"><span data-stu-id="95e05-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="95e05-171">Trata-se de uma opção obrigatória.</span><span class="sxs-lookup"><span data-stu-id="95e05-171">This is a required option.</span></span>
| <span data-ttu-id="95e05-172">-p</span><span class="sxs-lookup"><span data-stu-id="95e05-172">-p</span></span> | <span data-ttu-id="95e05-173">--projeto</span><span class="sxs-lookup"><span data-stu-id="95e05-173">--project</span></span> | <span data-ttu-id="95e05-174">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="95e05-174">The path to the project file to operate on.</span></span> <span data-ttu-id="95e05-175">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="95e05-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="95e05-176">-s</span><span class="sxs-lookup"><span data-stu-id="95e05-176">-s</span></span> | <span data-ttu-id="95e05-177">--serviços</span><span class="sxs-lookup"><span data-stu-id="95e05-177">--services</span></span> | <span data-ttu-id="95e05-178">O tipo de serviços gRPCs que devem ser gerados.</span><span class="sxs-lookup"><span data-stu-id="95e05-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="95e05-179">Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web.</span><span class="sxs-lookup"><span data-stu-id="95e05-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="95e05-180">Os valores aceitos são `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="95e05-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="95e05-181">-i</span><span class="sxs-lookup"><span data-stu-id="95e05-181">-i</span></span> | <span data-ttu-id="95e05-182">--Additional-importar-dirs</span><span class="sxs-lookup"><span data-stu-id="95e05-182">--additional-import-dirs</span></span> | <span data-ttu-id="95e05-183">Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="95e05-184">Esta é uma lista de caminhos separados por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="95e05-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="95e05-185">--acesso</span><span class="sxs-lookup"><span data-stu-id="95e05-185">--access</span></span> | <span data-ttu-id="95e05-186">O modificador de acesso a ser usado C# para as classes geradas.</span><span class="sxs-lookup"><span data-stu-id="95e05-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="95e05-187">O valor padrão é `Public`.</span><span class="sxs-lookup"><span data-stu-id="95e05-187">Default value is `Public`.</span></span> <span data-ttu-id="95e05-188">Os valores aceitos são `Internal` e `Public`.</span><span class="sxs-lookup"><span data-stu-id="95e05-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="95e05-189">Remover</span><span class="sxs-lookup"><span data-stu-id="95e05-189">Remove</span></span>

<span data-ttu-id="95e05-190">O comando `remove` é usado para remover referências Protobuf do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="95e05-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="95e05-191">O comando aceita argumentos de caminho e URLs de origem como argumentos.</span><span class="sxs-lookup"><span data-stu-id="95e05-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="95e05-192">A ferramenta:</span><span class="sxs-lookup"><span data-stu-id="95e05-192">The tool:</span></span>

* <span data-ttu-id="95e05-193">Remove apenas a referência Protobuf.</span><span class="sxs-lookup"><span data-stu-id="95e05-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="95e05-194">Não exclui o arquivo *. proto* , mesmo que ele tenha sido originalmente baixado de uma URL remota.</span><span class="sxs-lookup"><span data-stu-id="95e05-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="95e05-195">Uso</span><span class="sxs-lookup"><span data-stu-id="95e05-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="95e05-196">Argumentos</span><span class="sxs-lookup"><span data-stu-id="95e05-196">Arguments</span></span>

| <span data-ttu-id="95e05-197">Argumento</span><span class="sxs-lookup"><span data-stu-id="95e05-197">Argument</span></span> | <span data-ttu-id="95e05-198">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-198">Description</span></span> |
|-|-|
| <span data-ttu-id="95e05-199">referências</span><span class="sxs-lookup"><span data-stu-id="95e05-199">references</span></span> | <span data-ttu-id="95e05-200">As URLs ou caminhos de arquivo das referências de protobuf a serem removidas.</span><span class="sxs-lookup"><span data-stu-id="95e05-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="95e05-201">Opções</span><span class="sxs-lookup"><span data-stu-id="95e05-201">Options</span></span>

| <span data-ttu-id="95e05-202">Opção curta</span><span class="sxs-lookup"><span data-stu-id="95e05-202">Short option</span></span> | <span data-ttu-id="95e05-203">Opção Long</span><span class="sxs-lookup"><span data-stu-id="95e05-203">Long option</span></span> | <span data-ttu-id="95e05-204">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="95e05-205">-p</span><span class="sxs-lookup"><span data-stu-id="95e05-205">-p</span></span> | <span data-ttu-id="95e05-206">--projeto</span><span class="sxs-lookup"><span data-stu-id="95e05-206">--project</span></span> | <span data-ttu-id="95e05-207">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="95e05-207">The path to the project file to operate on.</span></span> <span data-ttu-id="95e05-208">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="95e05-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="95e05-209">Atualizar</span><span class="sxs-lookup"><span data-stu-id="95e05-209">Refresh</span></span>

<span data-ttu-id="95e05-210">O comando `refresh` é usado para atualizar uma referência remota com o conteúdo mais recente da URL de origem.</span><span class="sxs-lookup"><span data-stu-id="95e05-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="95e05-211">O caminho do arquivo de download e a URL de origem podem ser usados para especificar a referência a ser atualizada.</span><span class="sxs-lookup"><span data-stu-id="95e05-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="95e05-212">Observação:</span><span class="sxs-lookup"><span data-stu-id="95e05-212">Note:</span></span>

* <span data-ttu-id="95e05-213">Os hashes do conteúdo do arquivo são comparados para determinar se o arquivo local deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="95e05-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="95e05-214">Nenhuma informação de timestamp é comparada.</span><span class="sxs-lookup"><span data-stu-id="95e05-214">No timestamp information is compared.</span></span>

<span data-ttu-id="95e05-215">A ferramenta sempre substituirá o arquivo local pelo arquivo remoto se uma atualização for necessária.</span><span class="sxs-lookup"><span data-stu-id="95e05-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="95e05-216">Uso</span><span class="sxs-lookup"><span data-stu-id="95e05-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="95e05-217">Argumentos</span><span class="sxs-lookup"><span data-stu-id="95e05-217">Arguments</span></span>

| <span data-ttu-id="95e05-218">Argumento</span><span class="sxs-lookup"><span data-stu-id="95e05-218">Argument</span></span> | <span data-ttu-id="95e05-219">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-219">Description</span></span> |
|-|-|
| <span data-ttu-id="95e05-220">referências</span><span class="sxs-lookup"><span data-stu-id="95e05-220">references</span></span> | <span data-ttu-id="95e05-221">As URLs ou caminhos de arquivo para referências de protobuf remotas que devem ser atualizadas.</span><span class="sxs-lookup"><span data-stu-id="95e05-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="95e05-222">Deixe esse argumento vazio para atualizar todas as referências remotas.</span><span class="sxs-lookup"><span data-stu-id="95e05-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="95e05-223">Opções</span><span class="sxs-lookup"><span data-stu-id="95e05-223">Options</span></span>

| <span data-ttu-id="95e05-224">Opção curta</span><span class="sxs-lookup"><span data-stu-id="95e05-224">Short option</span></span> | <span data-ttu-id="95e05-225">Opção Long</span><span class="sxs-lookup"><span data-stu-id="95e05-225">Long option</span></span> | <span data-ttu-id="95e05-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="95e05-227">-p</span><span class="sxs-lookup"><span data-stu-id="95e05-227">-p</span></span> | <span data-ttu-id="95e05-228">--projeto</span><span class="sxs-lookup"><span data-stu-id="95e05-228">--project</span></span> | <span data-ttu-id="95e05-229">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="95e05-229">The path to the project file to operate on.</span></span> <span data-ttu-id="95e05-230">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="95e05-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="95e05-231">--execução seca</span><span class="sxs-lookup"><span data-stu-id="95e05-231">--dry-run</span></span> | <span data-ttu-id="95e05-232">Gera uma lista de arquivos que seriam atualizados sem baixar nenhum novo conteúdo.</span><span class="sxs-lookup"><span data-stu-id="95e05-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="95e05-233">List</span><span class="sxs-lookup"><span data-stu-id="95e05-233">List</span></span>

<span data-ttu-id="95e05-234">O comando `list` é usado para exibir todas as referências de Protobuf no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="95e05-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="95e05-235">Se todos os valores de uma coluna forem valores padrão, a coluna poderá ser omitida.</span><span class="sxs-lookup"><span data-stu-id="95e05-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="95e05-236">Uso</span><span class="sxs-lookup"><span data-stu-id="95e05-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="95e05-237">Opções</span><span class="sxs-lookup"><span data-stu-id="95e05-237">Options</span></span>

| <span data-ttu-id="95e05-238">Opção curta</span><span class="sxs-lookup"><span data-stu-id="95e05-238">Short option</span></span> | <span data-ttu-id="95e05-239">Opção Long</span><span class="sxs-lookup"><span data-stu-id="95e05-239">Long option</span></span> | <span data-ttu-id="95e05-240">Descrição</span><span class="sxs-lookup"><span data-stu-id="95e05-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="95e05-241">-p</span><span class="sxs-lookup"><span data-stu-id="95e05-241">-p</span></span> | <span data-ttu-id="95e05-242">--projeto</span><span class="sxs-lookup"><span data-stu-id="95e05-242">--project</span></span> | <span data-ttu-id="95e05-243">O caminho para o arquivo de projeto no qual operar.</span><span class="sxs-lookup"><span data-stu-id="95e05-243">The path to the project file to operate on.</span></span> <span data-ttu-id="95e05-244">Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.</span><span class="sxs-lookup"><span data-stu-id="95e05-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95e05-245">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="95e05-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
