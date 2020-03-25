---
title: Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: e12c38ed42a4e2714d785ef8f03097246c40d36e
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218967"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="46960-102">Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="46960-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="46960-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="46960-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="46960-104">Para criar um aplicativo autônomo Webassembly Blazor que usa [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="46960-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="46960-105">[Criar um locatário do AAD e um aplicativo Web](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="46960-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="46960-106">Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="46960-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="46960-107">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD do cliente**).</span><span class="sxs-lookup"><span data-stu-id="46960-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="46960-108">Escolha um **tipo de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="46960-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="46960-109">Você pode selecionar **contas neste diretório organizacional somente** para esta experiência.</span><span class="sxs-lookup"><span data-stu-id="46960-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="46960-110">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="46960-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="46960-111">Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .</span><span class="sxs-lookup"><span data-stu-id="46960-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="46960-112">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="46960-112">Select **Register**.</span></span>

<span data-ttu-id="46960-113">Em **autenticação** > **configurações da plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="46960-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="46960-114">Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="46960-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="46960-115">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="46960-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="46960-116">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="46960-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="46960-117">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="46960-117">Select the **Save** button.</span></span>

<span data-ttu-id="46960-118">Registre as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="46960-118">Record the following information:</span></span>

* <span data-ttu-id="46960-119">ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="46960-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="46960-120">ID do diretório (ID do locatário) (por exemplo, `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="46960-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="46960-121">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="46960-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="46960-122">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="46960-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="46960-123">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="46960-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="46960-124">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="46960-124">Authentication package</span></span>

<span data-ttu-id="46960-125">Quando um aplicativo é criado para usar contas corporativas ou de estudante (`SingleOrg`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="46960-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="46960-126">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="46960-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="46960-127">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="46960-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="46960-128">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="46960-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="46960-129">O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46960-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="46960-130">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="46960-130">Authentication service support</span></span>

<span data-ttu-id="46960-131">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="46960-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="46960-132">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="46960-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="46960-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="46960-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="46960-134">O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46960-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="46960-135">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46960-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="46960-136">O modelo Webassembly Blazor não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="46960-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="46960-137">Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="46960-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="46960-138">O escopo do token de acesso padrão deve estar no formato `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (por exemplo, `11111111-1111-1111-1111-111111111111/API.Access`).</span><span class="sxs-lookup"><span data-stu-id="46960-138">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="46960-139">Se um esquema ou esquema e host for fornecido para a configuração de escopo (como mostrado no portal do Azure), o *aplicativo cliente* lançará uma exceção sem tratamento quando receber uma resposta de *401 não autorizado* do *aplicativo de API do servidor*.</span><span class="sxs-lookup"><span data-stu-id="46960-139">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

## <a name="index-page"></a><span data-ttu-id="46960-140">Página de índice</span><span class="sxs-lookup"><span data-stu-id="46960-140">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="46960-141">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="46960-141">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="46960-142">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="46960-142">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="46960-143">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="46960-143">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="46960-144">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="46960-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="46960-145">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="46960-145">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
