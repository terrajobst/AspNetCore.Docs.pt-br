---
title: Azure Key Vault provedor de configuração no ASP.NET Core
author: guardrex
description: Saiba como usar o provedor de configuração Azure Key Vault para configurar um aplicativo usando pares de nome-valor carregados em tempo de execução.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 37ba756cc4170c145d2ab1f9f0a465057cc826c1
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358702"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="39cfa-103">Azure Key Vault provedor de configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39cfa-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="39cfa-104">De [Luke Latham](https://github.com/guardrex) e [Andrew Stanton-enfermaria](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="39cfa-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="39cfa-105">Este documento explica como usar o provedor de configuração do [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para carregar valores de configuração de aplicativo de segredos Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="39cfa-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="39cfa-106">Azure Key Vault é um serviço baseado em nuvem que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços.</span><span class="sxs-lookup"><span data-stu-id="39cfa-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="39cfa-107">Os cenários comuns para usar Azure Key Vault com aplicativos ASP.NET Core incluem:</span><span class="sxs-lookup"><span data-stu-id="39cfa-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="39cfa-108">Controlando o acesso a dados de configuração confidenciais.</span><span class="sxs-lookup"><span data-stu-id="39cfa-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="39cfa-109">Atendendo ao requisito de módulos de segurança de hardware (HSM) validados do FIPS 140-2 nível 2 ao armazenar dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="39cfa-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="39cfa-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39cfa-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="39cfa-111">Pacotes</span><span class="sxs-lookup"><span data-stu-id="39cfa-111">Packages</span></span>

<span data-ttu-id="39cfa-112">Adicione uma referência de pacote ao pacote [Microsoft. Extensions. Configuration. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="39cfa-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="39cfa-113">Aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="39cfa-113">Sample app</span></span>

<span data-ttu-id="39cfa-114">O aplicativo de exemplo é executado em um dos dois modos determinados pela instrução `#define` na parte superior do arquivo *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="39cfa-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="39cfa-115">`Certificate` &ndash; demonstra o uso de uma ID de cliente do Azure Key Vault e o certificado X. 509 para acessar os segredos armazenados no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="39cfa-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="39cfa-116">Esta versão do exemplo pode ser executada em qualquer local, implantada no serviço Azure App ou em qualquer host capaz de servir um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39cfa-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="39cfa-117">`Managed` &ndash; demonstra como usar [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o aplicativo para Azure Key Vault com a autenticação do Azure ad sem credenciais armazenadas no código ou na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="39cfa-118">Ao usar identidades gerenciadas para autenticar, uma ID de aplicativo do Azure AD e senha (segredo do cliente) não são necessárias.</span><span class="sxs-lookup"><span data-stu-id="39cfa-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="39cfa-119">A versão `Managed` do exemplo deve ser implantada no Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="39cfa-120">Siga as orientações na seção [usar as identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="39cfa-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="39cfa-121">Para obter mais informações sobre como configurar um aplicativo de exemplo usando diretivas de pré-processador (`#define`), consulte <xref:index#preprocessor-directives-in-sample-code>.</span><span class="sxs-lookup"><span data-stu-id="39cfa-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="39cfa-122">Armazenamento de segredo no ambiente de desenvolvimento</span><span class="sxs-lookup"><span data-stu-id="39cfa-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="39cfa-123">Defina os segredos localmente usando a [ferramenta Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="39cfa-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="39cfa-124">Quando o aplicativo de exemplo é executado no computador local no ambiente de desenvolvimento, os segredos são carregados do repositório do Gerenciador de segredo local.</span><span class="sxs-lookup"><span data-stu-id="39cfa-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="39cfa-125">A ferramenta Gerenciador de segredo requer uma propriedade `<UserSecretsId>` no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="39cfa-126">Defina o valor da propriedade (`{GUID}`) para qualquer GUID exclusivo:</span><span class="sxs-lookup"><span data-stu-id="39cfa-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="39cfa-127">Os segredos são criados como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="39cfa-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="39cfa-128">Valores hierárquicos (seções de configuração) usam um `:` (dois-pontos) como um separador em ASP.NET Core nomes de chave de [configuração](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="39cfa-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="39cfa-129">O Gerenciador de segredo é usado em um shell de comando aberto na [raiz do conteúdo](xref:fundamentals/index#content-root)do projeto, em que `{SECRET NAME}` é o nome e `{SECRET VALUE}` é o valor:</span><span class="sxs-lookup"><span data-stu-id="39cfa-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="39cfa-130">Execute os seguintes comandos em um shell de comando da raiz de [conteúdo](xref:fundamentals/index#content-root) do projeto para definir os segredos para o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="39cfa-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="39cfa-131">Quando esses segredos são armazenados em Azure Key Vault no [armazenamento secreto no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção, o sufixo `_dev` é alterado para `_prod`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="39cfa-132">O sufixo fornece uma indicação visual na saída do aplicativo que indica a origem dos valores de configuração.</span><span class="sxs-lookup"><span data-stu-id="39cfa-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="39cfa-133">Armazenamento de segredo no ambiente de produção com Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="39cfa-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="39cfa-134">As instruções fornecidas pelo guia de [início rápido: definem e recuperam um segredo de Azure Key Vault usando CLI do Azure](/azure/key-vault/quick-create-cli) tópico são resumidos aqui para criar um Azure Key Vault e armazenar segredos usados pelo aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="39cfa-135">Consulte o tópico para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="39cfa-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="39cfa-136">Abra o Azure cloud shell usando qualquer um dos seguintes métodos no [portal do Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="39cfa-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="39cfa-137">Selecione **Experimente** no canto superior direito de um bloco de código.</span><span class="sxs-lookup"><span data-stu-id="39cfa-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="39cfa-138">Use a cadeia de caracteres de pesquisa "CLI do Azure" na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="39cfa-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="39cfa-139">Abra Cloud Shell em seu navegador com o botão **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="39cfa-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="39cfa-140">Selecione o botão **Cloud Shell** no menu no canto superior direito do portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="39cfa-141">Para obter mais informações, consulte [interface de linha de comando (CLI) do Azure](/cli/azure/) e [visão geral do Azure cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="39cfa-141">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="39cfa-142">Se você ainda não estiver autenticado, entre com o comando `az login`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="39cfa-143">Crie um grupo de recursos com o comando a seguir, em que `{RESOURCE GROUP NAME}` é o nome do grupo de recursos para o novo grupo de recursos e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="39cfa-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="39cfa-144">Crie um cofre de chaves no grupo de recursos com o comando a seguir, em que `{KEY VAULT NAME}` é o nome do novo cofre de chaves e `{LOCATION}` é a região do Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="39cfa-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="39cfa-145">Crie segredos no cofre de chaves como pares de nome-valor.</span><span class="sxs-lookup"><span data-stu-id="39cfa-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="39cfa-146">Azure Key Vault nomes de segredo são limitados a caracteres alfanuméricos e traços.</span><span class="sxs-lookup"><span data-stu-id="39cfa-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="39cfa-147">Valores hierárquicos (seções de configuração) usam `--` (dois traços) como um separador.</span><span class="sxs-lookup"><span data-stu-id="39cfa-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="39cfa-148">Os dois-pontos, que normalmente são usados para delimitar uma seção de uma subchave na [configuração ASP.NET Core](xref:fundamentals/configuration/index), não são permitidos em nomes de segredo do cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="39cfa-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="39cfa-149">Portanto, dois traços são usados e alternados por dois-pontos quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="39cfa-150">Os segredos a seguir são para uso com o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="39cfa-151">Os valores incluem um sufixo `_prod` para distingui-los dos valores de sufixo `_dev` carregados no ambiente de desenvolvimento de segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="39cfa-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="39cfa-152">Substitua `{KEY VAULT NAME}` pelo nome do cofre de chaves que você criou na etapa anterior:</span><span class="sxs-lookup"><span data-stu-id="39cfa-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="39cfa-153">Use a ID do aplicativo e o certificado X. 509 para aplicativos não hospedados no Azure</span><span class="sxs-lookup"><span data-stu-id="39cfa-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="39cfa-154">Configure o Azure AD, Azure Key Vault e o aplicativo para usar uma ID de aplicativo Azure Active Directory e um certificado X. 509 para autenticar em um cofre de chaves **quando o aplicativo estiver hospedado fora do Azure**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="39cfa-155">Para obter mais informações, consulte [Sobre chaves, segredos e certificados](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="39cfa-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="39cfa-156">Embora o uso de uma ID de aplicativo e um certificado X. 509 tenha suporte para aplicativos hospedados no Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](#use-managed-identities-for-azure-resources) ao hospedar um aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="39cfa-157">Identidades gerenciadas não exigem o armazenamento de um certificado no aplicativo ou no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="39cfa-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="39cfa-158">O aplicativo de exemplo usa uma ID de aplicativo e um certificado X. 509 quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Certificate`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="39cfa-159">Crie um certificado PKCS # 12 arquivo morto ( *. pfx*).</span><span class="sxs-lookup"><span data-stu-id="39cfa-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="39cfa-160">As opções para criar certificados incluem [MakeCert no Windows](/windows/desktop/seccrypto/makecert) e [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="39cfa-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="39cfa-161">Instale o certificado no repositório de certificados pessoal do usuário atual.</span><span class="sxs-lookup"><span data-stu-id="39cfa-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="39cfa-162">Marcar a chave como exportável é opcional.</span><span class="sxs-lookup"><span data-stu-id="39cfa-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="39cfa-163">Observe a impressão digital do certificado, que é usada posteriormente neste processo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="39cfa-164">Exporte o certificado PKCS # 12 Archive ( *. pfx*) como um certificado codificado em der ( *. cer*).</span><span class="sxs-lookup"><span data-stu-id="39cfa-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="39cfa-165">Registre o aplicativo com o Azure AD (**registros de aplicativo**).</span><span class="sxs-lookup"><span data-stu-id="39cfa-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="39cfa-166">Carregue o certificado codificado em DER ( *. cer*) no Azure AD:</span><span class="sxs-lookup"><span data-stu-id="39cfa-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="39cfa-167">Selecione o aplicativo no Azure AD.</span><span class="sxs-lookup"><span data-stu-id="39cfa-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="39cfa-168">Navegue até **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="39cfa-169">Selecione **carregar certificado** para carregar o certificado, que contém a chave pública.</span><span class="sxs-lookup"><span data-stu-id="39cfa-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="39cfa-170">Um certificado *. cer*, *. pem*ou *. CRT* é aceitável.</span><span class="sxs-lookup"><span data-stu-id="39cfa-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="39cfa-171">Armazene o nome do cofre de chaves, a ID do aplicativo e a impressão digital do certificado no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="39cfa-172">Navegue até **cofres de chaves** na portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="39cfa-173">Selecione o cofre de chaves que você criou no [armazenamento de segredo no ambiente de produção com Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) seção.</span><span class="sxs-lookup"><span data-stu-id="39cfa-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="39cfa-174">Selecione **Políticas de acesso**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="39cfa-175">Selecione **Adicionar política de acesso**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="39cfa-176">Abra **permissões de segredo** e forneça ao aplicativo as permissões **Get** e **list** .</span><span class="sxs-lookup"><span data-stu-id="39cfa-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="39cfa-177">Selecione **selecionar entidade de segurança** e selecione o aplicativo registrado por nome.</span><span class="sxs-lookup"><span data-stu-id="39cfa-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="39cfa-178">Escolha o botão **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="39cfa-179">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-179">Select **OK**.</span></span>
1. <span data-ttu-id="39cfa-180">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="39cfa-180">Select **Save**.</span></span>
1. <span data-ttu-id="39cfa-181">Implante o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-181">Deploy the app.</span></span>

<span data-ttu-id="39cfa-182">O aplicativo de exemplo `Certificate` obtém seus valores de configuração de `IConfigurationRoot` com o mesmo nome que o nome do segredo:</span><span class="sxs-lookup"><span data-stu-id="39cfa-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="39cfa-183">Valores não hierárquicos: o valor de `SecretName` é obtido com `config["SecretName"]`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="39cfa-184">Valores hierárquicos (seções): Use a notação de `:` (dois-pontos) ou o método de extensão de `GetSection`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="39cfa-185">Use qualquer uma dessas abordagens para obter o valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="39cfa-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="39cfa-186">O certificado X. 509 é gerenciado pelo sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="39cfa-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="39cfa-187">O aplicativo chama <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> com valores fornecidos pelo arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="39cfa-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

<span data-ttu-id="39cfa-188">Valores de exemplo:</span><span class="sxs-lookup"><span data-stu-id="39cfa-188">Example values:</span></span>

* <span data-ttu-id="39cfa-189">Nome do cofre de chaves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="39cfa-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="39cfa-190">ID do aplicativo: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="39cfa-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="39cfa-191">Impressão digital do certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="39cfa-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="39cfa-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="39cfa-192">*appsettings.json*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

<span data-ttu-id="39cfa-193">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="39cfa-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="39cfa-194">No ambiente de desenvolvimento, os valores secretos são carregados com o sufixo `_dev`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="39cfa-195">No ambiente de produção, os valores são carregados com o sufixo `_prod`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="39cfa-196">Usar identidades gerenciadas para recursos do Azure</span><span class="sxs-lookup"><span data-stu-id="39cfa-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="39cfa-197">**Um aplicativo implantado no Azure** pode aproveitar as [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview), o que permite que o aplicativo se autentique com Azure Key Vault usando a autenticação do Azure ad sem credenciais (ID do aplicativo e senha/segredo do cliente) armazenados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="39cfa-198">O aplicativo de exemplo usa identidades gerenciadas para recursos do Azure quando a instrução `#define` na parte superior do arquivo *Program.cs* é definida como `Managed`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="39cfa-199">Insira o nome do cofre no arquivo *appSettings. JSON* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="39cfa-200">O aplicativo de exemplo não exige uma ID de aplicativo e uma senha (segredo do cliente) quando definido para a versão `Managed`, para que você possa ignorar essas entradas de configuração.</span><span class="sxs-lookup"><span data-stu-id="39cfa-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="39cfa-201">O aplicativo é implantado no Azure e o Azure autentica o aplicativo para acessar Azure Key Vault apenas usando o nome do cofre armazenado no arquivo *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="39cfa-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="39cfa-202">Implante o aplicativo de exemplo no serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="39cfa-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="39cfa-203">Um aplicativo implantado no serviço Azure App é registrado automaticamente com o Azure AD quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="39cfa-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="39cfa-204">Obtenha a ID de objeto da implantação para uso no comando a seguir.</span><span class="sxs-lookup"><span data-stu-id="39cfa-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="39cfa-205">A ID de objeto é mostrada no portal do Azure no painel de **identidade** do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="39cfa-206">Usando CLI do Azure e a ID de objeto do aplicativo, forneça ao aplicativo as permissões de `list` e `get` para acessar o cofre de chaves:</span><span class="sxs-lookup"><span data-stu-id="39cfa-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="39cfa-207">**Reinicie o aplicativo** usando CLI do Azure, PowerShell ou o portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="39cfa-208">O aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="39cfa-208">The sample app:</span></span>

* <span data-ttu-id="39cfa-209">Cria uma instância da classe `AzureServiceTokenProvider` sem uma cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="39cfa-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="39cfa-210">Quando uma cadeia de conexão não é fornecida, o provedor tenta obter um token de acesso de identidades gerenciadas para recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="39cfa-211">Um novo <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é criado com o retorno de chamada de token de instância de `AzureServiceTokenProvider`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="39cfa-212">A instância de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> é usada com uma implementação padrão de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carrega todos os valores secretos e substitui duplos (`--`) por dois-pontos (`:`) em nomes de chave.</span><span class="sxs-lookup"><span data-stu-id="39cfa-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

<span data-ttu-id="39cfa-213">Nome do cofre de chaves valor de exemplo: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="39cfa-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="39cfa-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="39cfa-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="39cfa-215">Quando você executa o aplicativo, uma página da Web mostra os valores secretos carregados.</span><span class="sxs-lookup"><span data-stu-id="39cfa-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="39cfa-216">No ambiente de desenvolvimento, os valores secretos têm o sufixo `_dev` porque são fornecidos por segredos do usuário.</span><span class="sxs-lookup"><span data-stu-id="39cfa-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="39cfa-217">No ambiente de produção, os valores são carregados com o sufixo `_prod` porque são fornecidos por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="39cfa-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="39cfa-218">Se você receber um erro de `Access denied`, confirme se o aplicativo está registrado no Azure AD e se forneceu acesso ao cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="39cfa-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="39cfa-219">Confirme que você reiniciou o serviço no Azure.</span><span class="sxs-lookup"><span data-stu-id="39cfa-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="39cfa-220">Para obter informações sobre como usar o provedor com uma identidade gerenciada e um pipeline DevOps do Azure, consulte [criar uma conexão de serviço Azure Resource Manager para uma VM com uma identidade de serviço gerenciado](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="39cfa-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configuration-options"></a><span data-ttu-id="39cfa-221">{1&gt;Opções de configuração&lt;1}</span><span class="sxs-lookup"><span data-stu-id="39cfa-221">Configuration options</span></span>

<span data-ttu-id="39cfa-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> pode aceitar uma <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span><span class="sxs-lookup"><span data-stu-id="39cfa-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="39cfa-223">propriedade</span><span class="sxs-lookup"><span data-stu-id="39cfa-223">Property</span></span>         | <span data-ttu-id="39cfa-224">Descrição</span><span class="sxs-lookup"><span data-stu-id="39cfa-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="39cfa-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> a ser usado para recuperar valores.</span><span class="sxs-lookup"><span data-stu-id="39cfa-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="39cfa-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instância usada para controlar o carregamento do segredo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="39cfa-227">`Timespan` aguardar entre as tentativas de sondagem do cofre de chaves para alterações.</span><span class="sxs-lookup"><span data-stu-id="39cfa-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="39cfa-228">O valor padrão é `null` (a configuração não é recarregada).</span><span class="sxs-lookup"><span data-stu-id="39cfa-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="39cfa-229">URI do Key Vault.</span><span class="sxs-lookup"><span data-stu-id="39cfa-229">Key vault URI.</span></span> |

::: moniker-end

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="39cfa-230">Usar um prefixo de nome de chave</span><span class="sxs-lookup"><span data-stu-id="39cfa-230">Use a key name prefix</span></span>

<span data-ttu-id="39cfa-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> fornece uma sobrecarga que aceita uma implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, que permite controlar como os segredos do cofre de chaves são convertidos em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="39cfa-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="39cfa-232">Por exemplo, você pode implementar a interface para carregar valores secretos com base em um valor de prefixo que você fornece na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="39cfa-233">Isso permite, por exemplo, carregar segredos com base na versão do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="39cfa-234">Não use prefixos em segredos do cofre de chaves para colocar segredos para vários aplicativos no mesmo cofre de chaves ou colocar segredos ambientais (por exemplo, *desenvolvimento* versus segredos de *produção* ) no mesmo cofre.</span><span class="sxs-lookup"><span data-stu-id="39cfa-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="39cfa-235">Recomendamos que aplicativos diferentes e ambientes de desenvolvimento/produção usem cofres de chaves separados para isolar os ambientes de aplicativo para o nível mais alto de segurança.</span><span class="sxs-lookup"><span data-stu-id="39cfa-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="39cfa-236">No exemplo a seguir, um segredo é estabelecido no cofre de chaves (e usando a ferramenta Gerenciador de segredo para o ambiente de desenvolvimento) para `5000-AppSecret` (os períodos não são permitidos em nomes de segredo do cofre de chaves).</span><span class="sxs-lookup"><span data-stu-id="39cfa-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="39cfa-237">Esse segredo representa um segredo do aplicativo para a versão 5.0.0.0 do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="39cfa-238">Para outra versão do aplicativo, 5.1.0.0, um segredo é adicionado ao cofre de chaves (e usando a ferramenta Gerenciador de segredo) para `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="39cfa-239">Cada versão do aplicativo carrega seu valor secreto com versão em sua configuração como `AppSecret`, eliminando a versão à medida que ela carrega o segredo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="39cfa-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> é chamado com uma <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>personalizada:</span><span class="sxs-lookup"><span data-stu-id="39cfa-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="39cfa-241">A implementação de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> reage aos prefixos de versão dos segredos para carregar o segredo apropriado na configuração:</span><span class="sxs-lookup"><span data-stu-id="39cfa-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="39cfa-242">`Load` carrega um segredo quando seu nome começa com o prefixo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="39cfa-243">Outros segredos não são carregados.</span><span class="sxs-lookup"><span data-stu-id="39cfa-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="39cfa-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="39cfa-244">`GetKey`:</span></span>
  * <span data-ttu-id="39cfa-245">Remove o prefixo do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="39cfa-246">Substitui dois traços em qualquer nome pelo `KeyDelimiter`, que é o delimitador usado na configuração (geralmente um dois-pontos).</span><span class="sxs-lookup"><span data-stu-id="39cfa-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="39cfa-247">Azure Key Vault não permite dois pontos em nomes secretos.</span><span class="sxs-lookup"><span data-stu-id="39cfa-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="39cfa-248">O método `Load` é chamado por um algoritmo de provedor que itera pelos segredos do cofre para localizar aqueles que têm o prefixo de versão.</span><span class="sxs-lookup"><span data-stu-id="39cfa-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="39cfa-249">Quando um prefixo de versão é encontrado com `Load`, o algoritmo usa o método `GetKey` para retornar o nome da configuração do nome do segredo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="39cfa-250">Ele retira o prefixo de versão do nome do segredo e retorna o restante do nome do segredo para carregar nos pares nome-valor da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="39cfa-251">Quando essa abordagem é implementada:</span><span class="sxs-lookup"><span data-stu-id="39cfa-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="39cfa-252">A versão do aplicativo especificada no arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="39cfa-253">No exemplo a seguir, a versão do aplicativo é definida como `5.0.0.0`:</span><span class="sxs-lookup"><span data-stu-id="39cfa-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="39cfa-254">Confirme se uma propriedade `<UserSecretsId>` está presente no arquivo de projeto do aplicativo, em que `{GUID}` é um GUID fornecido pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="39cfa-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="39cfa-255">Salve os seguintes segredos localmente com a [ferramenta Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="39cfa-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="39cfa-256">Os segredos são salvos em Azure Key Vault usando os seguintes comandos CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="39cfa-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="39cfa-257">Quando o aplicativo é executado, os segredos do cofre de chaves são carregados.</span><span class="sxs-lookup"><span data-stu-id="39cfa-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="39cfa-258">O segredo da cadeia de caracteres para `5000-AppSecret` corresponde à versão do aplicativo especificada no arquivo de projeto do aplicativo (`5.0.0.0`).</span><span class="sxs-lookup"><span data-stu-id="39cfa-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="39cfa-259">A versão, `5000` (com o Dash), é extraída do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="39cfa-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="39cfa-260">Em todo o aplicativo, a leitura da configuração com a chave `AppSecret` carrega o valor secreto.</span><span class="sxs-lookup"><span data-stu-id="39cfa-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="39cfa-261">Se a versão do aplicativo for alterada no arquivo de projeto para `5.1.0.0` e o aplicativo for executado novamente, o valor secreto retornado será `5.1.0.0_secret_value_dev` no ambiente de desenvolvimento e `5.1.0.0_secret_value_prod` em produção.</span><span class="sxs-lookup"><span data-stu-id="39cfa-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="39cfa-262">Você também pode fornecer sua própria implementação de <xref:Microsoft.Azure.KeyVault.KeyVaultClient> para <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span><span class="sxs-lookup"><span data-stu-id="39cfa-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="39cfa-263">Um cliente personalizado permite compartilhar uma única instância do cliente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="39cfa-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="39cfa-264">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="39cfa-264">Bind an array to a class</span></span>

<span data-ttu-id="39cfa-265">O provedor é capaz de ler valores de configuração em uma matriz para associação a uma matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="39cfa-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="39cfa-266">Ao ler de uma fonte de configuração que permite que as chaves contenham separadores de dois pontos (`:`), um segmento de chave numérico é usado para distinguir as chaves que compõem uma matriz (`:0:`, `:1:`,...</span><span class="sxs-lookup"><span data-stu-id="39cfa-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="39cfa-267">`:{n}:`).</span><span class="sxs-lookup"><span data-stu-id="39cfa-267">`:{n}:`).</span></span> <span data-ttu-id="39cfa-268">Para obter mais informações, consulte [configuração: associar uma matriz a uma classe](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="39cfa-268">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="39cfa-269">Azure Key Vault chaves não podem usar dois-pontos como separador.</span><span class="sxs-lookup"><span data-stu-id="39cfa-269">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="39cfa-270">A abordagem descrita neste tópico usa traços duplos (`--`) como um separador de valores hierárquicos (seções).</span><span class="sxs-lookup"><span data-stu-id="39cfa-270">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="39cfa-271">As chaves de matriz são armazenadas em Azure Key Vault com traços duplos e segmentos de chave numérica (`--0--`, `--1--`&hellip; `--{n}--`).</span><span class="sxs-lookup"><span data-stu-id="39cfa-271">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="39cfa-272">Examine a seguinte configuração do provedor de log [Serilog](https://serilog.net/) fornecida por um arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="39cfa-272">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="39cfa-273">Há dois literais de objeto definidos na matriz de `WriteTo` que refletem dois *coletores*Serilog, que descrevem os destinos para a saída de log:</span><span class="sxs-lookup"><span data-stu-id="39cfa-273">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="39cfa-274">A configuração mostrada no arquivo JSON anterior é armazenada em Azure Key Vault usando a notação de travessão duplo (`--`) e os segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="39cfa-274">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="39cfa-275">Chave</span><span class="sxs-lookup"><span data-stu-id="39cfa-275">Key</span></span> | <span data-ttu-id="39cfa-276">Value</span><span class="sxs-lookup"><span data-stu-id="39cfa-276">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="39cfa-277">Recarregar segredos</span><span class="sxs-lookup"><span data-stu-id="39cfa-277">Reload secrets</span></span>

<span data-ttu-id="39cfa-278">Os segredos são armazenados em cache até que `IConfigurationRoot.Reload()` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="39cfa-278">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="39cfa-279">Os segredos expirados, desabilitados e atualizados no cofre de chaves não são respeitados pelo aplicativo até que `Reload` seja executado.</span><span class="sxs-lookup"><span data-stu-id="39cfa-279">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="39cfa-280">Segredos desabilitados e expirados</span><span class="sxs-lookup"><span data-stu-id="39cfa-280">Disabled and expired secrets</span></span>

<span data-ttu-id="39cfa-281">Os segredos desabilitados e expirados lançam um <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span><span class="sxs-lookup"><span data-stu-id="39cfa-281">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="39cfa-282">Para impedir que o aplicativo seja acionado, forneça a configuração usando um provedor de configuração diferente ou atualize o segredo desabilitado ou expirado.</span><span class="sxs-lookup"><span data-stu-id="39cfa-282">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="39cfa-283">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="39cfa-283">Troubleshoot</span></span>

<span data-ttu-id="39cfa-284">Quando o aplicativo falha ao carregar a configuração usando o provedor, uma mensagem de erro é gravada na [infraestrutura de log de ASP.NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="39cfa-284">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="39cfa-285">As seguintes condições impedirão que a configuração seja carregada:</span><span class="sxs-lookup"><span data-stu-id="39cfa-285">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="39cfa-286">O aplicativo ou certificado não está configurado corretamente no Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="39cfa-286">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="39cfa-287">O cofre de chaves não existe no Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="39cfa-287">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="39cfa-288">O aplicativo não está autorizado a acessar o cofre de chaves.</span><span class="sxs-lookup"><span data-stu-id="39cfa-288">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="39cfa-289">A política de acesso não inclui as permissões `Get` e `List`.</span><span class="sxs-lookup"><span data-stu-id="39cfa-289">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="39cfa-290">No cofre de chaves, os dados de configuração (par nome-valor) são nomeados incorretamente, ausentes, desabilitados ou expirados.</span><span class="sxs-lookup"><span data-stu-id="39cfa-290">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="39cfa-291">O aplicativo tem o nome do cofre de chaves (`KeyVaultName`) errado, a ID do aplicativo do Azure AD (`AzureADApplicationId`) ou a impressão digital do certificado do Azure AD (`AzureADCertThumbprint`).</span><span class="sxs-lookup"><span data-stu-id="39cfa-291">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="39cfa-292">A chave de configuração (Name) está incorreta no aplicativo para o valor que você está tentando carregar.</span><span class="sxs-lookup"><span data-stu-id="39cfa-292">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="39cfa-293">Ao adicionar a política de acesso para o aplicativo ao cofre de chaves, a política foi criada, mas o botão **salvar** não foi selecionado na interface do usuário de **políticas de acesso** .</span><span class="sxs-lookup"><span data-stu-id="39cfa-293">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39cfa-294">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="39cfa-294">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="39cfa-295">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="39cfa-295">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="39cfa-296">Microsoft Azure: Key Vault documentação</span><span class="sxs-lookup"><span data-stu-id="39cfa-296">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="39cfa-297">Como gerar e transferir chaves protegidas por HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="39cfa-297">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="39cfa-298">Classe KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="39cfa-298">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="39cfa-299">Início rápido: definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET</span><span class="sxs-lookup"><span data-stu-id="39cfa-299">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="39cfa-300">Tutorial: como usar o Azure Key Vault com a máquina virtual do Windows do Azure no .NET</span><span class="sxs-lookup"><span data-stu-id="39cfa-300">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
