---
title: Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 0083f179f85371d4751fb179194417681fc1a01d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219058"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="6d9bb-102">Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6d9bb-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="6d9bb-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6d9bb-104">Este artigo descreve como criar um aplicativo autônomo do Webassembly Blazor que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="6d9bb-105">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="6d9bb-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="6d9bb-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="6d9bb-106">Create a tenant</span></span>

<span data-ttu-id="6d9bb-107">Siga as orientações em [tutorial: criar um locatário de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) para criar um locatário de AAD B2C e registrar as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="6d9bb-108">Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/`, que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="6d9bb-109">AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="6d9bb-110">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="6d9bb-110">Register a server API app</span></span>

<span data-ttu-id="6d9bb-111">Siga as orientações em [tutorial: registrar um aplicativo no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6d9bb-112">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-112">Select **New registration**.</span></span>
1. <span data-ttu-id="6d9bb-113">Forneça um **nome** para o aplicativo (por exemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6d9bb-114">Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6d9bb-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="6d9bb-115">(multilocatário) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="6d9bb-116">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="6d9bb-117">Confirme se **as permissões** > **conceder ao administrador o OpenID e as permissões de offline_access** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6d9bb-118">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-118">Select **Register**.</span></span>

<span data-ttu-id="6d9bb-119">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="6d9bb-120">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="6d9bb-121">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="6d9bb-122">Forneça um **nome de escopo** (por exemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6d9bb-123">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="6d9bb-124">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="6d9bb-125">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="6d9bb-126">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-126">Select **Add scope**.</span></span>

<span data-ttu-id="6d9bb-127">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-127">Record the following information:</span></span>

* <span data-ttu-id="6d9bb-128">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="6d9bb-129">ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="6d9bb-130">*Aplicativo de API do servidor* URI da ID do aplicativo (por exemplo, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, o portal do Azure pode padronizar o valor para a ID do cliente)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="6d9bb-131">Escopo padrão (por exemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="6d9bb-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="6d9bb-132">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="6d9bb-132">Register a client app</span></span>

<span data-ttu-id="6d9bb-133">Siga as orientações em [tutorial: registrar um aplicativo em Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) novamente para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6d9bb-134">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-134">Select **New registration**.</span></span>
1. <span data-ttu-id="6d9bb-135">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD B2C do cliente**).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="6d9bb-136">Para **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade. Para autenticar usuários com Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="6d9bb-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="6d9bb-137">(multilocatário) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="6d9bb-138">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="6d9bb-139">Confirme se **as permissões** > **conceder ao administrador o OpenID e as permissões de offline_access** estão habilitadas.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="6d9bb-140">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-140">Select **Register**.</span></span>

<span data-ttu-id="6d9bb-141">Em **autenticação** > **configurações da plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6d9bb-142">Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6d9bb-143">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6d9bb-144">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6d9bb-145">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-145">Select the **Save** button.</span></span>

<span data-ttu-id="6d9bb-146">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-146">In **API permissions**:</span></span>

1. <span data-ttu-id="6d9bb-147">Confirme se o aplicativo tem **Microsoft Graph** permissão > **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="6d9bb-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="6d9bb-148">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="6d9bb-149">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="6d9bb-150">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="6d9bb-150">Open the **API** list.</span></span>
1. <span data-ttu-id="6d9bb-151">Habilite o acesso à API (por exemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="6d9bb-152">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="6d9bb-153">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="6d9bb-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="6d9bb-154">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="6d9bb-155">Em **casa** > **Azure ad B2C** **fluxos de usuário** > :</span><span class="sxs-lookup"><span data-stu-id="6d9bb-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="6d9bb-156">Criar um fluxo de usuário de inscrição e entrada</span><span class="sxs-lookup"><span data-stu-id="6d9bb-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="6d9bb-157">No mínimo, selecione o atributo **Application claims** > **Display Name** user para preencher o `context.User.Identity.Name` no componente `LoginDisplay` (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="6d9bb-158">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-158">Record the following information:</span></span>

* <span data-ttu-id="6d9bb-159">Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) (por exemplo, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="6d9bb-160">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="6d9bb-161">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6d9bb-161">Create the app</span></span>

<span data-ttu-id="6d9bb-162">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="6d9bb-163">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6d9bb-164">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="6d9bb-165">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="6d9bb-165">Server app configuration</span></span>

<span data-ttu-id="6d9bb-166">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="6d9bb-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6d9bb-167">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="6d9bb-167">Authentication package</span></span>

<span data-ttu-id="6d9bb-168">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="6d9bb-169">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="6d9bb-169">Authentication service support</span></span>

<span data-ttu-id="6d9bb-170">O método `AddAuthentication` configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="6d9bb-171">O método `AddAzureADBearer` configura os parâmetros específicos no manipulador de portador JWT necessário para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="6d9bb-172">`UseAuthentication` e `UseAuthorization` garantir que:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="6d9bb-173">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="6d9bb-174">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="6d9bb-175">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="6d9bb-175">App settings</span></span>

<span data-ttu-id="6d9bb-176">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="6d9bb-177">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="6d9bb-177">WeatherForecast controller</span></span>

<span data-ttu-id="6d9bb-178">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o atributo `[Authorize]` aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="6d9bb-179">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="6d9bb-180">O atributo `[Authorize]` nesse controlador de API é a única coisa que protege essa API contra acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="6d9bb-181">O atributo `[Authorize]` usado no aplicativo Webassembly Blazor só serve como uma dica ao aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="6d9bb-182">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="6d9bb-182">Client app configuration</span></span>

<span data-ttu-id="6d9bb-183">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="6d9bb-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6d9bb-184">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="6d9bb-184">Authentication package</span></span>

<span data-ttu-id="6d9bb-185">Quando um aplicativo é criado para usar uma conta B2C individual (`IndividualB2C`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6d9bb-186">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6d9bb-187">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="6d9bb-188">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="6d9bb-189">O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="6d9bb-190">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="6d9bb-190">Authentication service support</span></span>

<span data-ttu-id="6d9bb-191">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6d9bb-192">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6d9bb-193">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-193">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="6d9bb-194">O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6d9bb-195">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="6d9bb-196">O modelo Webassembly Blazor configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura para o escopo padrão fornecido ao comando `dotnet new` (`{APP ID URI}/{DEFAULT SCOPE}`).</span><span class="sxs-lookup"><span data-stu-id="6d9bb-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="6d9bb-197">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="6d9bb-198">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="6d9bb-199">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="6d9bb-200">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="6d9bb-201">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="6d9bb-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="6d9bb-202">Página de índice</span><span class="sxs-lookup"><span data-stu-id="6d9bb-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="6d9bb-203">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="6d9bb-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6d9bb-204">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6d9bb-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6d9bb-205">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6d9bb-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="6d9bb-206">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="6d9bb-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6d9bb-207">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="6d9bb-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6d9bb-208">Executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6d9bb-208">Run the app</span></span>

<span data-ttu-id="6d9bb-209">Execute o aplicativo no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="6d9bb-209">Run the app from the Server project.</span></span> <span data-ttu-id="6d9bb-210">Ao usar o Visual Studio, selecione o projeto de servidor no **Gerenciador de soluções** e selecione o botão **executar** na barra de ferramentas ou inicie o aplicativo no menu **depurar** .</span><span class="sxs-lookup"><span data-stu-id="6d9bb-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6d9bb-211">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6d9bb-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="6d9bb-212">Tutorial: Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6d9bb-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
