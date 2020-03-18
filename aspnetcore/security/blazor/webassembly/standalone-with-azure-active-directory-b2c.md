---
title: Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: b4d32e91b4013cbea37baecb972a535d2874d3d1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434454"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="481b8-102">Proteger um aplicativo ASP.NET Core Blazor Webassembly autônomo com Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="481b8-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="481b8-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="481b8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="481b8-104">Para criar um aplicativo autônomo Webassembly Blazor que usa [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="481b8-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="481b8-105">Siga as orientações nos tópicos a seguir para criar um locatário e registrar um aplicativo Web no portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="481b8-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="481b8-106">[Criar um locatário AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; registrar as seguintes informações:</span><span class="sxs-lookup"><span data-stu-id="481b8-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="481b8-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="481b8-107">1\.</span></span> <span data-ttu-id="481b8-108">Instância de AAD B2C (por exemplo, `https://contoso.b2clogin.com/`, que inclui a barra à direita)</span><span class="sxs-lookup"><span data-stu-id="481b8-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="481b8-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="481b8-109">2\.</span></span> <span data-ttu-id="481b8-110">AAD B2C domínio de locatário (por exemplo, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="481b8-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="481b8-111">[Registre um aplicativo web](/azure/active-directory-b2c/tutorial-register-applications) &ndash; faça as seguintes seleções durante o registro do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="481b8-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="481b8-112">1 \.</span><span class="sxs-lookup"><span data-stu-id="481b8-112">1\.</span></span> <span data-ttu-id="481b8-113">Defina **aplicativo Web/API Web** como **Sim**.</span><span class="sxs-lookup"><span data-stu-id="481b8-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="481b8-114">2 \.</span><span class="sxs-lookup"><span data-stu-id="481b8-114">2\.</span></span> <span data-ttu-id="481b8-115">Defina **permitir fluxo implícito** como **Sim**.</span><span class="sxs-lookup"><span data-stu-id="481b8-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="481b8-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="481b8-116">3\.</span></span> <span data-ttu-id="481b8-117">Adicione uma **URL de resposta** de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="481b8-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="481b8-118">Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="481b8-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="481b8-119">[Criar fluxos de usuário](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; criar um fluxo de usuário de inscrição e entrada.</span><span class="sxs-lookup"><span data-stu-id="481b8-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="481b8-120">No mínimo, selecione o atributo **Application claims** > **Display Name** user para preencher o `context.User.Identity.Name` no componente `LoginDisplay` (*Shared/LoginDisplay. Razor*).</span><span class="sxs-lookup"><span data-stu-id="481b8-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="481b8-121">Registre o nome do fluxo de usuário de entrada e de entrada criado para o aplicativo (por exemplo, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="481b8-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="481b8-122">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="481b8-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="481b8-123">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="481b8-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="481b8-124">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="481b8-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="481b8-125">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="481b8-125">Authentication package</span></span>

<span data-ttu-id="481b8-126">Quando um aplicativo é criado para usar uma conta B2C individual (`IndividualB2C`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="481b8-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="481b8-127">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="481b8-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="481b8-128">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="481b8-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="481b8-129">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="481b8-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="481b8-130">O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="481b8-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="481b8-131">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="481b8-131">Authentication service support</span></span>

<span data-ttu-id="481b8-132">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="481b8-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="481b8-133">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="481b8-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="481b8-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="481b8-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="481b8-135">O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="481b8-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="481b8-136">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração do AAD do portal do Azure quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="481b8-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="481b8-137">O modelo Webassembly Blazor não configura automaticamente o aplicativo para solicitar um token de acesso para uma API segura.</span><span class="sxs-lookup"><span data-stu-id="481b8-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="481b8-138">Para provisionar um token como parte do fluxo de entrada, adicione o escopo aos escopos de token de acesso padrão do `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="481b8-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="481b8-139">Página de índice</span><span class="sxs-lookup"><span data-stu-id="481b8-139">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="481b8-140">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="481b8-140">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="481b8-141">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="481b8-141">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="481b8-142">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="481b8-142">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="481b8-143">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="481b8-143">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="481b8-144">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="481b8-144">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="481b8-145">Tutorial: Criar um locatário do Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="481b8-145">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
