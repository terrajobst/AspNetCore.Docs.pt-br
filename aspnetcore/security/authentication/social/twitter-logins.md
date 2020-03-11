---
title: Configuração de entrada externa do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário da conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 4710c033018710ce3620f8d7221ae2253b2c0b69
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665924"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="20396-103">Configuração de entrada externa do Twitter com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20396-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="20396-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="20396-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="20396-105">Este exemplo mostra como permitir que os usuários [entrem com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um exemplo ASP.NET Core projeto 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="20396-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="20396-106">Criar o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="20396-106">Create the app in Twitter</span></span>

* <span data-ttu-id="20396-107">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="20396-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="20396-108">Navegue até [https://apps.twitter.com/](https://apps.twitter.com/) e entre.</span><span class="sxs-lookup"><span data-stu-id="20396-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="20396-109">Se você ainda não tiver uma conta do Twitter, use o link **[inscrever-se agora](https://twitter.com/signup)** para criar uma.</span><span class="sxs-lookup"><span data-stu-id="20396-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="20396-110">Selecione **Criar um aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="20396-110">Select **Create an app**.</span></span> <span data-ttu-id="20396-111">Preencha o **nome do aplicativo**, a **Descrição do aplicativo** e o URI do **site** público (isso pode ser temporário até que você registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="20396-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="20396-112">Marque a caixa ao lado de **Habilitar entrada com o Twitter**</span><span class="sxs-lookup"><span data-stu-id="20396-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="20396-113">Microsoft. AspNetCore. Identity exige que os usuários tenham um endereço de email por padrão.</span><span class="sxs-lookup"><span data-stu-id="20396-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="20396-114">Vá para a guia **permissões** , clique no botão **Editar** e marque a caixa ao lado de **solicitar endereço de email dos usuários**.</span><span class="sxs-lookup"><span data-stu-id="20396-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="20396-115">Insira seu URI de desenvolvimento com `/signin-twitter` acrescentado ao campo **URLs de retorno de chamada** (por exemplo: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="20396-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="20396-116">O esquema de autenticação do Twitter configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-twitter` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="20396-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="20396-117">O segmento de URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="20396-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="20396-118">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Twitter por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="20396-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="20396-119">Preencha o restante do formulário e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="20396-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="20396-120">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="20396-120">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="20396-121">Armazenando segredo e chave de API do consumidor do Twitter</span><span class="sxs-lookup"><span data-stu-id="20396-121">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="20396-122">Execute os comandos a seguir para armazenar com segurança `ClientId` e `ClientSecret` usando o [Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="20396-122">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="20396-123">Vincular configurações confidenciais como o Twitter `Consumer Key` e `Consumer Secret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="20396-123">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="20396-124">Para os fins deste exemplo, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="20396-124">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="20396-125">Esses tokens podem ser encontrados na guia **chaves e tokens de acesso** após a criação de um novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="20396-125">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="20396-126">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="20396-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="20396-127">Adicione o serviço do Twitter no método `ConfigureServices` no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="20396-127">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="20396-128">Consulte a referência de API do [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="20396-128">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="20396-129">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="20396-129">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="20396-130">Entrar com o Twitter</span><span class="sxs-lookup"><span data-stu-id="20396-130">Sign in with Twitter</span></span>

<span data-ttu-id="20396-131">Execute o aplicativo e selecione **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="20396-131">Run the app and select **Log in**.</span></span> <span data-ttu-id="20396-132">É exibida uma opção para entrar com o Twitter:</span><span class="sxs-lookup"><span data-stu-id="20396-132">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="20396-133">Clicar no **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="20396-133">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="20396-134">Depois de inserir suas credenciais do Twitter, você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="20396-134">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="20396-135">Agora você está conectado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="20396-135">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="20396-136">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="20396-136">Troubleshooting</span></span>

* <span data-ttu-id="20396-137">**Somente ASP.NET Core 2. x:** Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="20396-137">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="20396-138">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="20396-138">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="20396-139">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="20396-139">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="20396-140">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="20396-140">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="20396-141">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="20396-141">Next steps</span></span>

* <span data-ttu-id="20396-142">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="20396-142">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="20396-143">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="20396-143">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="20396-144">Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="20396-144">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="20396-145">Defina o `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="20396-145">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="20396-146">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="20396-146">The configuration system is set up to read keys from environment variables.</span></span>
