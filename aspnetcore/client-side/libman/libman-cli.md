---
title: Use a interface de linha de comando LibMan (CLI) com o ASP.NET Core
author: scottaddie
description: Saiba como usar a interface de linha de comando LibMan (CLI) em um projeto ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894753"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>Use a interface de linha de comando LibMan (CLI) com o ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie)

O [LibMan](xref:client-side/libman/index) CLI é uma ferramenta de plataforma cruzada que tem suporte em qualquer lugar, há suporte para .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Instalação

Para instalar a CLI LibMan:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Um [ferramenta Global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) for instalado a partir de [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) pacote do NuGet.

Para instalar a CLI LibMan de uma fonte específica de pacote do NuGet:

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

No exemplo anterior, uma ferramenta Global do .NET Core é instalada do computador Windows local *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* arquivo.

## <a name="usage"></a>Uso

Após a instalação bem-sucedida da CLI, o comando a seguir pode ser usado:

```console
libman
```

Para exibir a versão da CLI instalada:

```console
libman --version
```

Para exibir os comandos da CLI disponíveis:

```console
libman --help
```

O comando anterior exibe uma saída semelhante à seguinte:

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

As seções a seguir descrevem os comandos da CLI disponíveis.

## <a name="initialize-libman-in-the-project"></a>Inicializar LibMan no projeto

O `libman init` comando cria um *libman.json* arquivo se ele não existir. O arquivo é criado com o conteúdo do modelo de item padrão.

### <a name="synopsis"></a>Sinopse

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman init` comando:

* `-d|--default-destination <PATH>`

  Um caminho relativo à pasta atual. Arquivos de biblioteca são instalados nesse local, se nenhum `destination` propriedade está definida para uma biblioteca no *libman.json*. O `<PATH>` valor é gravado para o `defaultDestination` propriedade de *libman.json*.

* `-p|--default-provider <PROVIDER>`

  O provedor a ser usado se nenhum provedor é definido para uma determinada biblioteca. O `<PROVIDER>` valor é gravado para o `defaultProvider` propriedade de *libman.json*. Substitua `<PROVIDER>` com um dos seguintes valores:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para criar uma *libman.json* arquivo em um projeto ASP.NET Core:

* Navegue até a raiz do projeto.
* Execute o seguinte comando:

  ```console
  libman init
  ```

* Digite o nome do provedor padrão, ou pressione `Enter` para usar o provedor do CDNJS padrão. Os valores válidos incluem:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![comando de inicialização libman – provedor padrão](_static/libman-init-provider.png)

Um *libman.json* arquivo é adicionado à raiz do projeto com o seguinte conteúdo:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Adicione arquivos de biblioteca

O `libman install` comando baixa e instala os arquivos de biblioteca no projeto. Um *libman.json* arquivo for adicionado, se ele existir. O *libman.json* arquivo é modificado para armazenar detalhes de configuração para os arquivos de biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

O nome da biblioteca para instalar. Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0`).

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman install` comando:

* `-d|--destination <PATH>`

  O local para instalar a biblioteca. Se não for especificado, o local padrão é usado. Se nenhum `defaultDestination` propriedade é especificada em *libman.json*, essa opção é necessária.

* `--files <FILE>`

  Especifique o nome do arquivo para instalá-lo da biblioteca. Se não for especificado, todos os arquivos da biblioteca são instalados. Forneça um `--files` opção por arquivo para ser instalado. Caminhos relativos são suportados também. Por exemplo: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  O nome do provedor a ser usado para a aquisição de biblioteca. Substitua `<PROVIDER>` com um dos seguintes valores:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Se não for especificado, o `defaultProvider` propriedade na *libman.json* é usado. Se nenhum `defaultProvider` propriedade é especificada em *libman.json*, essa opção é necessária.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte *libman.json* arquivo:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Para instalar a versão do jQuery 3.2.1 *jQuery* do arquivo para o *wwwroot/scripts/jquery* pasta usando o provedor do CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

O *libman.json* arquivo semelhante ao seguinte:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Para instalar o *calendar.js* e *calendar.css* arquivos da *c:\\temp\\contosoCalendar\\*  usando o sistema de arquivos provedor:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

O seguinte aviso é exibido por dois motivos:

* O *libman.json* arquivo não contém um `defaultDestination` propriedade.
* O `libman install` comando não contém o `-d|--destination` opção.

![libman instalar comando - destino](_static/libman-install-destination.png)

Depois de aceitar o destino padrão, o *libman.json* arquivo semelhante ao seguinte:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Restaurar arquivos de biblioteca

O `libman restore` comando instala arquivos de biblioteca definidos nas *libman.json*. As seguintes regras se aplicam:

* Se nenhum *libman.json* arquivo existir na raiz do projeto, um erro será retornado.
* Se uma biblioteca Especifica um provedor, o `defaultProvider` propriedade na *libman.json* será ignorado.
* Se uma biblioteca Especifica um destino, o `defaultDestination` propriedade na *libman.json* será ignorado.

### <a name="synopsis"></a>Sinopse

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman restore` comando:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para restaurar os arquivos de biblioteca definidos em *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Excluir arquivos de biblioteca

