---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082482"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="e7c65-103">Configuração de logon externo do Google no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7c65-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="e7c65-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7c65-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e7c65-105">[As APIs do Google + herdadas foram desligadas a partir de 7 de março de 2019](https://developers.google.com/+/api-shutdown).</span><span class="sxs-lookup"><span data-stu-id="e7c65-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="e7c65-106">Entre no Google + e os desenvolvedores devem mudar para um novo sistema de entrada do Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="e7c65-107">Os pacotes ASP.NET Core 2,1 e 2,2 para a autenticação do Google foram atualizados para acomodar as alterações.</span><span class="sxs-lookup"><span data-stu-id="e7c65-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="e7c65-108">Para obter mais informações e atenuações temporárias para ASP.NET Core, consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="e7c65-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="e7c65-109">Este tutorial foi atualizado com o novo processo de instalação.</span><span class="sxs-lookup"><span data-stu-id="e7c65-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="e7c65-110">Este tutorial mostra como permitir que os usuários entrem com sua conta do Google usando o projeto ASP.NET Core 2,2 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e7c65-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="e7c65-111">Criar um projeto do console do Google API e uma ID do cliente</span><span class="sxs-lookup"><span data-stu-id="e7c65-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="e7c65-112">Navegue até [integrando o logon do Google ao seu aplicativo Web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selecione **configurar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="e7c65-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="e7c65-113">Na caixa de diálogo **configurar seu cliente OAuth** , selecione **servidor Web**.</span><span class="sxs-lookup"><span data-stu-id="e7c65-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="e7c65-114">Na caixa de entrada de texto **URIs de redirecionamento autorizados** , defina o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="e7c65-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="e7c65-115">Por exemplo, `https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="e7c65-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="e7c65-116">Salve a **ID do cliente** e o **segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="e7c65-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="e7c65-117">Ao implantar o site, registre a nova URL pública do console do **Google**.</span><span class="sxs-lookup"><span data-stu-id="e7c65-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="e7c65-118">Store Google ClientID e ClientSecret</span><span class="sxs-lookup"><span data-stu-id="e7c65-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="e7c65-119">Armazene configurações confidenciais, como o `Client ID` Google `Client Secret` e com o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e7c65-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e7c65-120">Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="e7c65-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e7c65-121">Você pode gerenciar suas credenciais de API e uso no [console de API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="e7c65-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="e7c65-122">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="e7c65-122">Configure Google authentication</span></span>

<span data-ttu-id="e7c65-123">Adicione o serviço do Google `Startup.ConfigureServices`a:</span><span class="sxs-lookup"><span data-stu-id="e7c65-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="e7c65-124">Entrar com o Google</span><span class="sxs-lookup"><span data-stu-id="e7c65-124">Sign in with Google</span></span>

* <span data-ttu-id="e7c65-125">Execute o aplicativo e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="e7c65-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="e7c65-126">É exibida uma opção para entrar com o Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="e7c65-127">Clique no botão **Google** , que redireciona para o Google para autenticação.</span><span class="sxs-lookup"><span data-stu-id="e7c65-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="e7c65-128">Depois de inserir suas credenciais do Google, você será Redirecionado de volta para o site da Web.</span><span class="sxs-lookup"><span data-stu-id="e7c65-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="e7c65-129">Consulte a <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> referência da API para obter mais informações sobre as opções de configuração com suporte pela autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="e7c65-130">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="e7c65-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="e7c65-131">Alterar o URI de retorno de chamada padrão</span><span class="sxs-lookup"><span data-stu-id="e7c65-131">Change the default callback URI</span></span>

<span data-ttu-id="e7c65-132">O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="e7c65-133">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Google via o herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="e7c65-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e7c65-134">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="e7c65-134">Troubleshooting</span></span>

* <span data-ttu-id="e7c65-135">Se a entrada não funcionar e você não estiver recebendo erros, mude para o modo de desenvolvimento para facilitar a depuração do problema.</span><span class="sxs-lookup"><span data-stu-id="e7c65-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="e7c65-136">Se a identidade não estiver configurada chamando `services.AddIdentity` em `ConfigureServices`, tentar autenticar *os resultados em ArgumentException: A opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="e7c65-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e7c65-137">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="e7c65-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="e7c65-138">Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="e7c65-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e7c65-139">Selecione **aplicar migrações** para criar o banco de dados e atualize a página para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="e7c65-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7c65-140">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e7c65-140">Next steps</span></span>

* <span data-ttu-id="e7c65-141">Este artigo mostrou como você pode autenticar com o Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="e7c65-142">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e7c65-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="e7c65-143">Depois de publicar o aplicativo no Azure, redefina `ClientSecret` o no console de API do Google.</span><span class="sxs-lookup"><span data-stu-id="e7c65-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="e7c65-144">Defina as `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="e7c65-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e7c65-145">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e7c65-145">The configuration system is set up to read keys from environment variables.</span></span>
