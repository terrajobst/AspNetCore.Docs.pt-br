---
title: Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0803e436d66ef7df3c68739e674a8652fde11166
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083842"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="940e7-102">Proteger um aplicativo hospedado do Webassembly ASP.NET Core Blazor com o Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="940e7-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="940e7-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="940e7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="940e7-104">Este artigo descreve como criar um [aplicativo hospedado do WebassemblyBlazor](xref:blazor/hosting-models#blazor-webassembly) que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="940e7-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="940e7-105">Registrar aplicativos em AAD B2C e criar solução</span><span class="sxs-lookup"><span data-stu-id="940e7-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="940e7-106">Criar um locatário</span><span class="sxs-lookup"><span data-stu-id="940e7-106">Create a tenant</span></span>

<span data-ttu-id="940e7-107">Siga as orientações em [início rápido: configurar um locatário](/azure/active-directory/develop/quickstart-create-new-tenant) para criar um locatário no AAD.</span><span class="sxs-lookup"><span data-stu-id="940e7-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="940e7-108">Registrar um aplicativo de API do servidor</span><span class="sxs-lookup"><span data-stu-id="940e7-108">Register a server API app</span></span>

<span data-ttu-id="940e7-109">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo de API do servidor* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="940e7-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="940e7-110">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="940e7-110">Select **New registration**.</span></span>
1. <span data-ttu-id="940e7-111">Forneça um **nome** para o aplicativo (por exemplo, **Blazor servidor AAD**).</span><span class="sxs-lookup"><span data-stu-id="940e7-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="940e7-112">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="940e7-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="940e7-113">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="940e7-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="940e7-114">O *aplicativo de API do servidor* não requer um **URI de redirecionamento** nesse cenário, portanto, deixe a lista suspensa definida como **Web** e não insira um URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="940e7-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="940e7-115">Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .</span><span class="sxs-lookup"><span data-stu-id="940e7-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="940e7-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="940e7-116">Select **Register**.</span></span>

<span data-ttu-id="940e7-117">Em **permissões de API**, remova o **Microsoft Graph** > permissão de **usuário. leitura** , pois o aplicativo não exige o acesso de perfil de entrada ou de UER.</span><span class="sxs-lookup"><span data-stu-id="940e7-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="940e7-118">No **expor uma API**:</span><span class="sxs-lookup"><span data-stu-id="940e7-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="940e7-119">Selecione **Adicionar um escopo**.</span><span class="sxs-lookup"><span data-stu-id="940e7-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="940e7-120">Selecione **Salvar e continuar**.</span><span class="sxs-lookup"><span data-stu-id="940e7-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="940e7-121">Forneça um **nome de escopo** (por exemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="940e7-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="940e7-122">Forneça um **nome de exibição de consentimento do administrador** (por exemplo, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="940e7-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="940e7-123">Forneça uma **Descrição de consentimento do administrador** (por exemplo, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="940e7-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="940e7-124">Confirme se o **estado** está definido como **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="940e7-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="940e7-125">Selecione **Adicionar escopo**.</span><span class="sxs-lookup"><span data-stu-id="940e7-125">Select **Add scope**.</span></span>

<span data-ttu-id="940e7-126">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="940e7-126">Record the following information:</span></span>

* <span data-ttu-id="940e7-127">*Aplicativo de API do servidor* ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="940e7-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="940e7-128">ID do diretório (ID do locatário) (por exemplo, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="940e7-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="940e7-129">Domínio de locatário do AAD (por exemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="940e7-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="940e7-130">Escopo padrão (por exemplo, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="940e7-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="940e7-131">Registrar um aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="940e7-131">Register a client app</span></span>

<span data-ttu-id="940e7-132">Siga as orientações em [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft e os](/azure/active-directory/develop/quickstart-register-app) tópicos subsequentes do Azure AAD para registrar um aplicativo do AAD para o *aplicativo cliente* na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="940e7-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="940e7-133">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="940e7-133">Select **New registration**.</span></span>
1. <span data-ttu-id="940e7-134">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD do cliente**).</span><span class="sxs-lookup"><span data-stu-id="940e7-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="940e7-135">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="940e7-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="940e7-136">Você pode selecionar **contas neste diretório organizacional somente** (locatário único) para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="940e7-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="940e7-137">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="940e7-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="940e7-138">Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .</span><span class="sxs-lookup"><span data-stu-id="940e7-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="940e7-139">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="940e7-139">Select **Register**.</span></span>

<span data-ttu-id="940e7-140">Em **autenticação** > **configurações da plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="940e7-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="940e7-141">Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="940e7-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="940e7-142">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="940e7-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="940e7-143">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="940e7-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="940e7-144">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="940e7-144">Select the **Save** button.</span></span>

<span data-ttu-id="940e7-145">Em **permissões de API**:</span><span class="sxs-lookup"><span data-stu-id="940e7-145">In **API permissions**:</span></span>

1. <span data-ttu-id="940e7-146">Confirme se o aplicativo tem **Microsoft Graph** permissão > **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="940e7-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="940e7-147">Selecione **Adicionar uma permissão** seguida por **minhas APIs**.</span><span class="sxs-lookup"><span data-stu-id="940e7-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="940e7-148">Selecione o *aplicativo de API do servidor* na coluna **nome** (por exemplo, **Blazor servidor AAD**).</span><span class="sxs-lookup"><span data-stu-id="940e7-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="940e7-149">Abra a lista de **APIs** .</span><span class="sxs-lookup"><span data-stu-id="940e7-149">Open the **API** list.</span></span>
1. <span data-ttu-id="940e7-150">Habilite o acesso à API (por exemplo, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="940e7-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="940e7-151">Selecione **Adicionar Permissões**.</span><span class="sxs-lookup"><span data-stu-id="940e7-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="940e7-152">Selecione o botão **conceder conteúdo do administrador para {nome do locatário}** .</span><span class="sxs-lookup"><span data-stu-id="940e7-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="940e7-153">Clique em **Sim** para confirmar.</span><span class="sxs-lookup"><span data-stu-id="940e7-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="940e7-154">Registre a ID do aplicativo de aplicativo *cliente* (ID do cliente) (por exemplo, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="940e7-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="940e7-155">Criar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="940e7-155">Create the app</span></span>

<span data-ttu-id="940e7-156">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="940e7-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="940e7-157">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="940e7-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="940e7-158">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="940e7-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="940e7-159">Consulte a seção [suporte do serviço de autenticação](#Authentication service support) para obter uma alteração de configuração importante para o escopo do token de acesso padrão.</span><span class="sxs-lookup"><span data-stu-id="940e7-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="940e7-160">O valor fornecido pelo modelo Webassembly Blazor deve ser alterado manualmente depois que o *aplicativo cliente* é criado a partir do modelo.</span><span class="sxs-lookup"><span data-stu-id="940e7-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="940e7-161">Configuração de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="940e7-161">Server app configuration</span></span>

<span data-ttu-id="940e7-162">*Esta seção pertence ao aplicativo de **servidor** da solução.*</span><span class="sxs-lookup"><span data-stu-id="940e7-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="940e7-163">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="940e7-163">Authentication package</span></span>

<span data-ttu-id="940e7-164">O suporte para autenticar e autorizar chamadas para ASP.NET Core APIs Web é fornecido pelo `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="940e7-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="940e7-165">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="940e7-165">Authentication service support</span></span>

<span data-ttu-id="940e7-166">O método `AddAuthentication` configura os serviços de autenticação no aplicativo e configura o manipulador de portador JWT como o método de autenticação padrão.</span><span class="sxs-lookup"><span data-stu-id="940e7-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="940e7-167">O método `AddAzureADBearer` configura os parâmetros específicos no manipulador de portador JWT necessário para validar tokens emitidos pelo Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="940e7-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="940e7-168">`UseAuthentication` e `UseAuthorization` garantir que:</span><span class="sxs-lookup"><span data-stu-id="940e7-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="940e7-169">O aplicativo tenta analisar e validar tokens em solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="940e7-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="940e7-170">Qualquer solicitação que tente acessar um recurso protegido sem credenciais adequadas falhará.</span><span class="sxs-lookup"><span data-stu-id="940e7-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="940e7-171">Configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="940e7-171">App settings</span></span>

<span data-ttu-id="940e7-172">O arquivo *appSettings. JSON* contém as opções para configurar o manipulador de portador JWT usado para validar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="940e7-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="940e7-173">Controlador WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="940e7-173">WeatherForecast controller</span></span>

<span data-ttu-id="940e7-174">O controlador WeatherForecast (*Controllers/WeatherForecastController. cs*) expõe uma API protegida com o atributo `[Authorize]` aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="940e7-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="940e7-175">É **importante** entender que:</span><span class="sxs-lookup"><span data-stu-id="940e7-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="940e7-176">O atributo `[Authorize]` nesse controlador de API é a única coisa que protege essa API contra acesso não autorizado.</span><span class="sxs-lookup"><span data-stu-id="940e7-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="940e7-177">O atributo `[Authorize]` usado no aplicativo Webassembly Blazor só serve como uma dica ao aplicativo que o usuário deve estar autorizado para que o aplicativo funcione corretamente.</span><span class="sxs-lookup"><span data-stu-id="940e7-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="940e7-178">Configuração do aplicativo cliente</span><span class="sxs-lookup"><span data-stu-id="940e7-178">Client app configuration</span></span>

<span data-ttu-id="940e7-179">*Esta seção pertence ao aplicativo **cliente** da solução.*</span><span class="sxs-lookup"><span data-stu-id="940e7-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="940e7-180">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="940e7-180">Authentication package</span></span>

<span data-ttu-id="940e7-181">Quando um aplicativo é criado para usar contas corporativas ou de estudante (`SingleOrg`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="940e7-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="940e7-182">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="940e7-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="940e7-183">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="940e7-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="940e7-184">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="940e7-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="940e7-185">O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="940e7-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="940e7-186">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="940e7-186">Authentication service support</span></span>

<span data-ttu-id="940e7-187">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="940e7-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="940e7-188">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="940e7-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="940e7-189">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="940e7-189">*Program.cs*:</span></span>

<span data-ttu-id="940e7-190">Quando o *aplicativo cliente* é gerado, o escopo do token de acesso padrão é do formato `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span><span class="sxs-lookup"><span data-stu-id="940e7-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="940e7-191">**Remova a parte `api://` do valor do escopo.**</span><span class="sxs-lookup"><span data-stu-id="940e7-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="940e7-192">Esse problema será abordado em uma versão de visualização futura.</span><span class="sxs-lookup"><span data-stu-id="940e7-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="940e7-193">O escopo do token de acesso padrão deve estar no formato `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (por exemplo, `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="940e7-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="940e7-194">Se um esquema ou esquema e host for fornecido para a configuração de escopo (como mostrado no portal do Azure), o *aplicativo cliente* lançará uma exceção sem tratamento quando receber uma resposta de *401 não autorizado* do *aplicativo de API do servidor*.</span><span class="sxs-lookup"><span data-stu-id="940e7-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="940e7-195">O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="940e7-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="940e7-196">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="940e7-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="940e7-197">Os escopos de token de acesso padrão representam a lista de escopos de token de acesso que são:</span><span class="sxs-lookup"><span data-stu-id="940e7-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="940e7-198">Incluído por padrão na solicitação de entrada.</span><span class="sxs-lookup"><span data-stu-id="940e7-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="940e7-199">Usado para provisionar um token de acesso imediatamente após a autenticação.</span><span class="sxs-lookup"><span data-stu-id="940e7-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="940e7-200">Todos os escopos devem pertencer ao mesmo aplicativo por regras de Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="940e7-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="940e7-201">Escopos adicionais podem ser adicionados para aplicativos de API adicionais, conforme necessário:</span><span class="sxs-lookup"><span data-stu-id="940e7-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="940e7-202">Página de índice</span><span class="sxs-lookup"><span data-stu-id="940e7-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="940e7-203">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="940e7-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="940e7-204">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="940e7-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="940e7-205">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="940e7-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="940e7-206">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="940e7-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="940e7-207">Componente FetchData</span><span class="sxs-lookup"><span data-stu-id="940e7-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="940e7-208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="940e7-208">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
