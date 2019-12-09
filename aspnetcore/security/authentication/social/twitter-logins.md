---
title: Configuração de entrada externa do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944207"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="1bf71-103">Configuração de entrada externa do Twitter com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1bf71-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="1bf71-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1bf71-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1bf71-105">Este exemplo mostra como permitir que os usuários [entrem com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um exemplo ASP.NET Core projeto 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1bf71-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="1bf71-106">Criar o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="1bf71-106">Create the app in Twitter</span></span>

* <span data-ttu-id="1bf71-107">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1bf71-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="1bf71-108">Navegue até [ https://apps.twitter.com/ ](https://apps.twitter.com/) e entre.</span><span class="sxs-lookup"><span data-stu-id="1bf71-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="1bf71-109">Se você ainda não tiver uma conta do Twitter, use o link **[inscrever-se agora](https://twitter.com/signup)** para criar uma.</span><span class="sxs-lookup"><span data-stu-id="1bf71-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="1bf71-110">Selecione **Criar um aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="1bf71-110">Select **Create an app**.</span></span> <span data-ttu-id="1bf71-111">Preencha o **nome do aplicativo**, a **Descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="1bf71-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="1bf71-112">Insira seu URI de desenvolvimento com `/signin-twitter` acrescentado ao campo **URLs de retorno de chamada** (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="1bf71-112">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="1bf71-113">O esquema de autenticação do Twitter configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-twitter` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="1bf71-113">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="1bf71-114">O segmento de URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1bf71-114">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="1bf71-115">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Twitter por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="1bf71-115">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="1bf71-116">Preencha o restante do formulário e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="1bf71-116">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="1bf71-117">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="1bf71-117">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="1bf71-118">Armazenando segredo e chave de API do consumidor do Twitter</span><span class="sxs-lookup"><span data-stu-id="1bf71-118">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="1bf71-119">Execute os comandos a seguir para armazenar com segurança `ClientId` e `ClientSecret` usando o [Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="1bf71-119">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="1bf71-120">Vincular configurações confidenciais como o Twitter `Consumer Key` e `Consumer Secret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1bf71-120">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1bf71-121">Para os fins deste exemplo, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="1bf71-121">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="1bf71-122">Esses tokens podem ser encontrados na guia **chaves e tokens de acesso** após a criação de um novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="1bf71-122">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="1bf71-123">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="1bf71-123">Configure Twitter Authentication</span></span>

<span data-ttu-id="1bf71-124">Adicione o serviço do Twitter no método `ConfigureServices` no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="1bf71-124">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1bf71-125">Consulte a referência de API do [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1bf71-125">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="1bf71-126">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="1bf71-126">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="1bf71-127">Entrar com o Twitter</span><span class="sxs-lookup"><span data-stu-id="1bf71-127">Sign in with Twitter</span></span>

<span data-ttu-id="1bf71-128">Execute o aplicativo e selecione **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="1bf71-128">Run the app and select **Log in**.</span></span> <span data-ttu-id="1bf71-129">É exibida uma opção para entrar com o Twitter:</span><span class="sxs-lookup"><span data-stu-id="1bf71-129">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="1bf71-130">Clicar no **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="1bf71-130">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="1bf71-131">Depois de inserir suas credenciais do Twitter, você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="1bf71-131">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="1bf71-132">Agora você está conectado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="1bf71-132">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="1bf71-133">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="1bf71-133">Troubleshooting</span></span>

* <span data-ttu-id="1bf71-134">Apenas **ASP.NET Core 2.x:** se a identidade não for configurada chamando `services.AddIdentity` no `ConfigureServices`, a autenticação resultará em *ArgumentException: a opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="1bf71-134">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1bf71-135">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="1bf71-135">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="1bf71-136">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="1bf71-136">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1bf71-137">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="1bf71-137">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1bf71-138">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="1bf71-138">Next steps</span></span>

* <span data-ttu-id="1bf71-139">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="1bf71-139">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="1bf71-140">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1bf71-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1bf71-141">Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="1bf71-141">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="1bf71-142">Defina as `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1bf71-142">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1bf71-143">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1bf71-143">The configuration system is set up to read keys from environment variables.</span></span>
