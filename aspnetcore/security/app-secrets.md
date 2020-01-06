---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: 9b36ae64fbe277cd81ed22ba7b21b0a035082dbd
ms.sourcegitcommit: c815a9465e7b1bab44ce1643ec345b33e6cf1598
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75606786"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))

Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento. Nunca armazene senhas ou outros dados confidenciais no código-fonte. Os segredos de produção não devem ser usados para desenvolvimento ou teste. Os segredos não devem ser implantados com o aplicativo. Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variáveis de ambiente

As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais. Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.

::: moniker range="<= aspnetcore-1.1"

Configure a leitura de valores de variáveis de ambiente chamando <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> no construtor de `Startup`:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada. Uma cadeia de conexão de banco de dados padrão é incluída no arquivo *appSettings. JSON* do projeto com a chave `DefaultConnection`. A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha. Durante a implantação do aplicativo, o valor da chave de `DefaultConnection` pode ser substituído por um valor de variável de ambiente. A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.

> [!WARNING]
> Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado. Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis. Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Gerenciador de segredo

A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core. Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo. Os segredos do aplicativo são armazenados em um local separado da árvore do projeto. Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos. Os segredos do aplicativo não são verificados no controle do código-fonte.

> [!WARNING]
> A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável. É apenas para fins de desenvolvimento. As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.

## <a name="how-the-secret-manager-tool-works"></a>Como funciona a ferramenta Gerenciador de segredo

A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados. Você pode usar a ferramenta sem conhecer esses detalhes de implementação. Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Caminho do sistema de arquivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Caminho do sistema de arquivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo valor de `UserSecretsId` especificado no arquivo *. csproj* .

Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo. Esses detalhes de implementação podem ser alterados. Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>Instalar a ferramenta Gerenciador de segredo

A ferramenta Gerenciador de segredo é agrupada com o CLI do .NET Core no SDK do .NET Core 2.1.300 ou posterior. Para SDK do .NET Core versões anteriores ao 2.1.300, a instalação da ferramenta é necessária.

> [!TIP]
> Execute `dotnet --version` de um shell de comando para ver o número da versão do SDK do .NET Core instalado.

Um aviso será exibido se o SDK do .NET Core que está sendo usado incluir a ferramenta:

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

Instale o pacote NuGet [Microsoft. Extensions. secretomanager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) em seu projeto ASP.NET Core. Por exemplo:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

Execute o seguinte comando em um shell de comando para validar a instalação da ferramenta:

```dotnetcli
dotnet user-secrets -h
```

A ferramenta Gerenciador de segredo exibe o uso de exemplo, as opções e a ajuda de comando:

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
> Você deve estar no mesmo diretório que o arquivo *. csproj* para executar ferramentas definidas nos elementos de `DotNetCliToolReference` do arquivo *. csproj* .

::: moniker-end

## <a name="enable-secret-storage"></a>Habilitar o armazenamento secreto

A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.

::: moniker range=">= aspnetcore-3.0"

A ferramenta Gerenciador de segredo inclui um comando `init` no SDK do .NET Core 3.0.100 ou posterior. Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:

```dotnetcli
dotnet user-secrets init
```

O comando anterior adiciona um elemento `UserSecretsId` dentro de uma `PropertyGroup` do arquivo *. csproj* . Por padrão, o texto interno de `UserSecretsId` é um GUID. O texto interno é arbitrário, mas é exclusivo para o projeto.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para usar os segredos do usuário, defina um elemento `UserSecretsId` dentro de uma `PropertyGroup` do arquivo *. csproj* . O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto. Os desenvolvedores normalmente geram um GUID para o `UserSecretsId`.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto. Esse gesto adiciona um elemento `UserSecretsId`, populado com um GUID, ao arquivo *. csproj* .

## <a name="set-a-secret"></a>Definir um segredo

Defina um segredo de aplicativo que consiste em uma chave e seu valor. O segredo é associado ao valor de `UserSecretsId` do projeto. Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma propriedade `ServiceApiKey`.

A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios. Use a opção `--project` para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe. Por exemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Nivelamento de estrutura JSON no Visual Studio

O gesto **gerenciar segredos do usuário** do Visual Studio abre um arquivo *segredos. JSON* no editor de texto. Substitua o conteúdo de *Secrets. JSON* pelos pares de chave-valor a serem armazenados. Por exemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set`. Por exemplo, executar `dotnet user-secrets remove "Movies:ConnectionString"` recolhe o literal do objeto `Movies`. O arquivo modificado é semelhante ao seguinte:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Definir vários segredos

Um lote de segredos pode ser definido por meio de tubulação JSON para o comando `set`. No exemplo a seguir, o conteúdo do arquivo *Input. JSON* é canalizado para o comando `set`.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux/macOS](#tab/linux+macos)

Abra um shell de comando e execute o seguinte comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acessar um segredo

A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

Se seu projeto tiver como destino .NET Framework, instale o pacote NuGet [Microsoft. Extensions. Configuration. Usersecretos](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados. `CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no Construtor `Startup`. Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Instale o pacote NuGet [Microsoft. Extensions. Configuration. Usersecretos](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .

Adicione a fonte de configuração de segredos do usuário com uma chamada para <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no construtor de `Startup`:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

Os segredos do usuário podem ser recuperados por meio da API `Configuration`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>Mapear segredos para um POCO

O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para mapear os segredos anteriores para um POCO, use o recurso de [Associação de gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API do `Configuration`. O código a seguir é associado a uma `MovieSettings` personalizada POCO e acessa o valor da propriedade `ServiceApiKey`:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

Os segredos de `Movies:ConnectionString` e `Movies:ServiceApiKey` são mapeados para as respectivas propriedades no `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Substituição de cadeia de caracteres com segredos

O armazenamento de senhas em texto sem formatação não é seguro. Por exemplo, uma cadeia de conexão de banco de dados armazenada em *appSettings. JSON* pode incluir uma senha para o usuário especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Uma abordagem mais segura é armazenar a senha como um segredo. Por exemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Remova o `Password` par chave-valor da cadeia de conexão em *appSettings. JSON*. Por exemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

O valor do segredo pode ser definido em uma propriedade <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> do objeto de <xref:System.Data.SqlClient.SqlConnectionStringBuilder> para concluir a cadeia de conexão:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>Listar os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets list
```

A saída a seguir é exibida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *segredos. JSON*.

## <a name="remove-a-single-secret"></a>Remover um único segredo

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

O arquivo *segredos. JSON* do aplicativo foi modificado para remover o par chave-valor associado à chave de `MoviesConnectionString`:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

A execução de `dotnet user-secrets list` exibe a seguinte mensagem:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Remover todos os segredos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:

```dotnetcli
dotnet user-secrets clear
```

Todos os segredos do usuário para o aplicativo foram excluídos do arquivo *segredos. JSON* :

```json
{}
```

A execução de `dotnet user-secrets list` exibe a seguinte mensagem:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionais

* Consulte [esse problema](https://github.com/aspnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
