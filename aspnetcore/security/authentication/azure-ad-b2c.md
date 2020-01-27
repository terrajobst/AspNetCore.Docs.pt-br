---
title: Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core
author: camsoper
description: Descubra como configurar a autenticação do Azure Active Directory B2C com o ASP.NET Core.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727281"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="d3a47-103">Autenticação de nuvem com Azure Active Directory B2C no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3a47-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="d3a47-104">Por [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="d3a47-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="d3a47-105">[Azure Active Directory B2C do diretório](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) é uma solução de gerenciamento de identidade de nuvem para aplicativos web e móveis.</span><span class="sxs-lookup"><span data-stu-id="d3a47-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="d3a47-106">O serviço fornece autenticação para aplicativos hospedados na nuvem e locais.</span><span class="sxs-lookup"><span data-stu-id="d3a47-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="d3a47-107">Tipos de autenticação incluem contas individuais, contas de rede social e contas corporativas de federado.</span><span class="sxs-lookup"><span data-stu-id="d3a47-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="d3a47-108">Além disso, Azure AD B2C pode fornecer autenticação multifator com configuração mínima.</span><span class="sxs-lookup"><span data-stu-id="d3a47-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="d3a47-109">Azure Active Directory (Azure AD) e o Azure AD B2C são ofertas de produtos separados.</span><span class="sxs-lookup"><span data-stu-id="d3a47-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="d3a47-110">Um locatário do AD do Azure representa uma organização, enquanto que um locatário do Azure AD B2C representa uma coleção de identidades a serem usados com aplicativos de terceira parte confiável.</span><span class="sxs-lookup"><span data-stu-id="d3a47-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="d3a47-111">Para obter mais informações, consulte [do Azure AD B2C: perguntas frequentes (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="d3a47-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="d3a47-112">Neste tutorial, saiba como:</span><span class="sxs-lookup"><span data-stu-id="d3a47-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d3a47-113">Criar um locatário Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="d3a47-114">Registrar um aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="d3a47-115">Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="d3a47-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="d3a47-116">Configurar políticas que controlam o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d3a47-117">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d3a47-117">Prerequisites</span></span>

<span data-ttu-id="d3a47-118">A seguir é necessários para este passo a passo:</span><span class="sxs-lookup"><span data-stu-id="d3a47-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="d3a47-119">Assinatura do Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="d3a47-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="d3a47-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="d3a47-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="d3a47-121">Criar o locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="d3a47-122">Crie um locatário Azure Active Directory B2C [conforme descrito na documentação](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="d3a47-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="d3a47-123">Quando solicitado, associar o locatário com uma assinatura do Azure é opcional para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="d3a47-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="d3a47-124">Registrar o aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="d3a47-125">No locatário Azure AD B2C recém-criado, Registre seu aplicativo usando [as etapas na documentação na](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) seção **registrar um aplicativo Web** .</span><span class="sxs-lookup"><span data-stu-id="d3a47-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="d3a47-126">Parar na **criar um segredo do cliente de aplicativo web** seção.</span><span class="sxs-lookup"><span data-stu-id="d3a47-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="d3a47-127">Um segredo do cliente não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="d3a47-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="d3a47-128">Use os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="d3a47-128">Use the following values:</span></span>

| <span data-ttu-id="d3a47-129">Configuração</span><span class="sxs-lookup"><span data-stu-id="d3a47-129">Setting</span></span>                       | <span data-ttu-id="d3a47-130">Value</span><span class="sxs-lookup"><span data-stu-id="d3a47-130">Value</span></span>                     | <span data-ttu-id="d3a47-131">{1&gt;Observações&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d3a47-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d3a47-132">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d3a47-132">**Name**</span></span>                      | <span data-ttu-id="d3a47-133">*nome do aplicativo &lt;&gt;*</span><span class="sxs-lookup"><span data-stu-id="d3a47-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="d3a47-134">Insira um **nome** para o aplicativo que descrevem seu aplicativo para os consumidores.</span><span class="sxs-lookup"><span data-stu-id="d3a47-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="d3a47-135">**Incluir aplicativo web / API web**</span><span class="sxs-lookup"><span data-stu-id="d3a47-135">**Include web app / web API**</span></span> | <span data-ttu-id="d3a47-136">Sim</span><span class="sxs-lookup"><span data-stu-id="d3a47-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="d3a47-137">**Permitir fluxo implícito**</span><span class="sxs-lookup"><span data-stu-id="d3a47-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="d3a47-138">Sim</span><span class="sxs-lookup"><span data-stu-id="d3a47-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="d3a47-139">**URL de resposta**</span><span class="sxs-lookup"><span data-stu-id="d3a47-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="d3a47-140">URLs de resposta são pontos de extremidade onde o Azure AD B2C retornará os tokens que o aplicativo solicitar.</span><span class="sxs-lookup"><span data-stu-id="d3a47-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="d3a47-141">O Visual Studio fornece a URL de resposta a ser usada.</span><span class="sxs-lookup"><span data-stu-id="d3a47-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="d3a47-142">Por enquanto, insira `https://localhost:44300/signin-oidc` para preencher o formulário.</span><span class="sxs-lookup"><span data-stu-id="d3a47-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="d3a47-143">**URI da ID do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="d3a47-143">**App ID URI**</span></span>                | <span data-ttu-id="d3a47-144">Deixar em branco</span><span class="sxs-lookup"><span data-stu-id="d3a47-144">Leave blank</span></span>               | <span data-ttu-id="d3a47-145">Não é necessário para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="d3a47-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="d3a47-146">**Incluir cliente nativo**</span><span class="sxs-lookup"><span data-stu-id="d3a47-146">**Include native client**</span></span>     | <span data-ttu-id="d3a47-147">Não</span><span class="sxs-lookup"><span data-stu-id="d3a47-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="d3a47-148">Se estiver configurando uma URL de resposta não localhost, esteja ciente das [restrições sobre o que é permitido na lista URL de resposta](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="d3a47-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="d3a47-149">Depois que o aplicativo é registrado, a lista de aplicativos no locatário é exibida.</span><span class="sxs-lookup"><span data-stu-id="d3a47-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="d3a47-150">Selecione o aplicativo que acabou de ser registrado.</span><span class="sxs-lookup"><span data-stu-id="d3a47-150">Select the app that was just registered.</span></span> <span data-ttu-id="d3a47-151">Selecione o **cópia** ícone à direita do **ID do aplicativo** campo para copiá-lo na área de transferência.</span><span class="sxs-lookup"><span data-stu-id="d3a47-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="d3a47-152">Nada mais pode ser configurado no locatário Azure AD B2C neste momento, mas deixe a janela do navegador aberta.</span><span class="sxs-lookup"><span data-stu-id="d3a47-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="d3a47-153">Há mais configuração após a criação do aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3a47-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="d3a47-154">Criar um aplicativo ASP.NET Core no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3a47-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="d3a47-155">O modelo de aplicativo Web Visual Studio pode ser configurado para usar o locatário do Azure AD B2C para autenticação.</span><span class="sxs-lookup"><span data-stu-id="d3a47-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="d3a47-156">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="d3a47-156">In Visual Studio:</span></span>

1. <span data-ttu-id="d3a47-157">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3a47-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="d3a47-158">Selecione **aplicativo Web** na lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="d3a47-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="d3a47-159">Selecione o **alterar autenticação** botão.</span><span class="sxs-lookup"><span data-stu-id="d3a47-159">Select the **Change Authentication** button.</span></span>
    
    ![Botão de autenticação de alteração](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="d3a47-161">Na caixa de diálogo **alterar autenticação** , selecione **contas de usuário individuais**e, em seguida, selecione **conectar a um repositório de usuários existente na nuvem** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="d3a47-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Caixa de diálogo Alterar autenticação](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="d3a47-163">Preencha o formulário com os seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="d3a47-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="d3a47-164">Configuração</span><span class="sxs-lookup"><span data-stu-id="d3a47-164">Setting</span></span>                       | <span data-ttu-id="d3a47-165">Value</span><span class="sxs-lookup"><span data-stu-id="d3a47-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="d3a47-166">**Nome de domínio**</span><span class="sxs-lookup"><span data-stu-id="d3a47-166">**Domain Name**</span></span>               | <span data-ttu-id="d3a47-167">*&lt;o nome de domínio do seu locatário B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="d3a47-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="d3a47-168">**ID do aplicativo**</span><span class="sxs-lookup"><span data-stu-id="d3a47-168">**Application ID**</span></span>            | <span data-ttu-id="d3a47-169">*&lt;colar a ID do aplicativo da área de transferência&gt;*</span><span class="sxs-lookup"><span data-stu-id="d3a47-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="d3a47-170">**Caminho de retorno de chamada**</span><span class="sxs-lookup"><span data-stu-id="d3a47-170">**Callback Path**</span></span>             | <span data-ttu-id="d3a47-171">*&lt;usar o valor padrão&gt;*</span><span class="sxs-lookup"><span data-stu-id="d3a47-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="d3a47-172">**Política de inscrição ou entrada**</span><span class="sxs-lookup"><span data-stu-id="d3a47-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="d3a47-173">**Redefinir política de senha**</span><span class="sxs-lookup"><span data-stu-id="d3a47-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="d3a47-174">**Editar política de perfil**</span><span class="sxs-lookup"><span data-stu-id="d3a47-174">**Edit profile policy**</span></span>       | <span data-ttu-id="d3a47-175">*&lt;deixar em branco&gt;*</span><span class="sxs-lookup"><span data-stu-id="d3a47-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="d3a47-176">Selecione o link **copiar** ao lado de **URI de resposta** para copiar o URI de resposta para a área de transferência.</span><span class="sxs-lookup"><span data-stu-id="d3a47-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="d3a47-177">Selecione **Okey** para fechar o **alterar autenticação** caixa de diálogo.</span><span class="sxs-lookup"><span data-stu-id="d3a47-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="d3a47-178">Selecione **Okey** para criar o aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="d3a47-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="d3a47-179">Concluir o registro do aplicativo B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-179">Finish the B2C app registration</span></span>

<span data-ttu-id="d3a47-180">Retorne à janela do navegador com as propriedades do aplicativo B2C ainda abertas.</span><span class="sxs-lookup"><span data-stu-id="d3a47-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="d3a47-181">Altere a **URL de resposta** temporária especificada anteriormente para o valor copiado do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d3a47-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="d3a47-182">Selecione **salvar** na parte superior da janela.</span><span class="sxs-lookup"><span data-stu-id="d3a47-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="d3a47-183">Se você não tiver copiado a URL de resposta, use o endereço HTTPS da guia Depurar nas propriedades do projeto Web e acrescente o valor **CallbackPath** de *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d3a47-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="d3a47-184">Configurar políticas</span><span class="sxs-lookup"><span data-stu-id="d3a47-184">Configure policies</span></span>

<span data-ttu-id="d3a47-185">Use as etapas na documentação do Azure AD B2C para [criar uma política de inscrição ou de entrada](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)e, em seguida, [crie uma política de redefinição de senha](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="d3a47-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="d3a47-186">Use os valores de exemplo fornecidos na documentação do **provedores de identidade**, **atributos de inscrição**, e **declarações do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="d3a47-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="d3a47-187">Usar o botão **executar agora** para testar as políticas, conforme descrito na documentação é opcional.</span><span class="sxs-lookup"><span data-stu-id="d3a47-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="d3a47-188">Verifique se os nomes de política são exatamente conforme descrito na documentação, pois essas políticas foram usadas na caixa de diálogo **alterar autenticação** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d3a47-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="d3a47-189">Os nomes de política podem ser verificados em *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="d3a47-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="d3a47-190">Configurar as opções subjacentes de OpenIdConnectOptions/JwtBearer/cookie</span><span class="sxs-lookup"><span data-stu-id="d3a47-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="d3a47-191">Para configurar as opções subjacentes diretamente, use a constante de esquema apropriada no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d3a47-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

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

## <a name="run-the-app"></a><span data-ttu-id="d3a47-192">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d3a47-192">Run the app</span></span>

<span data-ttu-id="d3a47-193">No Visual Studio, pressione **F5** para compilar e executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3a47-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="d3a47-194">Depois que o aplicativo Web for iniciado, selecione **aceitar** para aceitar o uso de cookies (se solicitado) e, em seguida, selecione **entrar**.</span><span class="sxs-lookup"><span data-stu-id="d3a47-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Entrar no aplicativo](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="d3a47-196">O navegador redireciona para o locatário do Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="d3a47-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="d3a47-197">Entrar com uma conta existente (se uma foi criada a testar as políticas) ou selecione **Inscreva-se agora** para criar uma nova conta.</span><span class="sxs-lookup"><span data-stu-id="d3a47-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="d3a47-198">O **esqueceu sua senha?** link é usado para redefinir uma senha esquecida.</span><span class="sxs-lookup"><span data-stu-id="d3a47-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Logon Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="d3a47-200">Depois de entrar com êxito, o navegador redireciona para o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="d3a47-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Êxito](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="d3a47-202">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d3a47-202">Next steps</span></span>

<span data-ttu-id="d3a47-203">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="d3a47-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d3a47-204">Criar um locatário Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="d3a47-205">Registrar um aplicativo no Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="d3a47-206">Use o Visual Studio para criar um aplicativo Web ASP.NET Core configurado para usar o locatário Azure AD B2C para autenticação</span><span class="sxs-lookup"><span data-stu-id="d3a47-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="d3a47-207">Configurar políticas que controlam o comportamento do locatário do Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="d3a47-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="d3a47-208">Agora que o aplicativo ASP.NET Core está configurado para usar Azure AD B2C para autenticação, o [atributo autorizar](xref:security/authorization/simple) pode ser usado para proteger seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3a47-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="d3a47-209">Continue desenvolvendo seu aplicativo aprendendo a:</span><span class="sxs-lookup"><span data-stu-id="d3a47-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="d3a47-210">[Personalizar a interface do usuário do Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="d3a47-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="d3a47-211">[Configurar os requisitos de complexidade de senha](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="d3a47-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="d3a47-212">[Habilitar a autenticação multifator](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="d3a47-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="d3a47-213">Configurar provedores de identidade adicional, como [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [do Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)e outros.</span><span class="sxs-lookup"><span data-stu-id="d3a47-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="d3a47-214">[Usar a API do Graph do Azure AD](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) para recuperar informações de usuário adicionais, como associação de grupo, do locatário do Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="d3a47-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="d3a47-215">[Proteger uma API Web do ASP.NET Core usando Azure ad B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span><span class="sxs-lookup"><span data-stu-id="d3a47-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="d3a47-216">[Chamar uma API web de um aplicativo web do .NET usando o Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="d3a47-216">[Call a .NET web API from a .NET web app using Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).</span></span>