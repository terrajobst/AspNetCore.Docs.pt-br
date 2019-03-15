---
title: Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 1a10c4d035510c689e3eccadc5986df0cc06b71e
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841508"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), e [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica as técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo ASP.NET Core. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Segredos de produção não devem ser usados para desenvolvimento ou teste. Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

Variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou em arquivos de configuração local. Variáveis de ambiente substituem os valores de configuração para todas as fontes de configuração especificado anteriormente.

::: moniker range="<= aspnetcore-1.1"

Configure a leitura dos valores de variáveis de ambiente chamando <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> no `Startup` construtor:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

Considere um aplicativo da web ASP.NET Core no qual **contas de usuário individuais** segurança está habilitada. Uma cadeia de caracteres de conexão de banco de dados padrão está incluída no projeto do *appSettings. JSON* arquivo com a chave `DefaultConnection`. É a cadeia de caracteres de conexão padrão para o LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação de aplicativo, o `DefaultConnection` valor de chave pode ser substituído com o valor da variável de ambiente. A variável de ambiente pode armazenar a cadeia de caracteres de conexão completa com credenciais confidenciais.

> [!WARNING]
> Variáveis de ambiente geralmente são armazenadas em texto não criptografado e sem formatação. Se o computador ou processo for comprometido, as variáveis de ambiente podem ser acessadas por pessoas não confiáveis. Medidas adicionais para evitar a divulgação de segredos do usuário podem ser necessárias.

## <a name="secret-manager"></a>Secret Manager

A ferramenta Secret Manager armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Nesse contexto, uma parte dos dados confidenciais é um segredo do aplicativo. Segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo são compartilhados em vários projetos ou associados a um projeto específico. Os segredos do aplicativo não são verificados no controle de origem.

> [!WARNING]
> A ferramenta Secret Manager não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável. Ele é apenas a fins de desenvolvimento. As chaves e valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Secret Manager

A ferramenta Secret Manager abstrai os detalhes de implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem conhecer esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração do JSON em uma pasta de perfil do usuário do sistema protegido no computador local:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Na anterior caminhos de arquivo, substitua `<user_secrets_id>` com o `UserSecretsId` valor especificado na *. csproj* arquivo.

Não escreva código que depende do local ou o formato dos dados salvos com a ferramenta Secret Manager. Esses detalhes de implementação pode ser alterado. Por exemplo, os valores secretos não são criptografados, mas pode ser no futuro.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>Instalar a ferramenta Secret Manager

A ferramenta Secret Manager é fornecido com a CLI do .NET Core no SDK do .NET Core 2.1.300 ou posterior. Para versões do SDK do .NET Core anteriores 2.1.300, a instalação da ferramenta é necessária.

> [!TIP]
> Executar `dotnet --version` em um shell de comando para ver o número de versão do SDK do .NET Core instalado.

Um aviso será exibido se a ferramenta inclui o SDK do .NET Core que está sendo usada:

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

Instalar o [secretmanager](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) pacote do NuGet em seu projeto ASP.NET Core. Por exemplo:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

Execute o seguinte comando em um shell de comando para validar a instalação da ferramenta:

```console
dotnet user-secrets -h
```

A ferramenta Secret Manager exibe um exemplo de uso e opções de ajuda de comando:

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> Você deve estar no mesmo diretório que o *. csproj* arquivo para executar as ferramentas definidas na *. csproj* do arquivo `DotNetCliToolReference` elementos.

::: moniker-end

## <a name="enable-secret-storage"></a>Habilitar o armazenamento secreto

A ferramenta Secret Manager opera em definições de configuração de específicos do projeto armazenadas no perfil do usuário.

::: moniker range=">= aspnetcore-3.0"

A ferramenta Secret Manager inclui um `init` comando no SDK do .NET Core 3.0.100 ou posterior. Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:

```console
dotnet user-secrets init
```

