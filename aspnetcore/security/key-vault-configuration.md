---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: guardrex
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: 7eb8cf5dcd6b9f112a2ef30e694b6223a7d1f2fe
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114868"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault provedor de configuração no ASP.NET Core

De [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-enfermaria](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault. Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços. Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso a dados de configuração confidenciais.
* Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em um dos dois modos determinados pela instrução `#define` na parte superior do arquivo *Program.cs* :

* `Certificate` &ndash; demonstra o uso de uma ID de cliente do Azure Key Vault e o certificado X. 509 para acessar os segredos armazenados no Azure Key Vault. Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.
* `Managed` &ndash; demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo. Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias. A versão `Managed` do exemplo deve ser implantada no Azure. Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .

Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento de segredo no ambiente de desenvolvimento

Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets). Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.

A ferramenta Gerenciador de segredo requer uma propriedade `<UserSecretsId>` no arquivo de projeto do aplicativo. Defina o valor da propriedade (`{GUID}`) para qualquer GUID exclusivo:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Os segredos são criados como pares de nome-valor. Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .

O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o sufixo `_dev` é alterado para `_prod`. O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento de segredo no ambiente de produção com Azure Key Vault

As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo. Consulte o tópico para obter mais detalhes.

1. Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.
   * Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .
   * Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.

   Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).

1. Se você ainda não estiver autenticado, entre com o comando `az login`.

1. Crie um grupo de recursos com o comando a seguir, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie um cofre de chaves no grupo de recursos com o comando a seguir, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie segredos no cofre de chaves como pares de nome-valor.

   Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços. Valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador. Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves. Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.

   Os segredos a seguir são para uso com o aplicativo de exemplo. Os valores incluem um sufixo `_prod` para distingui-los dos valores de sufixo `_dev` carregados no ambiente de desenvolvimento de segredos do usuário. Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure

Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.

O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Certificate`.

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
1. Selecione **Políticas de acesso**.
1. Selecione **Adicionar política de acesso**.
1. Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .
1. Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome. Escolha o botão **Selecionar**.
1. Selecione **OK**.
1. Clique em **Salvar**.
1. Implante o aplicativo.

O aplicativo de exemplo `Certificate` obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:

* Valores não hierárquicos: o valor de `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use a notação de `:` (dois-pontos) ou o método de extensão de `GetSection`. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O certificado X. 509 é gerenciado pelo sistema operacional. O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo arquivo *appSettings. JSON* :

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de exemplo:

* Nome do cofre de chaves: `contosovault`
* ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`
* Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos são carregados com o sufixo `_dev`. No ambiente de produção, os valores são carregados com o sufixo `_prod`.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades gerenciadas para recursos do Azure

**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.

O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Managed`.

Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo. O aplicativo de exemplo não exige uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a versão `Managed`, para que você possa ignorar essas entradas de configuração. O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .

Implante o aplicativo de exemplo no serviço Azure App.

Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado. Obtenha a ID de objeto da implantação para uso no comando a seguir. A ID de objeto é mostrada no portal do Azure no painel de **identidade** do serviço de aplicativo.

Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo as permissões de `list` e `get` para acessar o cofre de chaves:

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.

O aplicativo de exemplo:

* Cria uma instância da classe `AzureServiceTokenProvider` sem uma cadeia de conexão. Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.
* Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o retorno de chamada de token de instância de `AzureServiceTokenProvider`.
* A instância de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é usada com uma implementação padrão de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui duplos (`--`) por dois-pontos (`:`) em nomes de chave.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Nome do cofre de chaves valor de exemplo: `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos têm o sufixo `_dev` porque são fornecidos por segredos do usuário. No ambiente de produção, os valores são carregados com o sufixo `_prod` porque são fornecidos por Azure Key Vault.

Se você receber um erro de `Access denied`, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves. Confirme que você reiniciou o serviço no Azure.

Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Opções de configuração

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> pode aceitar uma <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Propriedade         | DESCRIÇÃO |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient> a ser usado para recuperar valores. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instância usada para controlar o carregamento do segredo. |
| `ReloadInterval` | `Timespan` aguardar entre as tentativas de sondagem do cofre de chaves para alterações. O valor padrão é `null` (a configuração não é recarregada). |
| `Vault`          | URI do Key Vault. |

## <a name="use-a-key-name-prefix"></a>Usar um prefixo de nome de chave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornece uma sobrecarga que aceita uma implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração. Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.

> [!WARNING]
> Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre. Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.

No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves). Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para `5100-AppSecret`. Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret`, eliminando a versão à medida que ela carrega o segredo.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> é chamado com uma <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>personalizada:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

A implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:

