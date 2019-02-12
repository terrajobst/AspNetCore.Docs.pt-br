---
title: Provedor de configuração do Cofre de chaves do Azure no ASP.NET Core
author: guardrex
description: Saiba como usar o provedor de configuração do Cofre de chave do Azure para configurar um aplicativo usando pares de nome-valor carregados no tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2019
uid: security/key-vault-configuration
ms.openlocfilehash: f70389c86420d81e284ecc863ac8386f726ed2cf
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103105"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Provedor de configuração do Cofre de chaves do Azure no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-Nurse](https://github.com/anurse)

Este documento explica como usar o [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) provedor de configuração para carregar valores de configuração de aplicativo de segredos do Cofre de chaves do Azure. O Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços. Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso aos dados de configuração confidenciais.
* Atende ao requisito para FIPS 140-2 nível 2 validados módulos de segurança de Hardware (HSM) ao armazenar dados de configuração.

Esse cenário está disponível para aplicativos que usam o ASP.NET Core 2.1 ou posterior.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Para usar o provedor de configuração do Cofre de chave do Azure, adicione uma referência de pacote para o [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) pacote.

Para adotar a [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview) cenário, adicione uma referência de pacote para o [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) pacote.

> [!NOTE]
> No momento da gravação, a versão estável mais recente do `Microsoft.Azure.Services.AppAuthentication`, versão `1.0.3`, fornece suporte para [atribuído pelo sistema de identidades gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka). Suporte para *atribuída ao usuário identidades gerenciadas* está disponível no `1.0.2-preview` pacote. Este tópico demonstra o uso de identidades gerenciadas pelo sistema, e o aplicativo de exemplo fornecido usa a versão `1.0.3` do `Microsoft.Azure.Services.AppAuthentication` pacote.

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em qualquer um dos dois modos, determinados pelo `#define` instrução na parte superior dos *Program.cs* arquivo:

