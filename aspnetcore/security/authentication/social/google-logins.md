---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 98857a84238124e75d695242c8d421b9a29f02e7
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396101"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d7418-103">Configuração de logon externo do Google no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7418-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d7418-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7418-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7418-105">Em janeiro de 2019 Google começou a [desligar](https://developers.google.com/+/api-shutdown) Google + entrar e os desenvolvedores devem mover para um novo logon do Google no sistema, março.</span><span class="sxs-lookup"><span data-stu-id="d7418-105">In January 2019 Google started to [shut down](https://developers.google.com/+/api-shutdown) Google+ sign in and developers must move to a new Google sign in system by March.</span></span> <span data-ttu-id="d7418-106">O ASP.NET Core 2.1 e 2.2 pacotes para a autenticação do Google serão atualizados em fevereiro para acomodar as alterações.</span><span class="sxs-lookup"><span data-stu-id="d7418-106">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication will be updated in February to accommodate the changes.</span></span> <span data-ttu-id="d7418-107">Para obter mais informações e atenuações temporárias para o ASP.NET Core, consulte [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="d7418-107">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="d7418-108">Este tutorial foi atualizado com o novo processo de instalação.</span><span class="sxs-lookup"><span data-stu-id="d7418-108">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="d7418-109">Este tutorial mostra como habilitar usuários entrar com sua conta do Google usando um projeto ASP.NET Core 2.2 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7418-109">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="d7418-110">Criar uma ID de cliente e o projeto de Console de API do Google</span><span class="sxs-lookup"><span data-stu-id="d7418-110">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="d7418-111">Navegue até [a integração do Google Sign-In em seu aplicativo web](https://developers.google.com/identity/sign-in/web/devconsole-project) e selecione **configurar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="d7418-111">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="d7418-112">No **configurar o cliente do OAuth** caixa de diálogo, selecione **servidor Web**.</span><span class="sxs-lookup"><span data-stu-id="d7418-112">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="d7418-113">No **URIs de redirecionamento autorizados** caixa de entrada de texto, defina o URI de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="d7418-113">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="d7418-114">Por exemplo, `https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="d7418-114">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="d7418-115">Salvar a **ID do cliente** e **segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="d7418-115">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="d7418-116">Ao implantar o site, registrar a nova url pública do **Console do Google**.</span><span class="sxs-lookup"><span data-stu-id="d7418-116">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="d7418-117">Store Google ClientID e ClientSecret</span><span class="sxs-lookup"><span data-stu-id="d7418-117">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="d7418-118">Store as configurações confidenciais, como o Google `Client ID` e `Client Secret` com o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d7418-118">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d7418-119">Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="d7418-119">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

<span data-ttu-id="d7418-120">Você pode gerenciar suas credenciais de API e o uso na [Console de API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="d7418-120">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="d7418-121">Configurar a autenticação do Google</span><span class="sxs-lookup"><span data-stu-id="d7418-121">Configure Google authentication</span></span>

<span data-ttu-id="d7418-122">Adicionar o serviço do Google para `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d7418-122">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="d7418-123">Entrar com o Google</span><span class="sxs-lookup"><span data-stu-id="d7418-123">Sign in with Google</span></span>

* <span data-ttu-id="d7418-124">Execute o aplicativo e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="d7418-124">Run the app and click **Log in**.</span></span> <span data-ttu-id="d7418-125">Aparece uma opção para entrar com o Google.</span><span class="sxs-lookup"><span data-stu-id="d7418-125">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="d7418-126">Clique o **Google** botão, que redireciona para o Google para autenticação.</span><span class="sxs-lookup"><span data-stu-id="d7418-126">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="d7418-127">Depois de inserir suas credenciais do Google, você será redirecionado para o site da web.</span><span class="sxs-lookup"><span data-stu-id="d7418-127">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d7418-128">Consulte a [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="d7418-128">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="d7418-129">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="d7418-129">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="d7418-130">Alterar o retorno de chamada padrão URI</span><span class="sxs-lookup"><span data-stu-id="d7418-130">Change the default callback URI</span></span>

<span data-ttu-id="d7418-131">O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google.</span><span class="sxs-lookup"><span data-stu-id="d7418-131">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="d7418-132">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Google via o herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="d7418-132">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d7418-133">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="d7418-133">Troubleshooting</span></span>

* <span data-ttu-id="d7418-134">Se a entrada não funciona e você não estiver recebendo erros, alterne para modo de desenvolvimento para que o problema mais fácil de depurar.</span><span class="sxs-lookup"><span data-stu-id="d7418-134">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="d7418-135">Se a identidade não estiver configurada, chamando `services.AddIdentity` na `ConfigureServices`, tentar autenticar resulta em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="d7418-135">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d7418-136">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="d7418-136">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d7418-137">Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="d7418-137">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d7418-138">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="d7418-138">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7418-139">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="d7418-139">Next steps</span></span>

* <span data-ttu-id="d7418-140">Este artigo mostrou como você pode autenticar com o Google.</span><span class="sxs-lookup"><span data-stu-id="d7418-140">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="d7418-141">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d7418-141">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="d7418-142">Depois que você publica o aplicativo no Azure, redefinir o `ClientSecret` no Console de API do Google.</span><span class="sxs-lookup"><span data-stu-id="d7418-142">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="d7418-143">Defina as `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="d7418-143">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d7418-144">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d7418-144">The configuration system is set up to read keys from environment variables.</span></span>
