---
title: Configuração de entrada externa com o ASP.NET Core do Twitter
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 486d58b600ca5326a0728de40bb386fbb9440f67
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516880"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="85bc0-103">Configuração de entrada externa com o ASP.NET Core do Twitter</span><span class="sxs-lookup"><span data-stu-id="85bc0-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="85bc0-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85bc0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85bc0-105">Este exemplo mostra como permitir que os usuários [entrar com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um projeto do ASP.NET Core 2.2 de exemplo criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="85bc0-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="85bc0-106">Criar o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="85bc0-106">Create the app in Twitter</span></span>

* <span data-ttu-id="85bc0-107">Navegue até [ https://apps.twitter.com/ ](https://apps.twitter.com/) e entre.</span><span class="sxs-lookup"><span data-stu-id="85bc0-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="85bc0-108">Se você ainda não tiver uma conta do Twitter, use o **[Inscreva-se agora](https://twitter.com/signup)** link para criar um.</span><span class="sxs-lookup"><span data-stu-id="85bc0-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="85bc0-109">Toque **criar novo aplicativo** e preencha o requerimento **nome**, **descrição** e pública **site** URI (Isso pode ser temporário até que você Registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="85bc0-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="85bc0-110">Insira o URI de desenvolvimento com `/signin-twitter` acrescentados em de **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="85bc0-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="85bc0-111">O esquema de autenticação do Twitter configurado adiante neste exemplo automaticamente manipulará as solicitações em `/signin-twitter` rota para implementar o fluxo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="85bc0-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="85bc0-112">O segmento URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="85bc0-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="85bc0-113">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Twitter por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) classe.</span><span class="sxs-lookup"><span data-stu-id="85bc0-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="85bc0-114">Preencha o restante do formulário e toque **criar seu aplicativo Twitter**.</span><span class="sxs-lookup"><span data-stu-id="85bc0-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="85bc0-115">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="85bc0-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="85bc0-116">Armazenar a chave de API de consumidor do Twitter e o segredo</span><span class="sxs-lookup"><span data-stu-id="85bc0-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="85bc0-117">Execute os seguintes comandos para armazenar com segurança `ClientId` e `ClientSecret` usando [Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="85bc0-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

<span data-ttu-id="85bc0-118">Vincular as configurações confidenciais, como o Twitter `Consumer Key` e `Consumer Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="85bc0-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="85bc0-119">Para os fins deste exemplo, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="85bc0-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="85bc0-120">Esses tokens podem ser encontrados na **chaves e Tokens de acesso** guia depois de criar um novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="85bc0-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="85bc0-121">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="85bc0-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="85bc0-122">Adicione o serviço do Twitter na `ConfigureServices` método no *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="85bc0-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="85bc0-123">Consulte a [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="85bc0-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="85bc0-124">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="85bc0-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="85bc0-125">Entrar com o Twitter</span><span class="sxs-lookup"><span data-stu-id="85bc0-125">Sign in with Twitter</span></span>

<span data-ttu-id="85bc0-126">Execute o aplicativo e selecione **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="85bc0-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="85bc0-127">Será exibida uma opção para entrar com o Twitter:</span><span class="sxs-lookup"><span data-stu-id="85bc0-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="85bc0-128">Clicar em **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="85bc0-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="85bc0-129">Depois de inserir suas credenciais do Twitter, você será redirecionado para o site da web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="85bc0-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="85bc0-130">Agora você está conectado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="85bc0-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="85bc0-131">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="85bc0-131">Troubleshooting</span></span>

* <span data-ttu-id="85bc0-132">**ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="85bc0-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="85bc0-133">O modelo de projeto usado neste exemplo garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="85bc0-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="85bc0-134">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="85bc0-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="85bc0-135">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="85bc0-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85bc0-136">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="85bc0-136">Next steps</span></span>

* <span data-ttu-id="85bc0-137">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="85bc0-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="85bc0-138">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="85bc0-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="85bc0-139">Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="85bc0-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="85bc0-140">Defina as `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="85bc0-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="85bc0-141">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="85bc0-141">The configuration system is set up to read keys from environment variables.</span></span>