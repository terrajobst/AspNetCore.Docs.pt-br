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
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gerenciar referências de Protobuf com dotnet-grpc

Por [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` é uma ferramenta global do .NET Core para gerenciar referências de [Protobuf ( *. proto*)](xref:grpc/basics#proto-file) em um projeto gRPC do .net. A ferramenta pode ser usada para adicionar, atualizar, remover e listar referências de Protobuf.

## <a name="installation"></a>Instalação

Para instalar a [ferramenta Global `dotnet-grpc` .NET Core](/dotnet/core/tools/global-tools), execute o seguinte comando:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Adicionar referências

`dotnet-grpc` pode ser usado para adicionar referências de Protobuf como `<Protobuf />` itens ao arquivo *. csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

As referências de Protobuf são usadas para gerar C# os ativos de cliente e/ou servidor. A ferramenta `dotnet-grpc` pode:

* Crie uma referência de Protobuf de arquivos locais em disco.
* Crie uma referência de Protobuf de um arquivo remoto especificado por uma URL.
* Verifique se as dependências do pacote gRPC corretas foram adicionadas ao projeto.

Por exemplo, o pacote de `Grpc.AspNetCore` é adicionado a um aplicativo Web. o `Grpc.AspNetCore` contém bibliotecas de cliente e de servidor gRPC e suporte a ferramentas. Como alternativa, os pacotes `Grpc.Net.Client`, `Grpc.Tools` e `Google.Protobuf`, que contêm apenas as bibliotecas de cliente gRPC e o suporte de ferramentas, são adicionados a um aplicativo de console.

### <a name="add-file"></a>Adicionar arquivo

O comando `add-file` é usado para adicionar arquivos locais no disco como referências de Protobuf. Os caminhos de arquivo fornecidos:

* Pode ser relativo ao diretório atual ou aos caminhos absolutos.
* Pode conter curingas para o [mascaramento](https://wikipedia.org/wiki/Glob_(programming))de arquivo baseado em padrão.

Se algum arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta `Protos` no Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumentos

| {1&gt;Argumento&lt;1} | Descrição |
|-|-|
| files | O arquivo protobuf faz referência a. Eles podem ser um caminho para glob para arquivos protobuf locais. |

#### <a name="options"></a>{1&gt;Opções&lt;1}

| Opção curta | Opção Long | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo de projeto no qual operar. Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.
| -s | --serviços | O tipo de serviços gRPCs que devem ser gerados. Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web. Os valores aceitos são `Both`, `Client`, `Default`, `None``Server`.
| -i | --Additional-importar-dirs | Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf. Esta é uma lista de caminhos separados por ponto e vírgula.
| | --acesso | O modificador de acesso a ser usado C# para as classes geradas. O valor padrão é `Public`. Os valores aceitos são `Internal` e `Public`.

### <a name="add-url"></a>Adicionar URL

O comando `add-url` é usado para adicionar um arquivo remoto especificado por uma URL de origem como referência de Protobuf. Um caminho de arquivo deve ser fornecido para especificar onde baixar o arquivo remoto. O caminho do arquivo pode ser relativo ao diretório atual ou a um caminho absoluto. Se o caminho do arquivo estiver fora do diretório do projeto, um elemento `Link` será adicionado para exibir o arquivo na pasta virtual `Protos` no Visual Studio.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumentos

| {1&gt;Argumento&lt;1} | Descrição |
|-|-|
| url | A URL para um arquivo protobuf remoto. |

#### <a name="options"></a>{1&gt;Opções&lt;1}

| Opção curta | Opção Long | Descrição |
|-|-|-|
| -o | --output | Especifica o caminho de download para o arquivo protobuf remoto. Trata-se de uma opção obrigatória.
| -p | --projeto | O caminho para o arquivo de projeto no qual operar. Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.
| -s | --serviços | O tipo de serviços gRPCs que devem ser gerados. Se `Default` for especificado, `Both` será usado para projetos Web e `Client` será usado para projetos não Web. Os valores aceitos são `Both`, `Client`, `Default`, `None``Server`.
| -i | --Additional-importar-dirs | Diretórios adicionais a serem usados ao resolver importações para os arquivos protobuf. Esta é uma lista de caminhos separados por ponto e vírgula.
| | --acesso | O modificador de acesso a ser usado C# para as classes geradas. O valor padrão é `Public`. Os valores aceitos são `Internal` e `Public`.

## <a name="remove"></a>Remover

O comando `remove` é usado para remover referências Protobuf do arquivo *. csproj* . O comando aceita argumentos de caminho e URLs de origem como argumentos. A ferramenta:

* Remove apenas a referência Protobuf.
* Não exclui o arquivo *. proto* , mesmo que ele tenha sido originalmente baixado de uma URL remota.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumentos

| {1&gt;Argumento&lt;1} | Descrição |
|-|-|
| referências | As URLs ou caminhos de arquivo das referências de protobuf a serem removidas. |

### <a name="options"></a>{1&gt;Opções&lt;1}

| Opção curta | Opção Long | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo de projeto no qual operar. Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.

## <a name="refresh"></a>Atualizar

O comando `refresh` é usado para atualizar uma referência remota com o conteúdo mais recente da URL de origem. O caminho do arquivo de download e a URL de origem podem ser usados para especificar a referência a ser atualizada. Observação:

* Os hashes do conteúdo do arquivo são comparados para determinar se o arquivo local deve ser atualizado.
* Nenhuma informação de timestamp é comparada.

A ferramenta sempre substituirá o arquivo local pelo arquivo remoto se uma atualização for necessária.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumentos

| {1&gt;Argumento&lt;1} | Descrição |
|-|-|
| referências | As URLs ou caminhos de arquivo para referências de protobuf remotas que devem ser atualizadas. Deixe esse argumento vazio para atualizar todas as referências remotas. |

### <a name="options"></a>{1&gt;Opções&lt;1}

| Opção curta | Opção Long | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo de projeto no qual operar. Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.
| | --execução seca | Gera uma lista de arquivos que seriam atualizados sem baixar nenhum novo conteúdo.

## <a name="list"></a>Lista

O comando `list` é usado para exibir todas as referências de Protobuf no arquivo de projeto. Se todos os valores de uma coluna forem valores padrão, a coluna poderá ser omitida.

### <a name="usage"></a>Uso

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>{1&gt;Opções&lt;1}

| Opção curta | Opção Long | Descrição |
|-|-|-|
| -p | --projeto | O caminho para o arquivo de projeto no qual operar. Se um arquivo não for especificado, o comando pesquisará o diretório atual em busca de um.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
