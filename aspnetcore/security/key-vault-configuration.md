---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: guardrex
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/01/2019
uid: security/key-vault-configuration
ms.openlocfilehash: f16891182d274333ddc05eea401c06468e0717b1
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108088"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault provedor de configuração no ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-enfermaria](https://github.com/anurse)

Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault. Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços. Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso a dados de configuração confidenciais.
* Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.

Esse cenário está disponível para aplicativos direcionados ASP.NET Core 2,1 ou posterior.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Para usar o provedor de configuração Azure Key Vault, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

Para adotar as [identidades gerenciadas para o cenário de recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) , adicione uma referência de pacote ao pacote [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .

> [!NOTE]
> No momento da elaboração do artigo, a versão `Microsoft.Azure.Services.AppAuthentication` `1.0.3`estável mais recente do, do, oferece suporte para [identidades gerenciadas atribuídas pelo sistema](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work). O`1.2.0-preview2` suporte para *identidades gerenciadas atribuídas pelo usuário* está disponível no pacote. Este tópico demonstra o uso de identidades gerenciadas pelo sistema e o aplicativo de exemplo fornecido `1.0.3` usa a `Microsoft.Azure.Services.AppAuthentication` versão do pacote.

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em um dos dois modos determinados `#define` pela instrução na parte superior do arquivo *Program.cs* :

* `Certificate`&ndash; Demonstra o uso de uma Azure Key Vault ID do cliente e do certificado X. 509 para acessar os segredos armazenados no Azure Key Vault. Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.
* `Managed`Demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo. &ndash; Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias. A `Managed` versão do exemplo deve ser implantada no Azure. Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .

Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento de segredo no ambiente de desenvolvimento

Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets). Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.