O comando anterior adiciona uma `UserSecretsId` elemento dentro de uma `PropertyGroup` da *. csproj* arquivo. Por padrão, o texto interno do `UserSecretsId` é um GUID. O texto interno é arbitrário, mas é exclusivo para o projeto.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para usar os segredos do usuário, defina uma `UserSecretsId` elemento dentro de uma `PropertyGroup` da *. csproj* arquivo. O texto interno do `UserSecretsId` é arbitrária, mas é exclusiva para o projeto. Os desenvolvedores geralmente geram um GUID para o `UserSecretsId`.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> No Visual Studio, clique com botão direito no projeto no Gerenciador de soluções e selecione **gerenciar segredos do usuário** no menu de contexto. Esse gesto adiciona uma `UserSecretsId` elemento, preenchido com um GUID para o *. csproj* arquivo.

## <a name="set-a-secret"></a>Defina um segredo

Defina um segredo do aplicativo consiste em uma chave e seu valor. O segredo está associado com o projeto `UserSecretsId` valor. Por exemplo, execute o seguinte comando do diretório no qual o *. csproj* arquivo existe:

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, os dois-pontos indica que `Movies` é um objeto literal com um `ServiceApiKey` propriedade.

A ferramenta Secret Manager pode ser usada de outros diretórios muito. Use o `--project` opção de fornecer o caminho do sistema de arquivos no qual o *. csproj* arquivo existe. Por exemplo:

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Estrutura JSON nivelamento no Visual Studio

Visual Studio **gerenciar segredos do usuário** gesto abre uma *Secrets* arquivo no editor de texto. Substitua o conteúdo do *Secrets* com os pares chave-valor a ser armazenado. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é mesclada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`. Por exemplo, executando `dotnet user-secrets remove "Movies:ConnectionString"` recolhe o `Movies` literal de objeto. O arquivo modificado é semelhante a:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Defina vários segredos

Um lote de segredos pode ser definido ao canalizar o JSON para o `set` comando. No exemplo a seguir, o *Input* conteúdo do arquivo será canalizado para o `set` comando.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acesse um segredo

O [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos Secret Manager.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Se seu projeto for destinado ao .NET Framework, instale o [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) pacote do NuGet.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

No ASP.NET Core 2.0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> para inicializar uma nova instância do host com os padrões pré-configurados. `CreateDefaultBuilder` chamadas <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Quando `CreateDefaultBuilder` não é chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> no `Startup` construtor. Chamar `AddUserSecrets` apenas quando o aplicativo é executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Instalar o [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) pacote do NuGet.

Adicionar a fonte de configuração de segredos do usuário com uma chamada para <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> no `Startup` construtor:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

Segredos do usuário podem ser recuperados por meio de `Configuration` API:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>Segredos do mapa para um POCO

Mapeando um literal de objeto inteiro para um POCO (uma classe .NET simple com propriedades) é útil para agregar as propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores para um POCO, use o `Configuration` da API [de associação do grafo de objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) recurso. O código a seguir associa a um personalizado `MovieSettings` POCO e acessa o `ServiceApiKey` valor da propriedade:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

O `Movies:ConnectionString` e `Movies:ServiceApiKey` segredos são mapeados para as respectivas propriedades no `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Cadeia de caracteres de substituição com segredos

Armazenar senhas em texto sem formatação é inseguro. Por exemplo, uma cadeia de caracteres de conexão de banco de dados armazenados em *appSettings. JSON* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```console
dotnet user-secrets set "DbPassword" "pass123"
```

Remover o `Password` par chave-valor da cadeia de conexão na *appSettings. JSON*. Por exemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Valor do segredo pode ser definida em um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> do objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> propriedade para concluir a cadeia de caracteres de conexão:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>Lista os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando no diretório no qual o *. csproj* arquivo existe:

```console
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, dois-pontos em nomes de chave denota a hierarquia de objetos dentro *Secrets*.

## <a name="remove-a-single-secret"></a>Remover um segredo único

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando no diretório no qual o *. csproj* arquivo existe:

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

O aplicativo *Secrets* arquivo foi modificado para remover o par chave-valor associado a `MoviesConnectionString` chave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Executando `dotnet user-secrets list` exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remover todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando no diretório no qual o *. csproj* arquivo existe:

```console
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo tem sido excluídos do *Secrets* arquivo:

```json
{}
```

Executando `dotnet user-secrets list` exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
