---
title: Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com contas da Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219253"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="1cddc-102">Proteger um ASP.NET Core aplicativo autônomo Webassembly Blazor com contas da Microsoft</span><span class="sxs-lookup"><span data-stu-id="1cddc-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="1cddc-103">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1cddc-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1cddc-104">Para criar um aplicativo autônomo Webassembly Blazor que usa [contas da Microsoft com Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) para autenticação:</span><span class="sxs-lookup"><span data-stu-id="1cddc-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="1cddc-105">Criar um locatário do AAD e um aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="1cddc-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="1cddc-106">Registre um aplicativo do AAD na área **Azure Active Directory** > **registros de aplicativo** do portal do Azure:</span><span class="sxs-lookup"><span data-stu-id="1cddc-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="1cddc-107">1 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-107">1\.</span></span> <span data-ttu-id="1cddc-108">Forneça um **nome** para o aplicativo (por exemplo, **Blazor AAD do cliente**).</span><span class="sxs-lookup"><span data-stu-id="1cddc-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="1cddc-109">2 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-109">2\.</span></span> <span data-ttu-id="1cddc-110">Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional**.</span><span class="sxs-lookup"><span data-stu-id="1cddc-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="1cddc-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-111">3\.</span></span> <span data-ttu-id="1cddc-112">Deixe a lista suspensa **URI de redirecionamento** definida como **Web**e forneça um URI de redirecionamento de `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="1cddc-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="1cddc-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-113">4\.</span></span> <span data-ttu-id="1cddc-114">Desabilite as **permissões** > **conceder administrador concento para OpenID e caixa de seleção permissões de offline_access** .</span><span class="sxs-lookup"><span data-stu-id="1cddc-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="1cddc-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-115">5\.</span></span> <span data-ttu-id="1cddc-116">Selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="1cddc-116">Select **Register**.</span></span>

   <span data-ttu-id="1cddc-117">Em **autenticação** > **configurações da plataforma** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="1cddc-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="1cddc-118">1 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-118">1\.</span></span> <span data-ttu-id="1cddc-119">Confirme se o **URI de redirecionamento** do `https://localhost:5001/authentication/login-callback` está presente.</span><span class="sxs-lookup"><span data-stu-id="1cddc-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="1cddc-120">2 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-120">2\.</span></span> <span data-ttu-id="1cddc-121">Para **concessão implícita**, marque as caixas de seleção para **tokens de acesso** e **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="1cddc-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="1cddc-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-122">3\.</span></span> <span data-ttu-id="1cddc-123">Os padrões restantes para o aplicativo são aceitáveis para essa experiência.</span><span class="sxs-lookup"><span data-stu-id="1cddc-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="1cddc-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="1cddc-124">4\.</span></span> <span data-ttu-id="1cddc-125">Selecione o botão **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="1cddc-125">Select the **Save** button.</span></span>

   <span data-ttu-id="1cddc-126">Registre a ID do aplicativo (ID do cliente) (por exemplo, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="1cddc-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="1cddc-127">Substitua os espaços reservados no comando a seguir pelas informações registradas anteriormente e execute o comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1cddc-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="1cddc-128">Para especificar o local de saída, que cria uma pasta de projeto, se ela não existir, inclua a opção de saída no comando com um caminho (por exemplo, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="1cddc-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1cddc-129">O nome da pasta também se torna parte do nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="1cddc-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1cddc-130">Depois de criar o aplicativo, você deve ser capaz de:</span><span class="sxs-lookup"><span data-stu-id="1cddc-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="1cddc-131">Faça logon no aplicativo usando uma conta da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1cddc-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="1cddc-132">Solicite tokens de acesso para APIs da Microsoft usando a mesma abordagem que para aplicativos Blazor autônomos desde que você tenha configurado o aplicativo corretamente.</span><span class="sxs-lookup"><span data-stu-id="1cddc-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="1cddc-133">Para obter mais informações, consulte [início rápido: configurar um aplicativo para expor APIs Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="1cddc-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1cddc-134">Pacote de autenticação</span><span class="sxs-lookup"><span data-stu-id="1cddc-134">Authentication package</span></span>

<span data-ttu-id="1cddc-135">Quando um aplicativo é criado para usar contas corporativas ou de estudante (`SingleOrg`), o aplicativo recebe automaticamente uma referência de pacote para a [biblioteca de autenticação da Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="1cddc-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1cddc-136">O pacote fornece um conjunto de primitivos que ajudam o aplicativo a autenticar usuários e obter tokens para chamar APIs protegidas.</span><span class="sxs-lookup"><span data-stu-id="1cddc-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1cddc-137">Se estiver adicionando autenticação a um aplicativo, adicione manualmente o pacote ao arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="1cddc-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1cddc-138">Substitua `{VERSION}` na referência do pacote anterior pela versão do pacote `Microsoft.AspNetCore.Blazor.Templates` mostrado no artigo <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="1cddc-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1cddc-139">O pacote de `Microsoft.Authentication.WebAssembly.Msal` adiciona transitivamente o pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1cddc-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1cddc-140">Suporte ao serviço de autenticação</span><span class="sxs-lookup"><span data-stu-id="1cddc-140">Authentication service support</span></span>

<span data-ttu-id="1cddc-141">O suporte para autenticação de usuários é registrado no contêiner de serviço com o método de extensão `AddMsalAuthentication` fornecido pelo pacote `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="1cddc-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1cddc-142">Esse método configura todos os serviços necessários para que o aplicativo interaja com o provedor de identidade (IP).</span><span class="sxs-lookup"><span data-stu-id="1cddc-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1cddc-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1cddc-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="1cddc-144">O método `AddMsalAuthentication` aceita um retorno de chamada para configurar os parâmetros necessários para autenticar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1cddc-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1cddc-145">Os valores necessários para configurar o aplicativo podem ser obtidos na configuração de contas da Microsoft quando você registra o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1cddc-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="1cddc-146">Página de índice</span><span class="sxs-lookup"><span data-stu-id="1cddc-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1cddc-147">Componente do aplicativo</span><span class="sxs-lookup"><span data-stu-id="1cddc-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1cddc-148">Componente RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="1cddc-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1cddc-149">Componente LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="1cddc-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1cddc-150">Componente de autenticação</span><span class="sxs-lookup"><span data-stu-id="1cddc-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1cddc-151">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="1cddc-151">Additional resources</span></span>

* [<span data-ttu-id="1cddc-152">Início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft</span><span class="sxs-lookup"><span data-stu-id="1cddc-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="1cddc-153">Início rápido: configurar um aplicativo para expor APIs Web</span><span class="sxs-lookup"><span data-stu-id="1cddc-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