A ferramenta Gerenciador de segredo requer `<UserSecretsId>` uma propriedade no arquivo de projeto do aplicativo. Defina o valor da propriedade`{GUID}`() para qualquer Guid exclusivo:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Os segredos são criados como pares de nome-valor. Valores hierárquicos (seções de configuração) `:` usam um (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .

O Gerenciador de segredo é usado em um shell de comando aberto na raiz do conteúdo do projeto `{SECRET NAME}` , em que é `{SECRET VALUE}` o nome e é o valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando da raiz de conteúdo do projeto para definir os segredos para o aplicativo de exemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para. `_prod` O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento de segredo no ambiente de produção com Azure Key Vault

As instruções fornecidas pelo guia [de início rápido: Defina e recupere um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo. Consulte o tópico para obter mais detalhes.

1. Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.
   * Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .
   * Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.

   Para obter mais informações, consulte [interface de linha de comando (CLI) do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).

1. Se você ainda não estiver autenticado, entre com o `az login` comando.

1. Crie um grupo de recursos com o seguinte comando, `{RESOURCE GROUP NAME}` em que é o nome do grupo de recursos para o `{LOCATION}` novo grupo de recursos e é a região do Azure (Datacenter):

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie um cofre de chaves no grupo de recursos com o seguinte comando, `{KEY VAULT NAME}` em que é o nome do novo cofre de `{LOCATION}` chaves e é a região do Azure (Datacenter):

   ```azure-cli
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie segredos no cofre de chaves como pares de nome-valor.

   Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços. Os valores hierárquicos (seções de `--` configuração) usam (dois traços) como um separador. Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves. Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.

   Os segredos a seguir são para uso com o aplicativo de exemplo. Os valores incluem um `_prod` sufixo para distingui-los `_dev` dos valores de sufixo carregados no ambiente de desenvolvimento de segredos do usuário. Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure

Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**. Para obter mais informações, consulte [sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.

O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. `#define` 509 quando a instrução na parte superior do arquivo *Program.cs* é `Certificate`definida como.

1. Crie um certificado PKCS # 12 arquivo morto ( *. pfx*). As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).
1. Instale o certificado no repositório de certificados pessoal do usuário atual. Marcar a chave como exportável é opcional. Observe a impressão digital do certificado, que é usada posteriormente neste processo.
1. Exporte o certificado PKCS # 12 Archive ( *. pfx*) como um certificado codificado em der ( *. cer*).
1. Registre o aplicativo com o Azure AD (**registros de aplicativo**).
1. Carregue o certificado codificado em DER ( *. cer*) no Azure AD:
   1. Selecione o aplicativo no Azure AD.
   1. Navegue até **certificados & segredos**.
   1. Selecione **carregar certificado** para carregar o certificado, que contém a chave pública. Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.
1. Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo *appSettings. JSON* do aplicativo.
1. Navegue até **cofres de chaves** na portal do Azure.
1. Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.
1. Selecione **políticas de acesso**.
1. Selecione **Adicionar novo**.
1. Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome. Selecione o botão **selecionar** .
1. Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .
1. Selecione **OK**.
1. Clique em **Salvar**.
1. Implante o aplicativo.

O `Certificate` aplicativo de exemplo obtém seus valores de configuração `IConfigurationRoot` de com o mesmo nome que o nome do segredo:

* Valores não hierárquicos: O valor de `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use `:` a notação (dois- `GetSection` pontos) ou o método de extensão. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O certificado X. 509 é gerenciado pelo sistema operacional. O aplicativo chama `AddAzureKeyVault` com valores fornecidos pelo arquivo *appSettings. JSON* :

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

Valores de exemplo:

* Nome do cofre de chaves:`contosovault`
* ID do aplicativo:`627e911e-43cc-61d4-992e-12db9c81b413`
* Impressão digital do certificado:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos são `_dev` carregados com o sufixo. No ambiente de produção, os valores são carregados com `_prod` o sufixo.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades gerenciadas para recursos do Azure

**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.

O aplicativo de exemplo usa identidades gerenciadas para recursos `#define` do Azure quando a instrução na parte superior do arquivo *Program.cs* é definida como `Managed`.

Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo. O aplicativo de exemplo não requer uma ID de aplicativo e uma senha (segredo do cliente) `Managed` quando definido para a versão, para que você possa ignorar essas entradas de configuração. O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .

Implante o aplicativo de exemplo no serviço Azure App.

Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado. Obtenha a ID de objeto da implantação para uso no comando a seguir. A ID de objeto é mostrada no portal do Azure no painel de **identidade** do serviço de aplicativo.

Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo `list` e `get` permissões para acessar o cofre de chaves:

```azure-cli
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.

O aplicativo de exemplo:

* Cria uma instância da `AzureServiceTokenProvider` classe sem uma cadeia de conexão. Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.
* Um novo `KeyVaultClient` é criado com o `AzureServiceTokenProvider` retorno de chamada de token de instância.
* A `KeyVaultClient` instância é usada com uma implementação padrão do `IKeyVaultSecretManager` que carrega todos os valores secretos e substitui traços duplos (`--`) por dois`:`-pontos () em nomes de chave.

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos `_dev` têm o sufixo porque são fornecidos por segredos do usuário. No ambiente de produção, os valores são carregados com `_prod` o sufixo porque são fornecidos por Azure Key Vault.

Se você receber um `Access denied` erro, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves. Confirme que você reiniciou o serviço no Azure.

## <a name="use-a-key-name-prefix"></a>Usar um prefixo de nome de chave

`AddAzureKeyVault`fornece uma sobrecarga que aceita uma implementação do `IKeyVaultSecretManager`, que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração. Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.

> [!WARNING]
> Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre. Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.

No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento `5000-AppSecret` ) para (os períodos não são permitidos em nomes de segredo do cofre de chaves). Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para `5100-AppSecret`o. Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração `AppSecret`como, eliminando a versão à medida que ela carrega o segredo.

`AddAzureKeyVault`é chamado com um personalizado `IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

A `IKeyVaultSecretManager` implementação reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

O `Load` método é chamado por um algoritmo de provedor que itera por meio dos segredos do cofre para localizar aqueles que têm o prefixo de versão. Quando um prefixo de versão é encontrado `Load`com, o algoritmo usa `GetKey` o método para retornar o nome de configuração do nome do segredo. Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.

Quando essa abordagem é implementada:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme se uma `<UserSecretsId>` Propriedade está presente no arquivo de projeto do aplicativo, em `{GUID}` que é um GUID fornecido pelo usuário:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do cofre de chaves são carregados. O segredo da cadeia `5000-AppSecret` de caracteres para é correspondido à versão do aplicativo especificada no arquivo de projeto do`5.0.0.0`aplicativo ().

1. A versão, `5000` (com o Dash), é removida do nome da chave. Em todo o aplicativo, a leitura da configuração `AppSecret` com a chave carrega o valor secreto.

1. Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado estará `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.

> [!NOTE]
> Você também pode fornecer sua própria `KeyVaultClient` implementação para `AddAzureKeyVault`o. Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.

Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois-pontos (`:`), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,... `:{n}:`). Para obter mais informações, [consulte Configuration: Associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault chaves não podem usar dois-pontos como separador. A abordagem descrita neste tópico usa traços duplos (`--`) como separador de valores hierárquicos (seções). As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos`--0--`de `--1--`chave &hellip; numérica (,, `--{n}--`).

Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON. Há dois literais de objeto definidos na `WriteTo` matriz que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:

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

A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a notação de traço duplo (`--`) e os segmentos numéricos:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregar segredos

Os segredos são armazenados em `IConfigurationRoot.Reload()` cache até que seja chamado. Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo `Reload` aplicativo até que o seja executado.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desabilitados e expirados

Os segredos desabilitados e expirados lançam um `KeyVaultClientException` em tempo de execução. Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.

## <a name="troubleshoot"></a>Solução de problemas

Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index). As seguintes condições impedirão que a configuração seja carregada:

* O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.
* O cofre de chaves não existe no Azure Key Vault.
* O aplicativo não está autorizado a acessar o cofre de chaves.
* A política de acesso não `Get` inclui `List` permissões e.
* No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.
* O aplicativo tem o nome do cofre de chaves`KeyVaultName`errado (), a ID do`AzureADApplicationId`aplicativo do Azure AD () ou a`AzureADCertThumbprint`impressão digital do certificado do Azure AD ().
* A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Documentação do Key Vault](/azure/key-vault/)
* [Como gerar e transferir chaves protegidas por HSM para Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Início Rápido: Definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET](/azure/key-vault/quick-create-net)
* [Tutorial: Como usar Azure Key Vault com a máquina virtual do Windows do Azure no .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)