* `Load` carrega um segredo quando seu nome começa com o prefixo. Outros segredos não são carregados.
* `GetKey`:
  * Remove o prefixo do nome do segredo.
  * Substitui dois traços em qualquer nome pelo `KeyDelimiter`, que é o delimitador usado na configuração (geralmente um dois-pontos). Azure Key Vault não permite dois pontos em nomes secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

O método `Load` é chamado por um algoritmo de provedor que itera pelos segredos do cofre para localizar aqueles que têm o prefixo de versão. Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o método `GetKey` para retornar o nome da configuração do nome do segredo. Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.

Quando essa abordagem é implementada:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme se uma propriedade `<UserSecretsId>` está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:

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
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do cofre de chaves são carregados. O segredo da cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).

1. A versão, `5000` (com o Dash), é extraída do nome da chave. Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.

1. Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado será `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.

> [!NOTE]
> Você também pode fornecer sua própria implementação de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> para <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>. Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.

Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois pontos (`:`), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,... `:{n}:`). Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault chaves não podem usar dois-pontos como separador. A abordagem descrita neste tópico usa traços duplos (`--`) como um separador de valores hierárquicos (seções). As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica (`--0--`, `--1--`&hellip; `--{n}--`).

Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON. Há dois literais de objeto definidos na matriz de `WriteTo` que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:

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

A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a notação de travessão duplo (`--`) e os segmentos numéricos:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregar segredos

Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado. Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que `Reload` seja executado.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desabilitados e expirados

Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>. Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.

## <a name="troubleshoot"></a>Solucionar problemas

Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index). As seguintes condições impedirão que a configuração seja carregada:

* O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.
* O cofre de chaves não existe no Azure Key Vault.
* O aplicativo não está autorizado a acessar o cofre de chaves.
* A política de acesso não inclui as permissões `Get` e `List`.
* No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.
* O aplicativo tem o nome do cofre de chaves (`KeyVaultName`) errado, a ID do aplicativo do Azure AD (`AzureADApplicationId`) ou a impressão digital do certificado do Azure AD (`AzureADCertThumbprint`).
* A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.
* Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault documentação](/azure/key-vault/)
* [Como gerar e transferir chaves protegidas por HSM para Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Início rápido: definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET](/azure/key-vault/quick-create-net)
* [Tutorial: como usar o Azure Key Vault com a máquina virtual do Windows do Azure no .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault. Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços. Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:

* Controlando o acesso a dados de configuração confidenciais.
* Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pacotes

Adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .

## <a name="sample-app"></a>Aplicativo de exemplo

O aplicativo de exemplo é executado em um dos dois modos determinados pela instrução `#define` na parte superior do arquivo *Program.cs* :

* `Certificate` &ndash; demonstra o uso de uma ID de cliente do Azure Key Vault e o certificado X. 509 para acessar os segredos armazenados no Azure Key Vault. Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.
* `Managed` &ndash; demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo. Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias. A versão `Managed` do exemplo deve ser implantada no Azure. Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .

Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.

## <a name="secret-storage-in-the-development-environment"></a>Armazenamento de segredo no ambiente de desenvolvimento

Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets). Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.

A ferramenta Gerenciador de segredo requer uma propriedade `<UserSecretsId>` no arquivo de projeto do aplicativo. Defina o valor da propriedade (`{GUID}`) para qualquer GUID exclusivo:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Os segredos são criados como pares de nome-valor. Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .

O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o sufixo `_dev` é alterado para `_prod`. O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Armazenamento de segredo no ambiente de produção com Azure Key Vault

As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo. Consulte o tópico para obter mais detalhes.

1. Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):

   * Selecione **Experimente** no canto superior direito de um bloco de código. Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.
   * Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .
   * Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.

   Para obter mais informações, consulte [CLI do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).

1. Se você ainda não estiver autenticado, entre com o comando `az login`.

1. Crie um grupo de recursos com o comando a seguir, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie um cofre de chaves no grupo de recursos com o comando a seguir, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Crie segredos no cofre de chaves como pares de nome-valor.

   Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços. Valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador. Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves. Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.

   Os segredos a seguir são para uso com o aplicativo de exemplo. Os valores incluem um sufixo `_prod` para distingui-los dos valores de sufixo `_dev` carregados no ambiente de desenvolvimento de segredos do usuário. Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure

Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**. Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure. Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.

O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Certificate`.

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
1. Selecione **Políticas de acesso**.
1. Selecione **Adicionar política de acesso**.
1. Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .
1. Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome. Escolha o botão **Selecionar**.
1. Selecione **OK**.
1. Clique em **Salvar**.
1. Implante o aplicativo.

O aplicativo de exemplo `Certificate` obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:

* Valores não hierárquicos: o valor de `SecretName` é obtido com `config["SecretName"]`.
* Valores hierárquicos (seções): Use a notação de `:` (dois-pontos) ou o método de extensão de `GetSection`. Use qualquer uma dessas abordagens para obter o valor de configuração:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

O certificado X. 509 é gerenciado pelo sistema operacional. O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo arquivo *appSettings. JSON* :

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Valores de exemplo:

* Nome do cofre de chaves: `contosovault`
* ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`
* Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos são carregados com o sufixo `_dev`. No ambiente de produção, os valores são carregados com o sufixo `_prod`.

