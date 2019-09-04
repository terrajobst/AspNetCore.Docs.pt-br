---
title: Testar APIs Web com o HTTP REPL
author: scottaddie
description: Saiba como usar a ferramenta global HTTP REPL do .NET Core para navegar e testar uma API Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/29/2019
uid: web-api/http-repl
ms.openlocfilehash: 7121670856da4b123b1c3e780a7952da0fb696a1
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238050"
---
# <a name="test-web-apis-with-the-http-repl"></a>Testar APIs Web com o HTTP REPL

Por [Scott Addie](https://twitter.com/Scott_Addie)

O HTTP REPL (Read-Eval-Print Loop) é:

* Uma ferramenta de linha de comando leve e multiplataforma compatível com todos os recursos compatíveis com o .NET Core.
* Usado para fazer solicitações HTTP a fim de testar as APIs Web do ASP.NET Core (e as APIs Web não pertencentes ao ASP.NET Core) e exibir os resultados.
* Capaz de testar APIs Web hospedadas em qualquer ambiente, inclusive no localhost e no Serviço de Aplicativo do Azure.

Os [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) a seguir são compatíveis:

* [DELETE](#test-http-delete-requests)
* [OBTER](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [OPTIONS](#test-http-options-requests)
* [DISTRIBUÍDO](#test-http-patch-requests)
* [POSTAR](#test-http-post-requests)
* [POSICIONE](#test-http-put-requests)

Para acompanhar, [exiba ou baixe a API Web de exemplo do ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Pré-requisitos

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Instalação

Para instalar o HTTP REPL, execute o seguinte comando:

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada pelo pacote do NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).

## <a name="usage"></a>Uso

Após a instalação da ferramenta, execute o seguinte comando para iniciar o HTTP REPL:

```console
dotnet httprepl
```

Para exibir os comandos HTTP REPL disponíveis, execute um dos seguintes comandos:

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

É exibida a saída a seguir:

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

O HTTP REPL oferece conclusão de comando. Pressionar a tecla <kbd>Tab</kbd> itera na lista de comandos que completam os caracteres ou o ponto de extremidade da API que você digitou. As seções a seguir descrevem os comandos da CLI disponíveis.

## <a name="connect-to-the-web-api"></a>Conectar-se à API Web

Conecte-se à uma API Web executando o seguinte comando:

```console
dotnet httprepl <ROOT URI>
```

`<ROOT URI>` é o URI de base para a API Web. Por exemplo:

```console
dotnet httprepl https://localhost:5001
```

Outra opção é executar o seguinte comando a qualquer momento quando o HTTP REPL estiver em execução:

```console
connect <ROOT URI>
```

Por exemplo:

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a>Apontar manualmente para o documento Swagger da API Web

O comando connect acima tentará localizar automaticamente o documento do Swagger. Se isso não for possível por algum motivo, você poderá especificar o URI do documento do Swagger para a API Web usando a opção `--swagger`:

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

Por exemplo:

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Navegar na API Web

### <a name="view-available-endpoints"></a>Exibir os pontos de extremidade disponíveis

Para listar os diferentes pontos de extremidade (controladores) no caminho atual do endereço da API Web, execute os comandos `ls` ou `dir`:

```console
https://localhot:5001/~ ls
```

O seguinte formato de saída será exibido:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

A saída anterior indica que há dois controladores disponíveis: `Fruits` e `People`. Ambos os controladores são compatíveis com operações HTTP GET e POST sem parâmetro.

Navegar em um controlador específico revela mais detalhes. Por exemplo, a saída do seguinte comando mostra que o controlador `Fruits` também é compatível com as operações HTTP GET, PUT e DELETE. Cada uma dessas operações espera um parâmetro `id` na rota:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

Outra opção é executar o comando `ui` para abrir a página da interface do usuário do Swagger da API Web em um navegador. Por exemplo:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Navegar até um ponto de extremidade

Para navegar até um ponto de extremidade diferente na API Web, execute o comando `cd`:

```console
https://localhost:5001/~ cd people
```

O caminho após o comando `cd` não diferencia maiúsculas de minúsculas. O seguinte formato de saída será exibido:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Personalizar o HTTP REPL

As [cores](#set-color-preferences) padrão do HTTP REPL podem ser personalizadas. Além disso, um [editor de texto padrão](#set-the-default-text-editor) pode ser definido. As preferências de HTTP REPL são persistidas em toda a sessão atual e serão respeitadas nas sessões futuras. Depois de modificadas, as preferências são armazenadas no seguinte arquivo:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macostabmacos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

O arquivo *.httpreplprefs* é carregado na inicialização e suas alterações não são monitoradas no tempo de execução. As modificações manuais no arquivo entram em vigor somente após a reinicialização da ferramenta.

### <a name="view-the-settings"></a>Exibir as configurações

Para exibir as configurações disponíveis, execute o comando `pref get`. Por exemplo:

```console
https://localhost:5001/~ pref get
```

O comando anterior exibe os pares chave-valor disponíveis:

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Definir preferências de cores

No momento, as cores das respostas só são compatíveis com JSON. Para personalizar a cor padrão da ferramenta HTTP REPL, localize a chave correspondente à cor a ser alterada. Para ver instruções sobre como localizar as chaves, confira a seção [Exibir as configurações](#view-the-settings). Por exemplo, altere o valor da chave `colors.json` de `Green` para `White`, desta forma:

```console
https://localhost:5001/people~ pref set colors.json White
```

Somente as [cores permitidas](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) podem ser usadas. As solicitações HTTP subsequentes exibem a saída com a nova cor.

Quando chaves de cor específicas não estão definidas, mais chaves genéricas são consideradas. Para demonstrar esse comportamento de fallback, considere o seguinte exemplo:

* Se `colors.json.name` não tiver um valor, `colors.json.string` será usado.
* Se `colors.json.string` não tiver um valor, `colors.json.literal` será usado.
* Se `colors.json.literal` não tiver um valor, `colors.json` será usado. 
* Se `colors.json` não tiver um valor, a cor de texto padrão do shell de comando (`AllowedColors.None`) será usada.

### <a name="set-indentation-size"></a>Definir o tamanho do recuo

A personalização do tamanho do recuo da resposta só é compatível com JSON. O tamanho padrão é dois espaços. Por exemplo:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`. Por exemplo, para sempre usar quatro espaços:

```console
pref set formatting.json.indentSize 4
```

As respostas subsequentes respeitarão a configuração de quatro espaços:

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Definir o editor de texto padrão

Por padrão, o HTTP REPL não tem um editor de texto configurado para uso. Para testar os métodos de API Web que exigem o corpo da solicitação HTTP, é preciso definir um editor de texto padrão. A ferramenta HTTP REPL inicia o editor de texto configurado somente para escrever o corpo da solicitação. Execute o seguinte comando para definir o editor de texto preferido como padrão:

```console
pref set editor.command.default "<EXECUTABLE>"
```

No comando anterior, `<EXECUTABLE>` é o caminho completo para o arquivo executável do editor de texto. Por exemplo, execute o seguinte comando para definir o Visual Studio Code como o editor de texto padrão:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Para iniciar o editor de texto padrão com argumentos específicos da CLI, defina a chave `editor.command.default.arguments`. Por exemplo, imagine que o Visual Studio Code é o editor de texto padrão e que você quer que o HTTP REPL sempre o abra em uma nova sessão com as extensões desabilitadas. Execute o seguinte comando:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a>Definir os caminhos de pesquisa do Swagger

Por padrão, HTTP REPL tem um conjunto de caminhos relativos que ele usa para localizar o documento do Swagger ao executar o comando `connect` sem a opção `--swagger`. Esses caminhos relativos são combinados com os caminhos raiz e base especificados no comando `connect`. Os caminhos relativos padrão são:

- *swagger.json*
- *swagger/v1/swagger.json*
- */swagger.json*
- */swagger/v1/swagger.json*

Para usar um conjunto diferente de caminhos de pesquisa em seu ambiente, defina a preferência `swagger.searchPaths`. O valor precisa ser uma lista delimitada por pipes de caminhos relativos. Por exemplo:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a>Testar solicitações HTTP GET

### <a name="synopsis"></a>Sinopse

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

As opções a seguir estão disponíveis para o comando `get`:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP GET:

1. Execute o comando `get` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/people~ get
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. Recupere um registro específico passando um parâmetro para o comando `get`:

    ```console
    https://localhost:5001/people~ get 2
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a>Testar solicitações HTTP POST

### <a name="synopsis"></a>Sinopse

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP POST:

1. Execute o comando `post` em um ponto de extremidade compatível:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON. O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP. Por exemplo:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).

1. Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Salve o arquivo *.tmp* e feche o editor de texto. A seguinte saída será exibida no shell de comando:

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a>Testar solicitações HTTP PUT

### <a name="synopsis"></a>Sinopse

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP PUT:

1. *Opcional*: Execute o comando `get` para exibir os dados antes de modificá-los:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON. O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP. Por exemplo:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).

1. Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Salve o arquivo *.tmp* e feche o editor de texto. A seguinte saída será exibida no shell de comando:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Opcional*: Emita um comando `get` para ver as modificações. Por exemplo, se você digitou "Cereja" no editor de texto, um `get` retornará o seguinte:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a>Testar solicitações HTTP DELETE

### <a name="synopsis"></a>Sinopse

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemplo

Para emitir uma solicitação HTTP DELETE:

1. *Opcional*: Execute o comando `get` para exibir os dados antes de modificá-los:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    O comando anterior exibirá o seguinte formato de saída:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Opcional*: Emita um comando `get` para ver as modificações. Neste exemplo, o `get` retornará o seguinte:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a>Testar solicitações HTTP PATCH

### <a name="synopsis"></a>Sinopse

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testar solicitações HTTP HEAD

### <a name="synopsis"></a>Sinopse

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testar solicitações HTTP OPTIONS

### <a name="synopsis"></a>Sinopse

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.

### <a name="options"></a>Opções

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Configurar cabeçalhos de solicitações HTTP

Para configurar um cabeçalho de solicitação HTTP, use uma das seguintes abordagens:

1. Configure embutido com a solicitação HTTP. Por exemplo:

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  Com a abordagem anterior, cada cabeçalho de solicitação HTTP diferente exige sua própria opção `-h`.

1. Configure antes de enviar a solicitação HTTP. Por exemplo:

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  Ao configurar o cabeçalho antes de enviar a solicitação, ele permanecerá configurado durante toda a sessão do shell de comando. Para limpar o cabeçalho, forneça um valor vazio. Por exemplo:

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a>Alternar exibição da solicitação HTTP

Por padrão, a exibição da solicitação HTTP que está sendo enviada é suprimida. É possível alterar a configuração correspondente durante a sessão do shell de comando.

### <a name="enable-request-display"></a>Habilitar a exibição da solicitação

Exiba a solicitação HTTP que está sendo enviada executando o comando `echo on`. Por exemplo:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

As solicitações HTTP subsequentes na sessão atual exibem os cabeçalhos de solicitação. Por exemplo:

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a>Desabilitar a exibição da solicitação

Suprima a exibição da solicitação HTTP que está sendo enviada executando o comando `echo off`. Por exemplo:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Executar um script

Se você executar com frequência o mesmo conjunto de comandos HTTP REPL, considere armazená-los em um arquivo de texto. Os comandos no arquivo assumem a mesma forma que os executados manualmente na linha de comando. Os comandos podem ser executados em um modo em lote usando o comando `run`. Por exemplo:

1. Crie um arquivo de texto com um conjunto de comandos delimitados por nova linha. Para ilustrar, considere um arquivo *people-script.txt* com os seguintes comandos:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Execute o comando `run`, passando o caminho do arquivo de texto. Por exemplo:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    A saída a seguir é exibida:

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a>Limpar a saída

Para remover toda a saída gravada no shell de comando pela ferramenta HTTP REPL, execute os comandos `clear` ou `cls`. Para ilustrar, imagine que o shell de comando contém a seguinte saída:

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Execute o comando a seguir para limpar a saída:

```console
https://localhost:5001/~ clear
```

Após a execução o comando anterior, o shell de comando conterá somente a seguinte saída:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Recursos adicionais

* [Solicitações da API REST](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Repositório do GitHub do HTTP REPL](https://github.com/aspnet/HttpRepl)
