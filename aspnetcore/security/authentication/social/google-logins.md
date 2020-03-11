---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/30/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 83f45143eca1be43410880bfd875a3fce1d2e9c9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667506"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d51c4-103">Configuração de logon externo do Google no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d51c4-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d51c4-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d51c4-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d51c4-105">Este tutorial mostra como permitir que os usuários entrem com sua conta do Google usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d51c4-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="d51c4-106">Criar um projeto do console do Google API e uma ID do cliente</span><span class="sxs-lookup"><span data-stu-id="d51c4-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="d51c4-107">Instale [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="d51c4-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="d51c4-108">Navegue até [integrando o logon do Google ao seu aplicativo Web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selecione **configurar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="d51c4-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="d51c4-109">Na caixa de diálogo **configurar seu cliente OAuth** , selecione **servidor Web**.</span><span class="sxs-lookup"><span data-stu-id="d51c4-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="d51c4-110">Na caixa de entrada de texto **URIs de redirecionamento autorizados** , defina o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="d51c4-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="d51c4-111">Por exemplo, `https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="d51c4-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="d51c4-112">Salve a **ID do cliente** e o **segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="d51c4-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="d51c4-113">Ao implantar o site, registre a nova URL pública do console do **Google**.</span><span class="sxs-lookup"><span data-stu-id="d51c4-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="d51c4-114">Store Google ClientID e ClientSecret</span><span class="sxs-lookup"><span data-stu-id="d51c4-114">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="d51c4-115">Armazene configurações confidenciais, como o Google `Client ID` e `Client Secret` com o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d51c4-115">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d51c4-116">Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="d51c4-116">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d51c4-117">Você pode gerenciar suas credenciais de API e uso no [console de API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="d51c4-117">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="d51c4-118">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="d51c4-118">Configure Google authentication</span></span>

<span data-ttu-id="d51c4-119">Adicione o serviço do Google a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d51c4-119">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="d51c4-120">Entrar com o Google</span><span class="sxs-lookup"><span data-stu-id="d51c4-120">Sign in with Google</span></span>

* <span data-ttu-id="d51c4-121">Execute o aplicativo e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="d51c4-121">Run the app and click **Log in**.</span></span> <span data-ttu-id="d51c4-122">É exibida uma opção para entrar com o Google.</span><span class="sxs-lookup"><span data-stu-id="d51c4-122">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="d51c4-123">Clique no botão **Google** , que redireciona para o Google para autenticação.</span><span class="sxs-lookup"><span data-stu-id="d51c4-123">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="d51c4-124">Depois de inserir suas credenciais do Google, você será Redirecionado de volta para o site da Web.</span><span class="sxs-lookup"><span data-stu-id="d51c4-124">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d51c4-125">Consulte a referência da API <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> para obter mais informações sobre as opções de configuração com suporte pela autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="d51c4-125">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="d51c4-126">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="d51c4-126">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="d51c4-127">Alterar o URI de retorno de chamada padrão</span><span class="sxs-lookup"><span data-stu-id="d51c4-127">Change the default callback URI</span></span>

<span data-ttu-id="d51c4-128">O segmento de URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="d51c4-128">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="d51c4-129">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Google por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [googleoptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="d51c4-129">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d51c4-130">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="d51c4-130">Troubleshooting</span></span>

* <span data-ttu-id="d51c4-131">Se a entrada não funcionar e você não estiver recebendo erros, mude para o modo de desenvolvimento para facilitar a depuração do problema.</span><span class="sxs-lookup"><span data-stu-id="d51c4-131">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="d51c4-132">Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar os resultados em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="d51c4-132">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d51c4-133">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="d51c4-133">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d51c4-134">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="d51c4-134">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d51c4-135">Selecione **aplicar migrações** para criar o banco de dados e atualize a página para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="d51c4-135">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d51c4-136">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d51c4-136">Next steps</span></span>

* <span data-ttu-id="d51c4-137">Este artigo mostrou como você pode autenticar com o Google.</span><span class="sxs-lookup"><span data-stu-id="d51c4-137">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="d51c4-138">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d51c4-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="d51c4-139">Depois de publicar o aplicativo no Azure, redefina o `ClientSecret` no console de API do Google.</span><span class="sxs-lookup"><span data-stu-id="d51c4-139">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="d51c4-140">Defina o `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="d51c4-140">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d51c4-141">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d51c4-141">The configuration system is set up to read keys from environment variables.</span></span>