O `libman clean` comando exclui os arquivos de biblioteca restaurados anteriormente por meio de LibMan. Pastas se tornar vazias depois dessa operação são excluídas. Os arquivos de biblioteca associados configurações na `libraries` propriedade de *libman.json* não são removidos.

### <a name="synopsis"></a>Sinopse

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman clean` comando:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Para excluir arquivos de biblioteca instalados por meio de LibMan:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Desinstalar os arquivos de biblioteca

O `libman uninstall` comando:

* Exclui todos os arquivos associados a biblioteca especificada do destino no *libman.json*.
* Remove a configuração de biblioteca associado de *libman.json*.

Ocorre um erro quando:

* Não *libman.json* arquivo existe na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome estiver instalada, solicitado para escolher um.

### <a name="synopsis"></a>Sinopse

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

O nome da biblioteca para desinstalar. Esse nome pode incluir a notação de número de versão (por exemplo, `@1.2.0`).

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman uninstall` comando:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

Considere o seguinte *libman.json* arquivo:

[!code-json[](samples/LibManSample/libman.json)]

* Para desinstalar o jQuery, qualquer um dos comandos a seguir bem-sucedida:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Para desinstalar os arquivos de Lodash instalados por meio de `filesystem` provedor:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Atualizar a versão da biblioteca

O `libman update` comando atualiza uma biblioteca instalada via LibMan à versão especificada.

Ocorre um erro quando:

* Não *libman.json* arquivo existe na raiz do projeto.
* A biblioteca especificada não existe.

Se mais de uma biblioteca com o mesmo nome estiver instalada, solicitado para escolher um.

### <a name="synopsis"></a>Sinopse

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

O nome da biblioteca para atualizar.

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman update` comando:

* `-pre`

  Obter a versão de pré-lançamento mais recente da biblioteca.

* `--to <VERSION>`

  Obtenha uma versão específica da biblioteca.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

* Para atualizar o jQuery para a versão mais recente:

  ```console
  libman update jquery
  ```

* Para atualizar o jQuery versão 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Para atualizar o jQuery para a versão de pré-lançamento mais recente:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gerenciar o cache de biblioteca

O `libman cache` comando gerencia o cache de biblioteca LibMan. O `filesystem` provedor não usa o cache de biblioteca.

### <a name="synopsis"></a>Sinopse

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Arguments

`PROVIDER`

Usado somente com o `clean` comando. Especifica para limpar o cache do provedor. Os valores válidos incluem:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Opções

As seguintes opções estão disponíveis para o `libman cache` comando:

* `--files`

  Liste os nomes de arquivos que são armazenados em cache.

* `--libraries`

  Liste os nomes de bibliotecas que são armazenados em cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemplos

* Para exibir os nomes de bibliotecas em cache por um provedor, use um dos seguintes comandos:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Uma saída semelhante à apresentada a seguir será exibida:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Para exibir os nomes dos arquivos de biblioteca em cache por provedor:

  ```console
  libman cache list --files
  ```

  Uma saída semelhante à apresentada a seguir será exibida:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Observe que a saída anterior mostra que o jQuery versões 3.2.1 e 3.3.1 são armazenados em cache no provedor de CDNJS.

* Para esvaziar o cache de biblioteca para o provedor do CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Depois de esvaziar o cache do provedor do CDNJS o `libman cache list` comando exibe o seguinte:

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Para esvaziar o cache para todos os provedores com suporte:

  ```console
  libman cache clean
  ```

  Depois de esvaziar todos os caches de provedor, o `libman cache list` comando exibe o seguinte:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Recursos adicionais

* [Instalar uma ferramenta Global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Repositório do GitHub do LibMan](https://github.com/aspnet/LibraryManager)