## <a name="use-managed-identities-for-azure-resources"></a>Usar identidades gerenciadas para recursos do Azure

**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.

O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Managed`.

Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo. O aplicativo de exemplo não exige uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a versão `Managed`, para que você possa ignorar essas entradas de configuração. O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .

Implante o aplicativo de exemplo no serviço Azure App.

Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado. Obtenha a ID de objeto da implantação para uso no comando a seguir. A ID de objeto é mostrada no portal do Azure no painel de **identidade** do serviço de aplicativo.

Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo as permissões de `list` e `get` para acessar o cofre de chaves:

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.

O aplicativo de exemplo:

* Cria uma instância da classe `AzureServiceTokenProvider` sem uma cadeia de conexão. Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.
* Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o retorno de chamada de token de instância de `AzureServiceTokenProvider`.
* A instância de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é usada com uma implementação padrão de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui duplos (`--`) por dois-pontos (`:`) em nomes de chave.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Nome do cofre de chaves valor de exemplo: `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados. No ambiente de desenvolvimento, os valores secretos têm o sufixo `_dev` porque são fornecidos por segredos do usuário. No ambiente de produção, os valores são carregados com o sufixo `_prod` porque são fornecidos por Azure Key Vault.

Se você receber um erro de `Access denied`, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves. Confirme que você reiniciou o serviço no Azure.

Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Usar um prefixo de nome de chave

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornece uma sobrecarga que aceita uma implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração. Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo. Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.

> [!WARNING]
> Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre. Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.

No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves). Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo. Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para `5100-AppSecret`. Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret`, eliminando a versão à medida que ela carrega o segredo.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> é chamado com uma <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>personalizada:

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

A implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:

* `Load` carrega um segredo quando seu nome começa com o prefixo. Outros segredos não são carregados.
* `GetKey`:
  * Remove o prefixo do nome do segredo.
  * Substitui dois traços em qualquer nome pelo `KeyDelimiter`, que é o delimitador usado na configuração (geralmente um dois-pontos). Azure Key Vault não permite dois pontos em nomes secretos.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

O método `Load` é chamado por um algoritmo de provedor que itera pelos segredos do cofre para localizar aqueles que têm o prefixo de versão. Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o método `GetKey` para retornar o nome da configuração do nome do segredo. Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.

Quando essa abordagem é implementada:

1. A versão do aplicativo especificada no arquivo de projeto do aplicativo. No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Confirme se uma propriedade `<UserSecretsId>` está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:

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
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Quando o aplicativo é executado, os segredos do cofre de chaves são carregados. O segredo da cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).

1. A versão, `5000` (com o Dash), é extraída do nome da chave. Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.

1. Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado será `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.

> [!NOTE]
> Você também pode fornecer sua própria implementação de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> para <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>. Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.

## <a name="bind-an-array-to-a-class"></a>Associar uma matriz a uma classe

O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.

Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois pontos (`:`), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,... `:{n}:`). Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault chaves não podem usar dois-pontos como separador. A abordagem descrita neste tópico usa traços duplos (`--`) como um separador de valores hierárquicos (seções). As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica (`--0--`, `--1--`&hellip; `--{n}--`).

Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON. Há dois literais de objeto definidos na matriz de `WriteTo` que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:

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

A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a notação de travessão duplo (`--`) e os segmentos numéricos:

| Chave | Valor |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Recarregar segredos

Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado. Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que `Reload` seja executado.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Segredos desabilitados e expirados

Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>. Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.

## <a name="troubleshoot"></a>Solucionar problemas

Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index). As seguintes condições impedirão que a configuração seja carregada:

* O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.
* O cofre de chaves não existe no Azure Key Vault.
* O aplicativo não está autorizado a acessar o cofre de chaves.
* A política de acesso não inclui as permissões `Get` e `List`.
* No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.
* O aplicativo tem o nome do cofre de chaves (`KeyVaultName`) errado, a ID do aplicativo do Azure AD (`AzureADApplicationId`) ou a impressão digital do certificado do Azure AD (`AzureADCertThumbprint`).
* A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.
* Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault documentação](/azure/key-vault/)
* [Como gerar e transferir chaves protegidas por HSM para Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Início rápido: definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET](/azure/key-vault/quick-create-net)
* [Tutorial: como usar o Azure Key Vault com a máquina virtual do Windows do Azure no .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

