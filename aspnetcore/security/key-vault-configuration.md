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
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="0f4a8-103">Provedor de configuração do Cofre de chaves do Azure no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f4a8-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="0f4a8-104">Por [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="0f4a8-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="0f4a8-105">Este documento explica como usar o [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) provedor de configuração para carregar valores de configuração de aplicativo de segredos do Cofre de chaves do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="0f4a8-106">O Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="0f4a8-107">Cenários comuns para usar o Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="0f4a8-108">Controlando o acesso aos dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="0f4a8-109">Atende ao requisito para FIPS 140-2 nível 2 validados módulos de segurança de Hardware (HSM) ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="0f4a8-110">Esse cenário está disponível para aplicativos que usam o ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="0f4a8-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f4a8-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="0f4a8-112">Pacotes</span><span class="sxs-lookup"><span data-stu-id="0f4a8-112">Packages</span></span>

<span data-ttu-id="0f4a8-113">Para usar o provedor de configuração do Cofre de chave do Azure, adicione uma referência de pacote para o [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) pacote.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="0f4a8-114">Para adotar a [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview) cenário, adicione uma referência de pacote para o [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) pacote.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="0f4a8-115">No momento da gravação, a versão estável mais recente do `Microsoft.Azure.Services.AppAuthentication`, versão `1.0.3`, fornece suporte para [atribuído pelo sistema de identidades gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka).</span></span> <span data-ttu-id="0f4a8-116">Suporte para *atribuída ao usuário identidades gerenciadas* está disponível no `1.0.2-preview` pacote.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-116">Support for *user-assigned managed identities* is available in the `1.0.2-preview` package.</span></span> <span data-ttu-id="0f4a8-117">Este tópico demonstra o uso de identidades gerenciadas pelo sistema, e o aplicativo de exemplo fornecido usa a versão `1.0.3` do `Microsoft.Azure.Services.AppAuthentication` pacote.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="0f4a8-118">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="0f4a8-118">Sample app</span></span>

<span data-ttu-id="0f4a8-119">O aplicativo de exemplo é executado em qualquer um dos dois modos, determinados pelo `#define` instrução na parte superior dos *Program.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="0f4a8-120">`Basic` &ndash; Demonstra o uso de uma ID do aplicativo do Azure Key Vault e a senha (segredo do cliente) para acessar os segredos armazenados no cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-120">`Basic` &ndash; Demonstrates the use of an Azure Key Vault Application ID and Password (Client Secret) to access secrets stored in the key vault.</span></span> <span data-ttu-id="0f4a8-121">Implantar o `Basic` versão de exemplo em qualquer host capaz de atender a um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-121">Deploy the `Basic` version of the sample to any host capable of serving an ASP.NET Core app.</span></span> <span data-ttu-id="0f4a8-122">Siga as orientações na [ID do aplicativo de uso e o segredo do cliente para aplicativos do Azure não hospedados](#use-application-id-and-client-secret-for-non-azure-hosted-apps) seção.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-122">Follow the guidance in the [Use Application ID and Client Secret for non-Azure-hosted apps](#use-application-id-and-client-secret-for-non-azure-hosted-apps) section.</span></span>
* <span data-ttu-id="0f4a8-123">`Managed` &ndash; Demonstra como usar [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo ao Azure Key Vault com autenticação do Azure AD sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-123">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="0f4a8-124">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e a senha (segredo do cliente) não é necessário.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-124">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="0f4a8-125">O `Managed` versão deste exemplo deve ser implantado no Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-125">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="0f4a8-126">Siga as orientações na [usar as identidades de gerenciado para recursos do Azure](#use-managed-identities-for-azure-resources) seção.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-126">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="0f4a8-127">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-127">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="0f4a8-128">Armazenamento secreto no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="0f4a8-128">Secret storage in the Development environment</span></span>

<span data-ttu-id="0f4a8-129">Definir segredos localmente usando o [ferramenta Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-129">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0f4a8-130">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório local Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-130">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="0f4a8-131">A ferramenta Secret Manager exige um `<UserSecretsId>` propriedade no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-131">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="0f4a8-132">Defina o valor da propriedade (`{GUID}`) para o GUID exclusivo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-132">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="0f4a8-133">Segredos são criados como pares nome-valor.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-133">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="0f4a8-134">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador no [configuração do ASP.NET Core](xref:fundamentals/configuration/index) nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-134">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="0f4a8-135">O Gerenciador de segredo é usado em um shell de comando aberto para a raiz do conteúdo do projeto, onde `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-135">The Secret Manager is used from a command shell opened to the project's content root, where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="0f4a8-136">Execute os seguintes comandos em um shell de comando na raiz do conteúdo do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-136">Execute the following commands in a command shell from the project's content root to set the secrets for the sample app:</span></span>

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="0f4a8-137">Quando esses segredos são armazenados no Azure Key Vault na [armazenamento secreto no ambiente de produção com o Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o `_dev` sufixo é alterado para `_prod`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-137">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="0f4a8-138">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-138">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="0f4a8-139">Armazenamento secreto no ambiente de produção com o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0f4a8-139">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="0f4a8-140">As instruções fornecidas pelo [guia de início rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidas aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-140">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="0f4a8-141">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-141">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="0f4a8-142">Abra Azure Cloud shell usando qualquer um dos métodos a seguir na [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="0f4a8-142">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="0f4a8-143">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-143">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="0f4a8-144">Use a cadeia de caracteres de pesquisa "Da CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-144">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="0f4a8-145">Abra o Cloud Shell no navegador com o **iniciar Cloud Shell** botão.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-145">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="0f4a8-146">Selecione o **Cloud Shell** botão no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-146">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="0f4a8-147">Para obter mais informações, consulte [Interface de linha de comando (CLI do Azure)](/cli/azure/) e [visão geral do Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-147">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="0f4a8-148">Se você já não estiver autenticado, entrar com o `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-148">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="0f4a8-149">Criar um grupo de recursos com o comando a seguir, onde `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="0f4a8-149">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0f4a8-150">Criar um cofre de chaves no grupo de recursos com o comando a seguir, onde `{KEY VAULT NAME}` é o nome para o novo cofre de chaves e `{LOCATION}` é a região do Azure (datacenter):</span><span class="sxs-lookup"><span data-stu-id="0f4a8-150">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0f4a8-151">Crie os segredos no cofre de chaves como pares nome-valor.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-151">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="0f4a8-152">Nomes de segredos de Cofre de chaves do Azure são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-152">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="0f4a8-153">Usam valores hierárquicos (seções de configuração) `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-153">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0f4a8-154">Dois-pontos, que normalmente são usadas para delimitar uma seção de uma subchave no [configuração do ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredos do Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-154">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="0f4a8-155">Portanto, os dois traços são usados e trocados para dois-pontos quando os segredos são carregados para a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-155">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="0f4a8-156">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-156">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="0f4a8-157">Os valores incluem uma `_prod` sufixo para distingui-los da `_dev` sufixo valores carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-157">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="0f4a8-158">Substitua `{KEY VAULT NAME}` com o nome do Cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-158">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-client-secret-for-non-azure-hosted-apps"></a><span data-ttu-id="0f4a8-159">Use a ID do aplicativo e segredo do cliente para aplicativos não hospedados do Azure</span><span class="sxs-lookup"><span data-stu-id="0f4a8-159">Use Application ID and Client Secret for non-Azure-hosted apps</span></span>

<span data-ttu-id="0f4a8-160">Configurar o Azure AD, Azure Key Vault e o aplicativo para usar uma ID do aplicativo e a senha (segredo do cliente) para autenticar para um cofre de chaves **quando o aplicativo está hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-160">Configure Azure AD, Azure Key Vault, and the app to use an Application ID and Password (Client Secret) to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span>

> [!NOTE]
> <span data-ttu-id="0f4a8-161">Embora haja suporte para usar uma ID do aplicativo e a senha (segredo do cliente) para aplicativos hospedados no Azure, recomendamos o uso [identidades para recursos do Azure gerenciadas](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-161">Although using an Application ID and Password (Client Secret) is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="0f4a8-162">Identidades gerenciadas exigem armazenamento de credenciais no aplicativo ou sua configuração, portanto, ele é considerado como uma abordagem mais segura em geral.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-162">Managed identities require storing credentials in the app or its configuration, so it's regarded as a generally safer approach.</span></span>

<span data-ttu-id="0f4a8-163">O aplicativo de exemplo usa uma ID do aplicativo e a senha (segredo do cliente) quando o `#define` instrução na parte superior dos *Program.cs* arquivo é definido como `Basic`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-163">The sample app uses an Application ID and Password (Client Secret) when the `#define` statement at the top of the *Program.cs* file is set to `Basic`.</span></span>

1. <span data-ttu-id="0f4a8-164">Registrar o aplicativo com o Azure AD e estabeleça uma senha (segredo do cliente) para a identidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-164">Register the app with Azure AD and establish a Password (Client Secret) for the app's identity.</span></span>
1. <span data-ttu-id="0f4a8-165">Store o nome do Cofre de chaves, a ID do aplicativo e a senha/segredo do cliente do aplicativo *appSettings. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-165">Store the key vault name, Application ID, and Password/Client Secret in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="0f4a8-166">Navegue até **cofres de chaves** no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-166">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="0f4a8-167">Selecione o Cofre de chaves que você criou na [armazenamento secreto no ambiente de produção com o Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-167">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="0f4a8-168">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-168">Select **Access policies**.</span></span>
1. <span data-ttu-id="0f4a8-169">Selecione **adicionar novo**.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-169">Select **Add new**.</span></span>
1. <span data-ttu-id="0f4a8-170">Selecione **selecionar entidade** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-170">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="0f4a8-171">Selecione o **selecionar** botão.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-171">Select the **Select** button.</span></span>
1. <span data-ttu-id="0f4a8-172">Abra **permissões do segredo** e forneça o aplicativo com **obter** e **lista** permissões.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-172">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="0f4a8-173">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-173">Select **OK**.</span></span>
1. <span data-ttu-id="0f4a8-174">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-174">Select **Save**.</span></span>
1. <span data-ttu-id="0f4a8-175">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-175">Deploy the app.</span></span>

<span data-ttu-id="0f4a8-176">O `Basic` aplicativo de exemplo obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-176">The `Basic` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="0f4a8-177">Valores não são hierárquicos: O valor para `SecretName` é obtido com `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-177">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="0f4a8-178">Valores hierárquicos (seções): Use `:` notação (dois-pontos) ou o `GetSection` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-178">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="0f4a8-179">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-179">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="0f4a8-180">O aplicativo chama `AddAzureKeyVault` com os valores fornecidos pelo *appSettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-180">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=11-14)]

<span data-ttu-id="0f4a8-181">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-181">Example values:</span></span>

* <span data-ttu-id="0f4a8-182">Nome do Cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-182">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="0f4a8-183">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-183">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="0f4a8-184">Senha: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-184">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="0f4a8-185">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-185">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="0f4a8-186">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-186">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0f4a8-187">No ambiente de desenvolvimento, os valores secretos carregar com o `_dev` sufixo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-187">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="0f4a8-188">No ambiente de produção, os valores de carga com o `_prod` sufixo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-188">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="0f4a8-189">Usar identidades de gerenciado para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="0f4a8-189">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="0f4a8-190">**Um aplicativo implantado no Azure** podem aproveitar [identidades para recursos do Azure gerenciadas](/azure/active-directory/managed-identities-azure-resources/overview), que permite que o aplicativo autenticar com o Azure Key Vault usando a autenticação do Azure AD sem credenciais (ID do aplicativo e Segredo Password/Client) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-190">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="0f4a8-191">O aplicativo de exemplo usa identidades de gerenciado para recursos do Azure quando o `#define` instrução na parte superior dos *Program.cs* arquivo é definido como `Managed`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-191">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="0f4a8-192">Insira o nome do cofre para o aplicativo *appSettings. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-192">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="0f4a8-193">O aplicativo de exemplo não exige uma ID do aplicativo e a senha (segredo do cliente) quando definido como o `Managed` versão, portanto, você pode ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-193">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="0f4a8-194">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar o Azure Key Vault usando apenas o nome do cofre armazenada em do *appSettings. JSON* arquivo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-194">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="0f4a8-195">Implante o aplicativo de exemplo no serviço de aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-195">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="0f4a8-196">Um aplicativo implantado no serviço de aplicativo do Azure é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-196">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="0f4a8-197">Obter a ID de objeto de implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-197">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="0f4a8-198">A ID de objeto é mostrada no portal do Azure na **identidade** painel do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-198">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="0f4a8-199">Usando a CLI do Azure e a ID de objeto do aplicativo, forneça o aplicativo com `list` e `get` permissões para acessar o Cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-199">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="0f4a8-200">**Reinicie o aplicativo** usando o portal do Azure, PowerShell ou CLI do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-200">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="0f4a8-201">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-201">The sample app:</span></span>

* <span data-ttu-id="0f4a8-202">Cria uma instância do `AzureServiceTokenProvider` classe sem uma cadeia de caracteres de conexão.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-202">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="0f4a8-203">Quando uma cadeia de caracteres de conexão não for fornecida, o provedor tenta obter um token de acesso de identidades de gerenciado para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-203">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="0f4a8-204">Uma nova `KeyVaultClient` é criado com o `AzureServiceTokenProvider` token retorno de chamada de instância.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-204">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="0f4a8-205">O `KeyVaultClient` instância é usada com uma implementação padrão de `IKeyVaultSecretManager` que carrega todos os valores do segredo e substitui os dois traços (`--`) com dois-pontos (`:`) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-205">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="0f4a8-206">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-206">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0f4a8-207">No ambiente de desenvolvimento, os valores do segredo têm o `_dev` sufixo porque elas são fornecidas por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-207">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="0f4a8-208">No ambiente de produção, os valores de carga com o `_prod` sufixo porque elas são fornecidas pelo Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-208">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="0f4a8-209">Se você receber um `Access denied` erro, confirme se o aplicativo está registrado com o Azure AD e recebe acesso ao Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-209">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="0f4a8-210">Confirme que você tiver reiniciado o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-210">Confirm that you've restarted the service in Azure.</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="0f4a8-211">Usar um prefixo de nome da chave</span><span class="sxs-lookup"><span data-stu-id="0f4a8-211">Use a key name prefix</span></span>

<span data-ttu-id="0f4a8-212">`AddAzureKeyVault` Fornece uma sobrecarga que aceita uma implementação de `IKeyVaultSecretManager`, que permite que você controle como os principais segredos do cofre são convertidas em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-212">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="0f4a8-213">Por exemplo, você pode implementar a interface para carregar os valores do segredo com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-213">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="0f4a8-214">Isso permite que você, por exemplo, para carregar segredos de acordo com a versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-214">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="0f4a8-215">Não usar prefixos de segredos do Cofre de chaves para colocar segredos para vários aplicativos no mesmo Cofre de chaves ou para colocar segredos ambientais (por exemplo, *development* versus *produção* segredos) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-215">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="0f4a8-216">É recomendável que diferentes aplicativos e ambientes de desenvolvimento/produção usam cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-216">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="0f4a8-217">No exemplo a seguir, um segredo é estabelecido na chave do cofre (e usar a ferramenta Secret Manager no ambiente de desenvolvimento) para `5000-AppSecret` (períodos não são permitidos em nomes de segredos do Cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-217">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="0f4a8-218">Esse segredo representa um segredo do aplicativo para versão Version=5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-218">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="0f4a8-219">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado à chave de cofre (e usar a ferramenta Secret Manager) para `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-219">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="0f4a8-220">Cada versão do aplicativo carrega seu valor com controle de versão do segredo em sua configuração como `AppSecret`, remoção desativar a versão ele carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-220">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="0f4a8-221">`AddAzureKeyVault` é chamado com um personalizado `IKeyVaultSecretManager`:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-221">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="0f4a8-222">Os valores para nome do Cofre de chaves, a ID do aplicativo e a senha (segredo do cliente) são fornecidos pela *appSettings. JSON* arquivo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-222">The values for key vault name, Application ID, and Password (Client Secret) are provided by the *appsettings.json* file:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="0f4a8-223">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-223">Example values:</span></span>

* <span data-ttu-id="0f4a8-224">Nome do Cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-224">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="0f4a8-225">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-225">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="0f4a8-226">Senha: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span><span class="sxs-lookup"><span data-stu-id="0f4a8-226">Password: `g58K3dtg59o1Pa+e59v2Tx829w6VxTB2yv9sv/101di=`</span></span>

<span data-ttu-id="0f4a8-227">O `IKeyVaultSecretManager` implementação reage aos prefixos de segredos para carregar o segredo apropriado na configuração de versão:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-227">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="0f4a8-228">O `Load` método é chamado por um algoritmo de provedor que itera os segredos do cofre para encontrar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-228">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="0f4a8-229">Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o `GetKey` método para retornar o nome da configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-229">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="0f4a8-230">Ele ignora o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregamento na configuração do aplicativo pares nome-valor.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-230">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="0f4a8-231">Quando esse método é implementado:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-231">When this approach is implemented:</span></span>

1. <span data-ttu-id="0f4a8-232">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-232">The app's version specified in the app's project file.</span></span> <span data-ttu-id="0f4a8-233">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-233">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0f4a8-234">Confirme se um `<UserSecretsId>` propriedade estiver presente no arquivo de projeto do aplicativo, onde `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-234">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="0f4a8-235">Salvar os segredos seguir localmente com o [ferramenta Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="0f4a8-235">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="0f4a8-236">Segredos são salvos no Azure Key Vault usando os seguintes comandos de CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-236">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="0f4a8-237">Quando o aplicativo é executado, os segredos do Cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-237">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="0f4a8-238">O segredo de cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-238">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="0f4a8-239">A versão `5000` (com o traço) é removida do nome de chave.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-239">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="0f4a8-240">Em todo o aplicativo, lendo a configuração com a chave `AppSecret` carrega o valor do segredo.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-240">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="0f4a8-241">Se a versão do aplicativo é alterada no arquivo de projeto para `5.1.0.0` e o aplicativo é executado novamente, é o valor do segredo retornado `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-241">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="0f4a8-242">Você também pode fornecer seus próprios `KeyVaultClient` implementação para `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-242">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="0f4a8-243">Compartilhando uma única instância do cliente entre o aplicativo permite que um cliente personalizado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-243">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="authenticate-to-azure-key-vault-with-an-x509-certificate"></a><span data-ttu-id="0f4a8-244">Autenticar no Azure Key Vault com um certificado X.509</span><span class="sxs-lookup"><span data-stu-id="0f4a8-244">Authenticate to Azure Key Vault with an X.509 certificate</span></span>

<span data-ttu-id="0f4a8-245">Ao desenvolver um aplicativo do .NET Framework em um ambiente que dá suporte a certificados, você pode autenticar para o Azure Key Vault com um certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-245">When developing a .NET Framework app in an environment that supports certificates, you can authenticate to Azure Key Vault with an X.509 certificate.</span></span> <span data-ttu-id="0f4a8-246">Chave privada do certificado X.509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-246">The X.509 certificate's private key is managed by the OS.</span></span> <span data-ttu-id="0f4a8-247">Para obter mais informações, consulte [autenticar com um certificado em vez de um segredo do cliente](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-247">For more information, see [Authenticate with a Certificate instead of a Client Secret](/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret).</span></span> <span data-ttu-id="0f4a8-248">Use o `AddAzureKeyVault` sobrecarga que aceita um `X509Certificate2` (`_env` no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-248">Use the `AddAzureKeyVault` overload that accepts an `X509Certificate2` (`_env` in the following example :</span></span>

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

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0f4a8-249">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="0f4a8-249">Bind an array to a class</span></span>

<span data-ttu-id="0f4a8-250">O provedor é capaz de ler os valores de configuração em uma matriz para a associação para uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-250">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="0f4a8-251">Durante a leitura de uma fonte de configuração que permite que as chaves conter dois-pontos (`:`) separadores, um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="0f4a8-251">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="0f4a8-252">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-252">`:{n}:`).</span></span> <span data-ttu-id="0f4a8-253">Para obter mais informações, consulte [configuração: Associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-253">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="0f4a8-254">Chaves do Azure Key Vault não podem usar dois-pontos como um separador.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-254">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="0f4a8-255">A abordagem descrita neste tópico usa double traços (`--`) como separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-255">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="0f4a8-256">As chaves de matriz são armazenadas no Azure Key Vault com double traços e segmentos de chave numéricos (`--0--`, `--1--`,...</span><span class="sxs-lookup"><span data-stu-id="0f4a8-256">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, …</span></span> <span data-ttu-id="0f4a8-257">`--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-257">`--{n}--`).</span></span>

<span data-ttu-id="0f4a8-258">Examine o seguinte [Serilog](https://serilog.net/) fornecida por um arquivo JSON de configuração do provedor de log.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-258">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="0f4a8-259">Há dois objetos literais definidos na `WriteTo` matriz que refletem os dois Serilog *coletores*, que descrevem os destinos para saída de log:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-259">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="0f4a8-260">A configuração mostrada no arquivo JSON anterior é armazenada no Azure Key Vault usando o traço duplo (`--`) segmentos notação e numérico:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-260">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="0f4a8-261">Chave</span><span class="sxs-lookup"><span data-stu-id="0f4a8-261">Key</span></span> | <span data-ttu-id="0f4a8-262">Valor</span><span class="sxs-lookup"><span data-stu-id="0f4a8-262">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="0f4a8-263">Recarregue os segredos</span><span class="sxs-lookup"><span data-stu-id="0f4a8-263">Reload secrets</span></span>

<span data-ttu-id="0f4a8-264">Segredos são armazenados em cache até `IConfigurationRoot.Reload()` é chamado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-264">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="0f4a8-265">Expirado, desabilitado, e atualizados segredos no cofre de chaves não serão respeitados pelo aplicativo até `Reload` é executado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-265">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="0f4a8-266">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="0f4a8-266">Disabled and expired secrets</span></span>

<span data-ttu-id="0f4a8-267">Segredos desabilitados e expirados lançar um `KeyVaultClientException`.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-267">Disabled and expired secrets throw a `KeyVaultClientException`.</span></span> <span data-ttu-id="0f4a8-268">Para impedir que seu aplicativo acione, substitua o seu aplicativo ou atualizar o segredo desabilitado/expirado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-268">To prevent your app from throwing, replace your app or update the disabled/expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0f4a8-269">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="0f4a8-269">Troubleshoot</span></span>

<span data-ttu-id="0f4a8-270">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada para o [infra-estrutura de log do ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-270">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0f4a8-271">As seguintes condições impedirá que a configuração de carregamento:</span><span class="sxs-lookup"><span data-stu-id="0f4a8-271">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="0f4a8-272">O aplicativo não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-272">The app isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="0f4a8-273">O Cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-273">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="0f4a8-274">O aplicativo não está autorizado a acessar o Cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-274">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="0f4a8-275">A política de acesso não inclui `Get` e `List` permissões.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-275">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="0f4a8-276">No cofre de chaves, os dados de configuração (par nome-valor) estão nomeados incorretamente, ausente, desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-276">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="0f4a8-277">O aplicativo tem o nome do cofre da chave incorreta (`KeyVaultName`), Id de aplicativo do Azure AD (`AzureADApplicationId`), ou a senha (segredo do cliente) do Azure AD (`AzureADPassword`).</span><span class="sxs-lookup"><span data-stu-id="0f4a8-277">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD Password (Client Secret) (`AzureADPassword`).</span></span>
* <span data-ttu-id="0f4a8-278">A senha (segredo do cliente) do Azure AD (`AzureADPassword`) expirou.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-278">The Azure AD Password (Client Secret) (`AzureADPassword`) is expired.</span></span>
* <span data-ttu-id="0f4a8-279">A chave de configuração (nome) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="0f4a8-279">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0f4a8-280">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0f4a8-280">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="0f4a8-281">Microsoft Azure: Cofre de chaves</span><span class="sxs-lookup"><span data-stu-id="0f4a8-281">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="0f4a8-282">Microsoft Azure: Documentação do Key Vault</span><span class="sxs-lookup"><span data-stu-id="0f4a8-282">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="0f4a8-283">Como gerar e transferir protegida por HSM chaves para o Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0f4a8-283">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="0f4a8-284">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="0f4a8-284">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
