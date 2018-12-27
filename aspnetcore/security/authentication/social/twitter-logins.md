---
title: Configuração de logon externo do Twitter com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Twitter em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/twitter-logins
ms.openlocfilehash: 49db8b921fde169380ca284f46e535786b2b8a30
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735798"
---
# <a name="twitter-external-login-setup-with-aspnet-core"></a><span data-ttu-id="b3189-103">Configuração de logon externo do Twitter com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3189-103">Twitter external login setup with ASP.NET Core</span></span>

<span data-ttu-id="b3189-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b3189-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b3189-105">Este tutorial mostra como permitir que os usuários [entrar com sua conta do Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) usando um projeto do ASP.NET Core 2.0 de exemplo criado sobre o [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b3189-105">This tutorial shows you how to enable your users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="b3189-106">Criar o aplicativo no Twitter</span><span class="sxs-lookup"><span data-stu-id="b3189-106">Create the app in Twitter</span></span>

* <span data-ttu-id="b3189-107">Navegue até [ https://apps.twitter.com/ ](https://apps.twitter.com/) e entre.</span><span class="sxs-lookup"><span data-stu-id="b3189-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="b3189-108">Se você ainda não tiver uma conta do Twitter, use o **[Inscreva-se agora](https://twitter.com/signup)** link para criar um.</span><span class="sxs-lookup"><span data-stu-id="b3189-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span> <span data-ttu-id="b3189-109">Depois de entrar, o **gerenciamento de aplicativos** página é mostrada:</span><span class="sxs-lookup"><span data-stu-id="b3189-109">After signing in, the **Application Management** page is shown:</span></span>

  ![Gerenciamento de aplicativo do Twitter aberto no Microsoft Edge](index/_static/TwitterAppManage.png)

* <span data-ttu-id="b3189-111">Toque **criar novo aplicativo** e preencha o requerimento **nome**, **descrição** e pública **site** URI (Isso pode ser temporário até que você Registre o nome de domínio):</span><span class="sxs-lookup"><span data-stu-id="b3189-111">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

  ![Criar uma página de aplicativo](index/_static/TwitterCreate.png)

* <span data-ttu-id="b3189-113">Insira o URI de desenvolvimento com `/signin-twitter` acrescentados em de **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://localhost:44320/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="b3189-113">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-twitter`).</span></span> <span data-ttu-id="b3189-114">O esquema de autenticação do Twitter configurado mais tarde neste tutorial automaticamente manipulará as solicitações em `/signin-twitter` rota para implementar o fluxo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="b3189-114">The Twitter authentication scheme configured later in this tutorial will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b3189-115">O segmento URI `/signin-twitter` é definido como o retorno de chamada padrão do provedor de autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="b3189-115">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="b3189-116">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Twitter por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) classe.</span><span class="sxs-lookup"><span data-stu-id="b3189-116">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="b3189-117">Preencha o restante do formulário e toque **criar seu aplicativo Twitter**.</span><span class="sxs-lookup"><span data-stu-id="b3189-117">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="b3189-118">Novos detalhes do aplicativo são exibidos:</span><span class="sxs-lookup"><span data-stu-id="b3189-118">New application details are displayed:</span></span>

  ![Guia Detalhes na página de aplicativo](index/_static/TwitterAppDetails.png)

* <span data-ttu-id="b3189-120">Ao implantar o site será necessário rever a **gerenciamento de aplicativos** página e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="b3189-120">When deploying the site you'll need to revisit the **Application Management** page and register a new public URI.</span></span>

## <a name="storing-twitter-consumerkey-and-consumersecret"></a><span data-ttu-id="b3189-121">Armazenar Twitter ConsumerKey e ConsumerSecret</span><span class="sxs-lookup"><span data-stu-id="b3189-121">Storing Twitter ConsumerKey and ConsumerSecret</span></span>

<span data-ttu-id="b3189-122">Vincular as configurações confidenciais, como o Twitter `Consumer Key` e `Consumer Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b3189-122">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b3189-123">Para os fins deste tutorial, nomeie os tokens `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret`.</span><span class="sxs-lookup"><span data-stu-id="b3189-123">For the purposes of this tutorial, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="b3189-124">Esses tokens podem ser encontrados na **chaves e Tokens de acesso** guia depois de criar seu novo aplicativo do Twitter:</span><span class="sxs-lookup"><span data-stu-id="b3189-124">These tokens can be found on the **Keys and Access Tokens** tab after creating your new Twitter application:</span></span>

![Guia chaves e Tokens de acesso](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a><span data-ttu-id="b3189-126">Configurar a autenticação do Twitter</span><span class="sxs-lookup"><span data-stu-id="b3189-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="b3189-127">O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) pacote já está instalado.</span><span class="sxs-lookup"><span data-stu-id="b3189-127">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) package is already installed.</span></span>

* <span data-ttu-id="b3189-128">Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="b3189-128">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="b3189-129">Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="b3189-129">To install with .NET Core CLI, execute the following in your project directory:</span></span>

  `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b3189-130">Adicione o serviço do Twitter na `ConfigureServices` método no *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="b3189-130">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b3189-131">Adicione o middleware do Twitter na `Configure` método no *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="b3189-131">Add the Twitter middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

::: moniker-end

<span data-ttu-id="b3189-132">Consulte a [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Twitter.</span><span class="sxs-lookup"><span data-stu-id="b3189-132">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="b3189-133">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="b3189-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="b3189-134">Entrar com o Twitter</span><span class="sxs-lookup"><span data-stu-id="b3189-134">Sign in with Twitter</span></span>

<span data-ttu-id="b3189-135">Executar o aplicativo e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="b3189-135">Run your application and click **Log in**.</span></span> <span data-ttu-id="b3189-136">Será exibida uma opção para entrar com o Twitter:</span><span class="sxs-lookup"><span data-stu-id="b3189-136">An option to sign in with Twitter appears:</span></span>

![Aplicativo da Web: Usuário não autenticado](index/_static/DoneTwitter.png)

<span data-ttu-id="b3189-138">Clicar em **Twitter** redireciona para o Twitter para autenticação:</span><span class="sxs-lookup"><span data-stu-id="b3189-138">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

![Página de autenticação do Twitter](index/_static/TwitterLogin.png)

<span data-ttu-id="b3189-140">Depois de inserir suas credenciais do Twitter, você será redirecionado para o site da web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="b3189-140">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="b3189-141">Agora você está conectado usando suas credenciais do Twitter:</span><span class="sxs-lookup"><span data-stu-id="b3189-141">You are now logged in using your Twitter credentials:</span></span>

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="b3189-143">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="b3189-143">Troubleshooting</span></span>

* <span data-ttu-id="b3189-144">**ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="b3189-144">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="b3189-145">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="b3189-145">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="b3189-146">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="b3189-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b3189-147">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="b3189-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b3189-148">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="b3189-148">Next steps</span></span>

* <span data-ttu-id="b3189-149">Este artigo mostrou como você pode autenticar com o Twitter.</span><span class="sxs-lookup"><span data-stu-id="b3189-149">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="b3189-150">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="b3189-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="b3189-151">Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `ConsumerSecret` no portal do desenvolvedor do Twitter.</span><span class="sxs-lookup"><span data-stu-id="b3189-151">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="b3189-152">Defina as `Authentication:Twitter:ConsumerKey` e `Authentication:Twitter:ConsumerSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="b3189-152">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="b3189-153">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="b3189-153">The configuration system is set up to read keys from environment variables.</span></span>