* `Basic` &ndash; Demonstra o uso de uma ID do aplicativo do Azure Key Vault e a senha (segredo do cliente) para acessar os segredos armazenados no cofre de chaves. Implantar o `Basic` versão de exemplo em qualquer host capaz de atender a um aplicativo ASP.NET Core. Siga as orientações na [ID do aplicativo de uso e o segredo do cliente para aplicativos do Azure não hospedados](#use-application-id-and-client-secret-for-non-azure-hosted-apps) seção.
* `Managed` &ndash; Demonstra como usar [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo ao Azure Key Vault com autenticação do Azure AD sem credenciais armazenadas no código ou na configuração do aplicativo. Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e a senha (segredo do cliente) não é necessário. O `Managed` versão deste exemplo deve ser implantado no Azure. Siga as orientações na [usar as identidades de gerenciado para recursos do Azure](#use-managed-identities-for-azure-resources) seção.

Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento secreto no ambiente de desenvolvimento

Definir segredos localmente usando o [ferramenta Secret Manager](xref:security/app-secrets). Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório local Secret Manager.

A ferramenta Secret Manager exige um `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo. Defina o valor da propriedade (`{GUID}`) para o GUID exclusivo:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Segredos são criados como pares nome-valor. Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador no [configuração do ASP.NET Core](xref:fundamentals/configuration/index) nomes de chave.

O Gerenciador de segredo é usado em um shell de comando aberto para a raiz do conteúdo do projeto, onde `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando na raiz do conteúdo do projeto para definir os segredos para o aplicativo de exemplo:

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados no Azure Key Vault na [armazenamento secreto no ambiente de produção com o Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod`. O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento secreto no ambiente de produção com o Azure Key Vault

As instruções fornecidas pelo [guia de início rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo. Consulte o tópico para obter mais detalhes.

1. Abra Azure Cloud shell usando qualquer um dos métodos a seguir na [portal do Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a cadeia de caracteres de pesquisa "Da CLI do Azure" na caixa de texto.
   * Abra o Cloud Shell no navegador com o **iniciar Cloud Shell** botão.
   * Selecione o **Cloud Shell** botão no menu no canto superior direito do portal do Azure.

   Para obter mais informações, consulte [Interface de linha de comando (CLI do Azure)](/cli/azure/) e [visão geral do Azure Cloud Shell](/azure/cloud-shell/overview).

1. Se você já não estiver autenticado, entrar com o `az login` comando.

1. Criar um grupo de recursos com o comando a seguir, onde `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (datacenter):

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Criar um cofre de chaves no grupo de recursos com o comando a seguir, onde `{KEY VAULT NAME}` é o nome para o novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie os segredos no cofre de chaves como pares nome-valor.

   Nomes de segredos de Cofre de chaves do Azure são limitados a caracteres alfanuméricos e traços. Usam valores hierárquicos (seções de configuração) `--` (dois traços) como um separador. Dois-pontos, que normalmente são usadas para delimitar uma seção de uma subchave no [configuração do ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredos do Cofre de chaves. Portanto, os dois traços são usados e trocados para dois-pontos quando os segredos são carregados para a configuração do aplicativo.

   Os segredos a seguir são para uso com o aplicativo de exemplo. Os valores incluem uma `_prod` sufixo para distingui-los da `_dev` sufixo valores carregados no ambiente de desenvolvimento de segredos do usuário. Substitua `{KEY VAULT NAME}` com o nome do Cofre de chaves que você criou na etapa anterior:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a>Use a ID do aplicativo e segredo do cliente para aplicativos não hospedados do Azure

Configurar o Azure AD, Azure Key Vault e o aplicativo para usar uma ID do aplicativo e a senha (segredo do cliente) para autenticar para um cofre de chaves **quando o aplicativo está hospedado fora do Azure**.

> [!NOTE]
> Embora haja suporte para usar uma ID do aplicativo e a senha (segredo do cliente) para aplicativos hospedados no Azure, recomendamos o uso [identidades para recursos do Azure gerenciadas](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. Identidades gerenciadas exigem armazenamento de credenciais no aplicativo ou sua configuração, portanto, ele é considerado como uma abordagem mais segura em geral.

O aplicativo de exemplo usa uma ID do aplicativo e a senha (segredo do cliente) quando o `#define` instrução na parte superior dos *Program.cs* arquivo é definido como `Basic`.

1. Registrar o aplicativo com o Azure AD e estabeleça uma senha (segredo do cliente) para a identidade do aplicativo.
1. Store o nome do Cofre de chaves, a ID do aplicativo e a senha/segredo do cliente do aplicativo *appSettings. JSON* arquivo.
1. Navegue até **cofres de chaves** no portal do Azure.
1. Selecione o Cofre de chaves que você criou na [armazenamento secreto no ambiente de produção com o Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.
1. Selecione **políticas de acesso**.
1. Selecione **adicionar novo**.
1. Selecione **selecionar entidade** e selecione o aplicativo registrado por nome. Selecione o **selecionar** botão.
1. Abra **permissões do segredo** e forneça o aplicativo com **obter** e **lista** permissões.
1. Selecione **OK**.
1. Selecione **Salvar**.
1. Implante o aplicativo.

O `Basic` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:

* Valores não são hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use `:` notação (dois-pontos) ou o `GetSection` método de extensão. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O aplicativo chama `AddAzureKeyVault` com os valores fornecidos pelo *appSettings. JSON* arquivo:

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

Valores de exemplo:

* Nome do Cofre de chaves: `contosovault`
* ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`
* Senha: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos carregar com o `_dev` sufixo. No ambiente de produção, os valores de carga com o `_prod` sufixo.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades de gerenciado para recursos do Azure

**Um aplicativo implantado no Azure** podem aproveitar [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview), que permite que o aplicativo autenticar com o Azure Key Vault usando a autenticação do Azure AD sem credenciais (ID do aplicativo e Segredo Password/Client) armazenados no aplicativo.

O aplicativo de exemplo usa identidades de gerenciado para recursos do Azure quando o `#define` instrução na parte superior dos *Program.cs* arquivo é definido como `Managed`.

Insira o nome do cofre para o aplicativo *appSettings. JSON* arquivo. O aplicativo de exemplo não exige uma ID do aplicativo e a senha (segredo do cliente) quando definido como o `Managed` versão, portanto, você pode ignorar essas entradas de configuração. O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar o Azure Key Vault usando apenas o nome do cofre armazenada em do *appSettings. JSON* arquivo.

Implante o aplicativo de exemplo no serviço de aplicativo do Azure.

Um aplicativo implantado no serviço de aplicativo do Azure é registrado automaticamente com o Azure AD quando o serviço é criado. Obter a ID de objeto de implantação para uso no comando a seguir. A ID de objeto é mostrada no portal do Azure na **identidade** painel do serviço de aplicativo.

Usando a CLI do Azure e a ID de objeto do aplicativo, forneça o aplicativo com `list` e `get` permissões para acessar o Cofre de chaves:

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando o portal do Azure, PowerShell ou CLI do Azure.

O aplicativo de exemplo:

* Cria uma instância do `AzureServiceTokenProvider` classe sem uma cadeia de caracteres de conexão. Quando uma cadeia de caracteres de conexão não for fornecida, o provedor tenta obter um token de acesso de identidades de gerenciado para recursos do Azure.
* Uma nova `KeyVaultClient` é criado com o `AzureServiceTokenProvider` token retorno de chamada de instância.
* O `KeyVaultClient` instância é usada com uma implementação padrão de `IKeyVaultSecretManager` que carrega todos os valores do segredo e substitui os dois traços (`--`) com dois-pontos (`:`) em nomes de chave.

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores do segredo têm o `_dev` sufixo porque elas são fornecidas por segredos do usuário. No ambiente de produção, os valores de carga com o `_prod` sufixo porque elas são fornecidas pelo Azure Key Vault.

Se você receber um `Access denied` erro, confirme se o aplicativo está registrado com o Azure AD e recebe acesso ao Cofre de chaves. Confirme que você tiver reiniciado o serviço no Azure.

## <a name="use-a-key-name-prefix"></a>Usar um prefixo de nome da chave

`AddAzureKeyVault` Fornece uma sobrecarga que aceita uma implementação de `IKeyVaultSecretManager`, que permite que você controle como os principais segredos do cofre são convertidas em chaves de configuração. Por exemplo, você pode implementar a interface para carregar os valores do segredo com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite que você, por exemplo, para carregar segredos de acordo com a versão do aplicativo.

> [!WARNING]
> Não usar prefixos de segredos do Cofre de chaves para colocar segredos para vários aplicativos no mesmo Cofre de chaves ou para colocar segredos ambientais (por exemplo, *development* versus *produção* segredos) no mesmo cofre. É recomendável que diferentes aplicativos e ambientes de desenvolvimento/produção usam cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.

No exemplo a seguir, um segredo é estabelecido na chave do cofre (e usar a ferramenta Secret Manager no ambiente de desenvolvimento) para `5000-AppSecret` (períodos não são permitidos em nomes de segredos do Cofre de chaves). Esse segredo representa um segredo do aplicativo para versão Version=5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado à chave de cofre (e usar a ferramenta Secret Manager) para `5100-AppSecret`. Cada versão do aplicativo carrega seu valor com controle de versão do segredo em sua configuração como `AppSecret`, remoção desativar a versão ele carrega o segredo.

`AddAzureKeyVault` é chamado com um personalizado `IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

Os valores para nome do Cofre de chaves, a ID do aplicativo e a senha (segredo do cliente) são fornecidos pela *appSettings. JSON* arquivo:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Valores de exemplo:

* Nome do Cofre de chaves: `contosovault`
* ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`
* Senha: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`

O `IKeyVaultSecretManager` implementação reage aos prefixos de segredos para carregar o segredo apropriado na configuração de versão:

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

O `Load` método é chamado por um algoritmo de provedor que itera os segredos do cofre para encontrar aqueles que têm o prefixo de versão. Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o `GetKey` método para retornar o nome da configuração do nome do segredo. Ele ignora o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregamento na configuração do aplicativo pares nome-valor.

Quando esse método é implementado:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme se um `<UserSecretsId>` propriedade estiver presente no arquivo de projeto do aplicativo, onde `{GUID}` é um GUID fornecido pelo usuário:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salvar os segredos seguir localmente com o [ferramenta Secret Manager](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Segredos são salvos no Azure Key Vault usando os seguintes comandos de CLI do Azure:

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do Cofre de chaves são carregados. O segredo de cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).

1. A versão `5000` (com o traço) é removida do nome de chave. Em todo o aplicativo, lendo a configuração com a chave `AppSecret` carrega o valor do segredo.

1. Se a versão do aplicativo é alterada no arquivo de projeto para `5.1.0.0` e o aplicativo é executado novamente, é o valor do segredo retornado `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.

> [!NOTE]
> Você também pode fornecer seus próprios `KeyVaultClient` implementação para `AddAzureKeyVault`. Compartilhando uma única instância do cliente entre o aplicativo permite que um cliente personalizado.

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a>Autenticar no Azure Key Vault com um certificado X.509

Ao desenvolver um aplicativo do .NET Framework em um ambiente que dá suporte a certificados, você pode autenticar para o Azure Key Vault com um certificado X.509. Chave privada do certificado X.509 é gerenciado pelo sistema operacional. Para obter mais informações, consulte [autenticar com um certificado em vez de um segredo do cliente](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret). Use o `AddAzureKeyVault` sobrecarga que aceita um `X509Certificate2` (`_env` no exemplo a seguir:

```csharp
var builtConfig = config.Build();

var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates
    .Find(X509FindType.FindByThumbprint, 
        config["CertificateThumbprint"], false);

config.AddAzureKeyVault(
    builtConfig["KeyVaultName"],
    builtConfig["AzureADApplicationId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(context.HostingEnvironment.ApplicationName));

store.Close();
```

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler os valores de configuração em uma matriz para a associação para uma matriz POCO.

Durante a leitura de uma fonte de configuração que permite que as chaves conter dois-pontos (`:`) separadores, um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,... `:{n}:`). Para obter mais informações, consulte [configuração: Associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Chaves do Azure Key Vault não podem usar dois-pontos como um separador. A abordagem descrita neste tópico usa double traços (`--`) como separador de valores hierárquicos (seções). As chaves de matriz são armazenadas no Azure Key Vault com double traços e segmentos de chave numéricos (`--0--`, `--1--`,... `--{n}--`).

Examine o seguinte [Serilog](https://serilog.net/) fornecida por um arquivo JSON de configuração do provedor de log. Há dois objetos literais definidos na `WriteTo` matriz que refletem os dois Serilog *coletores*, que descrevem os destinos para saída de log:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

A configuração mostrada no arquivo JSON anterior é armazenada no Azure Key Vault usando o traço duplo (`--`) segmentos notação e numérico:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregue os segredos

Segredos são armazenados em cache até `IConfigurationRoot.Reload()` é chamado. Expirado, desabilitado, e atualizados segredos no cofre de chaves não serão respeitados pelo aplicativo até `Reload` é executado.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desabilitados e expirados

Segredos desabilitados e expirados lançar um `KeyVaultClientException`. Para impedir que seu aplicativo acione, substitua o seu aplicativo ou atualizar o segredo desabilitado/expirado.

## <a name="troubleshoot"></a>Solução de problemas

Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada para o [infra-estrutura de log do ASP.NET Core](xref:fundamentals/logging/index). As seguintes condições impedirá que a configuração de carregamento:

* O aplicativo não está configurado corretamente no Azure Active Directory.
* O Cofre de chaves não existe no Azure Key Vault.
* O aplicativo não está autorizado a acessar o Cofre de chaves.
* A política de acesso não inclui `Get` e `List` permissões.
* No cofre de chaves, os dados de configuração (par nome-valor) estão nomeados incorretamente, ausente, desabilitado ou expirado.
* O aplicativo tem o nome do cofre da chave incorreta (`KeyVaultName`), Id de aplicativo do Azure AD (`AzureADApplicationId`), ou a senha (segredo do cliente) do Azure AD (`AzureADPassword`).
* A senha (segredo do cliente) do Azure AD (`AzureADPassword`) expirou.
* A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Cofre de chaves](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Documentação do Key Vault](/azure/key-vault/)
* [Como gerar e transferir protegida por HSM chaves para o Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
