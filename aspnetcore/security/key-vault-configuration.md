---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: guardrex
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2019
uid: security/key-vault-configuration
ms.openlocfilehash: acc3a77cdeb3ba73d8467d465128106e461efa7c
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034324"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="8a465-103">Azure Key Vault provedor de configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a465-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="8a465-104">De [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-enfermaria](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="8a465-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="8a465-105">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8a465-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="8a465-106">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="8a465-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="8a465-107">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="8a465-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="8a465-108">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="8a465-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="8a465-109">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a465-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="8a465-110">Esse cenário está disponível para aplicativos direcionados ASP.NET Core 2,1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="8a465-110">This scenario is available for apps that target ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="8a465-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a465-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="8a465-112">Pacotes</span><span class="sxs-lookup"><span data-stu-id="8a465-112">Packages</span></span>

<span data-ttu-id="8a465-113">Para usar o provedor de configuração Azure Key Vault, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="8a465-113">To use the Azure Key Vault Configuration Provider, add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

<span data-ttu-id="8a465-114">Para adotar as [identidades gerenciadas para o cenário de recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) , adicione uma referência de pacote ao pacote [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .</span><span class="sxs-lookup"><span data-stu-id="8a465-114">To adopt the [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) scenario, add a package reference to the [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) package.</span></span>

> [!NOTE]
> <span data-ttu-id="8a465-115">No momento da elaboração do artigo, a versão estável mais recente do `Microsoft.Azure.Services.AppAuthentication`, Version `1.0.3`, fornece suporte para [identidades gerenciadas atribuídas pelo sistema](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span><span class="sxs-lookup"><span data-stu-id="8a465-115">At the time of writing, the latest stable release of `Microsoft.Azure.Services.AppAuthentication`, version `1.0.3`, provides support for [system-assigned managed identities](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work).</span></span> <span data-ttu-id="8a465-116">O suporte para *identidades gerenciadas atribuídas pelo usuário* está disponível no pacote `1.2.0-preview2`.</span><span class="sxs-lookup"><span data-stu-id="8a465-116">Support for *user-assigned managed identities* is available in the `1.2.0-preview2` package.</span></span> <span data-ttu-id="8a465-117">Este tópico demonstra o uso de identidades gerenciadas pelo sistema e o aplicativo de exemplo fornecido usa a versão `1.0.3` do pacote `Microsoft.Azure.Services.AppAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="8a465-117">This topic demonstrates the use of system-managed identities, and the provided sample app uses version `1.0.3` of the `Microsoft.Azure.Services.AppAuthentication` package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="8a465-118">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="8a465-118">Sample app</span></span>

<span data-ttu-id="8a465-119">O aplicativo de exemplo é executado em um dos dois modos determinados pela instrução `#define` na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="8a465-119">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="8a465-120">`Certificate` &ndash; demonstra o uso de uma ID de cliente do Azure Key Vault e o certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8a465-120">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="8a465-121">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8a465-121">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="8a465-122">`Managed` &ndash; demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-122">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="8a465-123">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="8a465-123">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="8a465-124">A versão `Managed` do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-124">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="8a465-125">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="8a465-125">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="8a465-126">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="8a465-126">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="8a465-127">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="8a465-127">Secret storage in the Development environment</span></span>

<span data-ttu-id="8a465-128">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8a465-128">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8a465-129">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="8a465-129">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="8a465-130">A ferramenta Gerenciador de segredo requer uma propriedade `<UserSecretsId>` no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-130">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="8a465-131">Defina o valor da propriedade (`{GUID}`) para qualquer GUID exclusivo:</span><span class="sxs-lookup"><span data-stu-id="8a465-131">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="8a465-132">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="8a465-132">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="8a465-133">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="8a465-133">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="8a465-134">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="8a465-134">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="8a465-135">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a465-135">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="8a465-136">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o sufixo `_dev` é alterado para `_prod`.</span><span class="sxs-lookup"><span data-stu-id="8a465-136">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="8a465-137">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a465-137">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="8a465-138">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8a465-138">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="8a465-139">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a465-139">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="8a465-140">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="8a465-140">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="8a465-141">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="8a465-141">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="8a465-142">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="8a465-142">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="8a465-143">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="8a465-143">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="8a465-144">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="8a465-144">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="8a465-145">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-145">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="8a465-146">Para obter mais informações, consulte [interface de linha de comando (CLI) do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="8a465-146">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="8a465-147">Se você ainda não estiver autenticado, entre com o comando `az login`.</span><span class="sxs-lookup"><span data-stu-id="8a465-147">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="8a465-148">Crie um grupo de recursos com o comando a seguir, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8a465-148">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8a465-149">Crie um cofre de chaves no grupo de recursos com o comando a seguir, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="8a465-149">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="8a465-150">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="8a465-150">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="8a465-151">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="8a465-151">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="8a465-152">Valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="8a465-152">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8a465-153">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="8a465-153">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="8a465-154">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-154">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="8a465-155">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="8a465-155">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="8a465-156">Os valores incluem um sufixo `_prod` para distingui-los dos valores de sufixo `_dev` carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a465-156">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="8a465-157">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="8a465-157">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="8a465-158">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="8a465-158">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="8a465-159">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="8a465-159">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="8a465-160">Para obter mais informações, consulte [sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="8a465-160">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="8a465-161">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-161">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="8a465-162">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="8a465-162">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="8a465-163">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="8a465-163">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="8a465-164">Crie um certificado PKCS # 12 arquivo morto ( *. pfx*).</span><span class="sxs-lookup"><span data-stu-id="8a465-164">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="8a465-165">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="8a465-165">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="8a465-166">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="8a465-166">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="8a465-167">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="8a465-167">Marking the key as exportable is optional.</span></span> <span data-ttu-id="8a465-168">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="8a465-168">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="8a465-169">Exporte o certificado PKCS # 12 Archive ( *. pfx*) como um certificado codificado em der ( *. cer*).</span><span class="sxs-lookup"><span data-stu-id="8a465-169">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="8a465-170">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="8a465-170">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="8a465-171">Carregue o certificado codificado em DER ( *. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="8a465-171">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="8a465-172">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="8a465-172">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="8a465-173">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="8a465-173">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="8a465-174">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="8a465-174">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="8a465-175">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="8a465-175">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="8a465-176">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-176">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="8a465-177">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-177">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="8a465-178">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="8a465-178">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="8a465-179">Selecione **políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="8a465-179">Select **Access policies**.</span></span>
1. <span data-ttu-id="8a465-180">Selecione **Adicionar novo**.</span><span class="sxs-lookup"><span data-stu-id="8a465-180">Select **Add new**.</span></span>
1. <span data-ttu-id="8a465-181">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="8a465-181">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="8a465-182">Selecione o botão **selecionar** .</span><span class="sxs-lookup"><span data-stu-id="8a465-182">Select the **Select** button.</span></span>
1. <span data-ttu-id="8a465-183">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="8a465-183">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="8a465-184">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="8a465-184">Select **OK**.</span></span>
1. <span data-ttu-id="8a465-185">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8a465-185">Select **Save**.</span></span>
1. <span data-ttu-id="8a465-186">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-186">Deploy the app.</span></span>

<span data-ttu-id="8a465-187">O aplicativo de exemplo `Certificate` obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="8a465-187">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="8a465-188">Valores não hierárquicos: o valor de `SecretName` é obtido com `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="8a465-188">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="8a465-189">Valores hierárquicos (seções): Use a notação de `:` (dois-pontos) ou o método de extensão de `GetSection`.</span><span class="sxs-lookup"><span data-stu-id="8a465-189">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="8a465-190">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="8a465-190">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="8a465-191">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="8a465-191">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="8a465-192">O aplicativo chama `AddAzureKeyVault` com valores fornecidos pelo arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="8a465-192">The app calls `AddAzureKeyVault` with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="8a465-193">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a465-193">Example values:</span></span>

* <span data-ttu-id="8a465-194">Nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8a465-194">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="8a465-195">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="8a465-195">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="8a465-196">Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="8a465-196">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="8a465-197">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8a465-197">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/sample/appsettings.json)]

<span data-ttu-id="8a465-198">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="8a465-198">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8a465-199">No ambiente de desenvolvimento, os valores secretos são carregados com o sufixo `_dev`.</span><span class="sxs-lookup"><span data-stu-id="8a465-199">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="8a465-200">No ambiente de produção, os valores são carregados com o sufixo `_prod`.</span><span class="sxs-lookup"><span data-stu-id="8a465-200">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="8a465-201">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="8a465-201">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="8a465-202">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-202">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="8a465-203">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Managed`.</span><span class="sxs-lookup"><span data-stu-id="8a465-203">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="8a465-204">Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-204">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="8a465-205">O aplicativo de exemplo não exige uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a versão `Managed`, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a465-205">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="8a465-206">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8a465-206">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="8a465-207">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="8a465-207">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="8a465-208">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="8a465-208">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="8a465-209">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="8a465-209">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="8a465-210">A ID de objeto é mostrada no portal do Azure no painel de **identidade** do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-210">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="8a465-211">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo as permissões de `list` e `get` para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="8a465-211">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="8a465-212">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-212">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="8a465-213">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="8a465-213">The sample app:</span></span>

* <span data-ttu-id="8a465-214">Cria uma instância da classe `AzureServiceTokenProvider` sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="8a465-214">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="8a465-215">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-215">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="8a465-216">Um novo `KeyVaultClient` é criado com o retorno de chamada de token de instância de `AzureServiceTokenProvider`.</span><span class="sxs-lookup"><span data-stu-id="8a465-216">A new `KeyVaultClient` is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="8a465-217">A instância de `KeyVaultClient` é usada com uma implementação padrão de `IKeyVaultSecretManager` que carrega todos os valores secretos e substitui duplos (`--`) por dois-pontos (`:`) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="8a465-217">The `KeyVaultClient` instance is used with a default implementation of `IKeyVaultSecretManager` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="8a465-218">Nome do cofre de chaves valor de exemplo: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="8a465-218">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="8a465-219">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8a465-219">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="8a465-220">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="8a465-220">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="8a465-221">No ambiente de desenvolvimento, os valores secretos têm o sufixo `_dev` porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="8a465-221">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="8a465-222">No ambiente de produção, os valores são carregados com o sufixo `_prod` porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8a465-222">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="8a465-223">Se você receber um erro de `Access denied`, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="8a465-223">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="8a465-224">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="8a465-224">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="8a465-225">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="8a465-225">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="8a465-226">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="8a465-226">Use a key name prefix</span></span>

<span data-ttu-id="8a465-227">`AddAzureKeyVault` fornece uma sobrecarga que aceita uma implementação de `IKeyVaultSecretManager`, que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="8a465-227">`AddAzureKeyVault` provides an overload that accepts an implementation of `IKeyVaultSecretManager`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="8a465-228">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-228">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="8a465-229">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-229">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="8a465-230">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="8a465-230">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="8a465-231">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="8a465-231">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="8a465-232">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="8a465-232">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="8a465-233">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-233">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="8a465-234">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="8a465-234">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="8a465-235">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret`, eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="8a465-235">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="8a465-236">`AddAzureKeyVault` é chamado com uma `IKeyVaultSecretManager` personalizada:</span><span class="sxs-lookup"><span data-stu-id="8a465-236">`AddAzureKeyVault` is called with a custom `IKeyVaultSecretManager`:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

<span data-ttu-id="8a465-237">A implementação de `IKeyVaultSecretManager` reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="8a465-237">The `IKeyVaultSecretManager` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

<span data-ttu-id="8a465-238">O método `Load` é chamado por um algoritmo de provedor que itera pelos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="8a465-238">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="8a465-239">Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o método `GetKey` para retornar o nome da configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="8a465-239">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="8a465-240">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-240">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="8a465-241">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="8a465-241">When this approach is implemented:</span></span>

1. <span data-ttu-id="8a465-242">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-242">The app's version specified in the app's project file.</span></span> <span data-ttu-id="8a465-243">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="8a465-243">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="8a465-244">Confirme se uma propriedade `<UserSecretsId>` está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="8a465-244">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="8a465-245">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="8a465-245">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="8a465-246">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="8a465-246">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="8a465-247">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="8a465-247">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="8a465-248">O segredo da cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="8a465-248">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="8a465-249">A versão, `5000` (com o Dash), é extraída do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="8a465-249">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="8a465-250">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="8a465-250">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="8a465-251">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado será `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="8a465-251">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="8a465-252">Você também pode fornecer sua própria implementação de `KeyVaultClient` para `AddAzureKeyVault`.</span><span class="sxs-lookup"><span data-stu-id="8a465-252">You can also provide your own `KeyVaultClient` implementation to `AddAzureKeyVault`.</span></span> <span data-ttu-id="8a465-253">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8a465-253">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8a465-254">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="8a465-254">Bind an array to a class</span></span>

<span data-ttu-id="8a465-255">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="8a465-255">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="8a465-256">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois pontos (`:`), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="8a465-256">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="8a465-257">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="8a465-257">`:{n}:`).</span></span> <span data-ttu-id="8a465-258">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="8a465-258">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="8a465-259">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="8a465-259">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="8a465-260">A abordagem descrita neste tópico usa traços duplos (`--`) como um separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="8a465-260">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="8a465-261">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica (`--0--`, `--1--` &hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="8a465-261">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="8a465-262">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="8a465-262">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="8a465-263">Há dois literais de objeto definidos na matriz de `WriteTo` que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="8a465-263">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="8a465-264">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a notação de travessão duplo (`--`) e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="8a465-264">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="8a465-265">Chave</span><span class="sxs-lookup"><span data-stu-id="8a465-265">Key</span></span> | <span data-ttu-id="8a465-266">Valor</span><span class="sxs-lookup"><span data-stu-id="8a465-266">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="8a465-267">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="8a465-267">Reload secrets</span></span>

<span data-ttu-id="8a465-268">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="8a465-268">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="8a465-269">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="8a465-269">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="8a465-270">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="8a465-270">Disabled and expired secrets</span></span>

<span data-ttu-id="8a465-271">Os segredos desabilitados e expirados lançam um `KeyVaultClientException` em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="8a465-271">Disabled and expired secrets throw a `KeyVaultClientException` at runtime.</span></span> <span data-ttu-id="8a465-272">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="8a465-272">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8a465-273">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="8a465-273">Troubleshoot</span></span>

<span data-ttu-id="8a465-274">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8a465-274">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8a465-275">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="8a465-275">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="8a465-276">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8a465-276">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="8a465-277">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="8a465-277">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="8a465-278">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="8a465-278">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="8a465-279">A política de acesso não inclui as permissões `Get` e `List`.</span><span class="sxs-lookup"><span data-stu-id="8a465-279">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="8a465-280">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="8a465-280">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="8a465-281">O aplicativo tem o nome do cofre de chaves (`KeyVaultName`) errado, a ID do aplicativo do Azure AD (`AzureADApplicationId`) ou a impressão digital do certificado do Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="8a465-281">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="8a465-282">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="8a465-282">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8a465-283">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8a465-283">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="8a465-284">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="8a465-284">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="8a465-285">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="8a465-285">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="8a465-286">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8a465-286">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="8a465-287">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="8a465-287">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="8a465-288">Início rápido: definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET</span><span class="sxs-lookup"><span data-stu-id="8a465-288">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="8a465-289">Tutorial: como usar o Azure Key Vault com a máquina virtual do Windows do Azure no .NET</span><span class="sxs-lookup"><span data-stu-id="8a465-289">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
