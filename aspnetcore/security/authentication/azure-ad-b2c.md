---
title: Autenticação de nuvem com o Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com o ASP.NET Core.
ms.date: 02/27/2019
ms.custom: mvc
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 86be999e02cfe34193bd594dcf89e8872590cca5
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898013"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="e4a53-103">Autenticação de nuvem com o Azure Active Directory B2C no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4a53-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="e4a53-104">Por [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="e4a53-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="e4a53-105">[Azure Active Directory B2C do diretório](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos web e móveis.</span><span class="sxs-lookup"><span data-stu-id="e4a53-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="e4a53-106">O serviço fornece autenticação para aplicativos hospedados na nuvem e locais.</span><span class="sxs-lookup"><span data-stu-id="e4a53-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="e4a53-107">Tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas de federado.</span><span class="sxs-lookup"><span data-stu-id="e4a53-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="e4a53-108">Além disso, o Azure AD B2C pode fornecer a autenticação multifator com configuração mínima.</span><span class="sxs-lookup"><span data-stu-id="e4a53-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="e4a53-109">Azure Active Directory (Azure AD) e o Azure AD B2C são ofertas de produtos separados.</span><span class="sxs-lookup"><span data-stu-id="e4a53-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="e4a53-110">Um locatário do AD do Azure representa uma organização, enquanto que um locatário do Azure AD B2C representa uma coleção de identidades a serem usados com aplicativos de terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="e4a53-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="e4a53-111">Para obter mais informações, consulte [do Azure AD B2C: Perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="e4a53-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="e4a53-112">Neste tutorial, saiba como:</span><span class="sxs-lookup"><span data-stu-id="e4a53-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e4a53-113">Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="e4a53-114">Registrar um aplicativo no Azure B2C do AD</span><span class="sxs-lookup"><span data-stu-id="e4a53-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="e4a53-115">Usar o Visual Studio para criar um aplicativo web do ASP.NET Core configurado para usar o locatário do Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="e4a53-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="e4a53-116">Configurar políticas para controlar o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4a53-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e4a53-117">Prerequisites</span></span>

<span data-ttu-id="e4a53-118">A seguir é necessários para este passo a passo:</span><span class="sxs-lookup"><span data-stu-id="e4a53-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="e4a53-119">Assinatura do Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="e4a53-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* <span data-ttu-id="e4a53-120">[Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (qualquer edição)</span><span class="sxs-lookup"><span data-stu-id="e4a53-120">[Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) (any edition)</span></span>

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="e4a53-121">Criar o locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="e4a53-122">Criar um locatário do Azure Active Directory B2C [conforme descrito na documentação do](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="e4a53-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="e4a53-123">Quando solicitado, associar o locatário com uma assinatura do Azure é opcional para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="e4a53-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="e4a53-124">Registrar o aplicativo no Azure B2C do AD</span><span class="sxs-lookup"><span data-stu-id="e4a53-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="e4a53-125">No locatário do Azure AD B2C recém-criado, registre seu aplicativo usando [as etapas na documentação do](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) sob o **registrar um aplicativo web** seção.</span><span class="sxs-lookup"><span data-stu-id="e4a53-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) under the **Register a web app** section.</span></span> <span data-ttu-id="e4a53-126">Parar na **criar um segredo do cliente de aplicativo web** seção.</span><span class="sxs-lookup"><span data-stu-id="e4a53-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="e4a53-127">Um segredo do cliente não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="e4a53-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="e4a53-128">Use os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e4a53-128">Use the following values:</span></span>

| <span data-ttu-id="e4a53-129">Configuração</span><span class="sxs-lookup"><span data-stu-id="e4a53-129">Setting</span></span>                       | <span data-ttu-id="e4a53-130">Valor</span><span class="sxs-lookup"><span data-stu-id="e4a53-130">Value</span></span>                     | <span data-ttu-id="e4a53-131">Observações</span><span class="sxs-lookup"><span data-stu-id="e4a53-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e4a53-132">**Nome**</span><span class="sxs-lookup"><span data-stu-id="e4a53-132">**Name**</span></span>                      | <span data-ttu-id="e4a53-133">*&lt;Nome do aplicativo&gt;*</span><span class="sxs-lookup"><span data-stu-id="e4a53-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="e4a53-134">Insira um **nome** para o aplicativo que descrevem seu aplicativo para os consumidores.</span><span class="sxs-lookup"><span data-stu-id="e4a53-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="e4a53-135">**Incluir aplicativo web / API web**</span><span class="sxs-lookup"><span data-stu-id="e4a53-135">**Include web app / web API**</span></span> | <span data-ttu-id="e4a53-136">Sim</span><span class="sxs-lookup"><span data-stu-id="e4a53-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="e4a53-137">**Permitir fluxo implícito**</span><span class="sxs-lookup"><span data-stu-id="e4a53-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="e4a53-138">Sim</span><span class="sxs-lookup"><span data-stu-id="e4a53-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="e4a53-139">**URL de resposta**</span><span class="sxs-lookup"><span data-stu-id="e4a53-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="e4a53-140">URLs de resposta são pontos de extremidade onde o Azure AD B2C retornará os tokens que o aplicativo solicitar.</span><span class="sxs-lookup"><span data-stu-id="e4a53-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="e4a53-141">O Visual Studio fornece a URL de resposta para usar.</span><span class="sxs-lookup"><span data-stu-id="e4a53-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="e4a53-142">Por enquanto, insira `https://localhost:44300/signin-oidc` preencha o formulário.</span><span class="sxs-lookup"><span data-stu-id="e4a53-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="e4a53-143">**URI da ID do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="e4a53-143">**App ID URI**</span></span>                | <span data-ttu-id="e4a53-144">Deixe em branco</span><span class="sxs-lookup"><span data-stu-id="e4a53-144">Leave blank</span></span>               | <span data-ttu-id="e4a53-145">Não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="e4a53-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="e4a53-146">**Incluir cliente nativo**</span><span class="sxs-lookup"><span data-stu-id="e4a53-146">**Include native client**</span></span>     | <span data-ttu-id="e4a53-147">Não</span><span class="sxs-lookup"><span data-stu-id="e4a53-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="e4a53-148">Se estar ciente de como configurar uma URL de resposta não sejam localhost, o [restrições sobre o que é permitido na lista de URL de resposta](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span><span class="sxs-lookup"><span data-stu-id="e4a53-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url).</span></span> 

<span data-ttu-id="e4a53-149">Depois que o aplicativo é registrado, é exibida a lista de aplicativos no locatário.</span><span class="sxs-lookup"><span data-stu-id="e4a53-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="e4a53-150">Selecione o aplicativo que acabou de registrar.</span><span class="sxs-lookup"><span data-stu-id="e4a53-150">Select the app that was just registered.</span></span> <span data-ttu-id="e4a53-151">Selecione o **cópia** ícone à direita do **ID do aplicativo** campo para copiá-lo na área de transferência.</span><span class="sxs-lookup"><span data-stu-id="e4a53-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="e4a53-152">Nada mais podem ser configurado no locatário do Azure AD B2C neste momento, mas deixe a janela do navegador aberta.</span><span class="sxs-lookup"><span data-stu-id="e4a53-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="e4a53-153">Não há mais de configuração depois que o aplicativo ASP.NET Core é criado.</span><span class="sxs-lookup"><span data-stu-id="e4a53-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio-2017"></a><span data-ttu-id="e4a53-154">Criar um aplicativo ASP.NET Core no Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="e4a53-154">Create an ASP.NET Core app in Visual Studio 2017</span></span>

<span data-ttu-id="e4a53-155">O modelo de aplicativo Web Visual Studio pode ser configurado para usar o locatário do Azure AD B2C para autenticação.</span><span class="sxs-lookup"><span data-stu-id="e4a53-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="e4a53-156">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e4a53-156">In Visual Studio:</span></span>

1. <span data-ttu-id="e4a53-157">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4a53-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="e4a53-158">Selecione **aplicativo Web** da lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="e4a53-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="e4a53-159">Selecione o **alterar autenticação** botão.</span><span class="sxs-lookup"><span data-stu-id="e4a53-159">Select the **Change Authentication** button.</span></span>
    
    ![Botão de autenticação de alteração](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="e4a53-161">No **alterar autenticação** caixa de diálogo, selecione **contas de usuário individuais**e, em seguida, selecione **conectar-se ao repositório de usuário existente na nuvem** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="e4a53-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="e4a53-163">Preencha o formulário com os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="e4a53-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="e4a53-164">Configuração</span><span class="sxs-lookup"><span data-stu-id="e4a53-164">Setting</span></span>                       | <span data-ttu-id="e4a53-165">Valor</span><span class="sxs-lookup"><span data-stu-id="e4a53-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="e4a53-166">**Nome de domínio**</span><span class="sxs-lookup"><span data-stu-id="e4a53-166">**Domain Name**</span></span>               | <span data-ttu-id="e4a53-167">*&lt;o nome de domínio do seu locatário do B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="e4a53-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="e4a53-168">**ID do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="e4a53-168">**Application ID**</span></span>            | <span data-ttu-id="e4a53-169">*&lt;Cole a ID do aplicativo da área de transferência&gt;*</span><span class="sxs-lookup"><span data-stu-id="e4a53-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="e4a53-170">**Caminho de retorno de chamada**</span><span class="sxs-lookup"><span data-stu-id="e4a53-170">**Callback Path**</span></span>             | <span data-ttu-id="e4a53-171">*&lt;Use o valor padrão&gt;*</span><span class="sxs-lookup"><span data-stu-id="e4a53-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="e4a53-172">**Política de inscrição ou entrada**</span><span class="sxs-lookup"><span data-stu-id="e4a53-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="e4a53-173">**Política de redefinição de senha**</span><span class="sxs-lookup"><span data-stu-id="e4a53-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="e4a53-174">**Editar política de perfil**</span><span class="sxs-lookup"><span data-stu-id="e4a53-174">**Edit profile policy**</span></span>       | <span data-ttu-id="e4a53-175">*&lt;Deixe em branco&gt;*</span><span class="sxs-lookup"><span data-stu-id="e4a53-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="e4a53-176">Selecione o **cópia** próximo ao link **URI de resposta** para copiar o URI de resposta para a área de transferência.</span><span class="sxs-lookup"><span data-stu-id="e4a53-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="e4a53-177">Selecione **Okey** para fechar o **alterar autenticação** caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="e4a53-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="e4a53-178">Selecione **Okey** para criar o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e4a53-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="e4a53-179">Concluir o registro do aplicativo B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-179">Finish the B2C app registration</span></span>

<span data-ttu-id="e4a53-180">Retornar à janela do navegador com as propriedades do aplicativo B2C ainda abertas.</span><span class="sxs-lookup"><span data-stu-id="e4a53-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="e4a53-181">Alterar temporários **URL de resposta** especificado anteriormente para o valor copiado do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4a53-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="e4a53-182">Selecione **salvar** na parte superior da janela.</span><span class="sxs-lookup"><span data-stu-id="e4a53-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="e4a53-183">Se você não copiar a URL de resposta, use o endereço HTTPS na guia Debug nas propriedades do projeto da web e acrescente o **CallbackPath** o valor da *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e4a53-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="e4a53-184">Configurar políticas</span><span class="sxs-lookup"><span data-stu-id="e4a53-184">Configure policies</span></span>

<span data-ttu-id="e4a53-185">Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)e então [criar uma política de redefinição de senha](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span><span class="sxs-lookup"><span data-stu-id="e4a53-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy).</span></span> <span data-ttu-id="e4a53-186">Use os valores de exemplo fornecidos na documentação do **provedores de identidade**, **atributos de inscrição**, e **declarações do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="e4a53-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="e4a53-187">Usando o **executar agora** botão para testar as políticas, conforme descrito na documentação é opcional.</span><span class="sxs-lookup"><span data-stu-id="e4a53-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="e4a53-188">Verifique se os nomes de política são exatamente como descrito na documentação, como essas políticas foram usadas na **alterar autenticação** caixa de diálogo no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4a53-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="e4a53-189">Os nomes de política podem ser verificados no *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="e4a53-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="e4a53-190">Configure as opções de JwtBearer/OpenIdConnectOptions/Cookie subjacentes</span><span class="sxs-lookup"><span data-stu-id="e4a53-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="e4a53-191">Para configurar as opções subjacentes diretamente, use a constante do esquema apropriado no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e4a53-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="e4a53-192">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="e4a53-192">Run the app</span></span>

<span data-ttu-id="e4a53-193">No Visual Studio, pressione **F5** para compilar e executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a53-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="e4a53-194">Depois que o aplicativo web for iniciado, selecione **Accept** para aceitar o uso de cookies (se solicitado) e, em seguida, selecione **entrar**.</span><span class="sxs-lookup"><span data-stu-id="e4a53-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Entrar no aplicativo](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="e4a53-196">O navegador é redirecionado para o locatário do Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="e4a53-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="e4a53-197">Entrar com uma conta existente (se uma foi criada a testar as políticas) ou selecione **Inscreva-se agora** para criar uma nova conta.</span><span class="sxs-lookup"><span data-stu-id="e4a53-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="e4a53-198">O **esqueceu sua senha?** link é usado para redefinir uma senha esquecida.</span><span class="sxs-lookup"><span data-stu-id="e4a53-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Logon no Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="e4a53-200">Depois de entrar com êxito, o navegador é redirecionado para o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="e4a53-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Êxito](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="e4a53-202">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e4a53-202">Next steps</span></span>

<span data-ttu-id="e4a53-203">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="e4a53-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e4a53-204">Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="e4a53-205">Registrar um aplicativo no Azure B2C do AD</span><span class="sxs-lookup"><span data-stu-id="e4a53-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="e4a53-206">Usar o Visual Studio para criar um aplicativo Web do ASP.NET Core configurados para usar o locatário do Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="e4a53-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="e4a53-207">Configurar políticas para controlar o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="e4a53-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="e4a53-208">Agora que o aplicativo ASP.NET Core está configurado para usar o Azure AD B2C para autenticação, o [atributo Authorize](xref:security/authorization/simple) pode ser usado para proteger seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4a53-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="e4a53-209">Continue a desenvolver seu aplicativo aprendendo a:</span><span class="sxs-lookup"><span data-stu-id="e4a53-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="e4a53-210">[Personalizar a interface do usuário do Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="e4a53-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="e4a53-211">[Configurar os requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="e4a53-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="e4a53-212">[Habilitar a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="e4a53-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="e4a53-213">Configurar provedores de identidade adicional, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [do Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.</span><span class="sxs-lookup"><span data-stu-id="e4a53-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="e4a53-214">[Usar a API do Graph do Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações de usuário adicionais, como associação de grupo, do locatário do Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="e4a53-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="e4a53-215">[Proteger um ASP.NET Core API da web usando o Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span><span class="sxs-lookup"><span data-stu-id="e4a53-215">[Secure an ASP.NET Core web API using Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).</span></span>
* <span data-ttu-id="e4a53-216">[Chamar uma API web de um aplicativo web do .NET usando o Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e4a53-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>
