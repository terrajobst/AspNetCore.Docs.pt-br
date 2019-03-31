---
title: Configuração de logon externo Account da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta da Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1733d049d6752c24d7749b5b5ae2a4b866492358
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58751000"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="2522a-103">Configuração de logon externo Account da Microsoft com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2522a-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="2522a-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2522a-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2522a-105">Este tutorial mostra como permitir que seus usuários entrar com sua conta da Microsoft usando um projeto do ASP.NET Core 2.0 de exemplo criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="2522a-105">This tutorial shows you how to enable your users to sign in with their Microsoft account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="2522a-106">Criar o aplicativo no Portal do desenvolvedor da Microsoft</span><span class="sxs-lookup"><span data-stu-id="2522a-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="2522a-107">Navegue até [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) e criar ou entrar em uma conta da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="2522a-107">Navigate to [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com) and create or sign into a Microsoft account:</span></span>

![Caixa de diálogo entrar](index/_static/MicrosoftDevLogin.png)

<span data-ttu-id="2522a-109">Se você ainda não tiver uma conta da Microsoft, toque em  **[crie uma!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span><span class="sxs-lookup"><span data-stu-id="2522a-109">If you don't already have a Microsoft account, tap **[Create one!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)**</span></span> <span data-ttu-id="2522a-110">Depois de entrar, você será redirecionado para **meus aplicativos** página:</span><span class="sxs-lookup"><span data-stu-id="2522a-110">After signing in you are redirected to **My applications** page:</span></span>

![Portal do desenvolvedor Microsoft aberto no Microsoft Edge](index/_static/MicrosoftDev.png)

* <span data-ttu-id="2522a-112">Toque **adicionar um aplicativo** no canto superior direito de canto e insira seu **nome do aplicativo** e **Contact Email**:</span><span class="sxs-lookup"><span data-stu-id="2522a-112">Tap **Add an app** in the upper right corner and enter your **Application Name** and **Contact Email**:</span></span>

![Caixa de diálogo Nova registro de aplicativo](index/_static/MicrosoftDevAppCreate.png)

* <span data-ttu-id="2522a-114">Para os fins deste tutorial, desmarque a **instalação guiada** caixa de seleção.</span><span class="sxs-lookup"><span data-stu-id="2522a-114">For the purposes of this tutorial, clear the **Guided Setup** check box.</span></span>

* <span data-ttu-id="2522a-115">Toque **Create** para continuar para o **registro** página.</span><span class="sxs-lookup"><span data-stu-id="2522a-115">Tap **Create** to continue to the **Registration** page.</span></span> <span data-ttu-id="2522a-116">Fornecer um **nome** e observe o valor da **Id do aplicativo**, que você usar como `ClientId` posteriormente no tutorial:</span><span class="sxs-lookup"><span data-stu-id="2522a-116">Provide a **Name** and note the value of the **Application Id**, which you use as `ClientId` later in the tutorial:</span></span>

![Página de registro](index/_static/MicrosoftDevAppReg.png)

* <span data-ttu-id="2522a-118">Toque **Adicionar plataforma** na **plataformas** seção e selecione o **Web** plataforma:</span><span class="sxs-lookup"><span data-stu-id="2522a-118">Tap **Add Platform** in the **Platforms** section and select the **Web** platform:</span></span>

![Adicionar caixa de diálogo de plataforma](index/_static/MicrosoftDevAppPlatform.png)

* <span data-ttu-id="2522a-120">No novo **Web** plataforma, digite sua URL de desenvolvimento com `/signin-microsoft` acrescentado para o **URLs de redirecionamento** campo (por exemplo: `https://localhost:44320/signin-microsoft`).</span><span class="sxs-lookup"><span data-stu-id="2522a-120">In the new **Web** platform section, enter your development URL with `/signin-microsoft` appended into the **Redirect URLs** field (for example: `https://localhost:44320/signin-microsoft`).</span></span> <span data-ttu-id="2522a-121">O esquema de autenticação da Microsoft configurado mais tarde neste tutorial automaticamente manipulará as solicitações em `/signin-microsoft` rota para implementar o fluxo de OAuth:</span><span class="sxs-lookup"><span data-stu-id="2522a-121">The Microsoft authentication scheme configured later in this tutorial will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow:</span></span>

![Seção de plataforma da Web](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> <span data-ttu-id="2522a-123">O segmento URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação do Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2522a-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="2522a-124">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação da Microsoft por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="2522a-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

* <span data-ttu-id="2522a-125">Toque **Adicionar URL** para garantir que a URL foi adicionada.</span><span class="sxs-lookup"><span data-stu-id="2522a-125">Tap **Add URL** to ensure the URL was added.</span></span>

* <span data-ttu-id="2522a-126">Preencha quaisquer outras configurações de aplicativo, se necessário e toque em **salvar** na parte inferior da página para salvar as alterações à configuração de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2522a-126">Fill out any other application settings if necessary and tap **Save** at the bottom of the page to save changes to app configuration.</span></span>

* <span data-ttu-id="2522a-127">Ao implantar o site será necessário rever a **registro** página e defina uma nova URL pública.</span><span class="sxs-lookup"><span data-stu-id="2522a-127">When deploying the site you'll need to revisit the **Registration** page and set a new public URL.</span></span>

## <a name="store-microsoft-application-id-and-password"></a><span data-ttu-id="2522a-128">Id do aplicativo da Microsoft e a senha de Store</span><span class="sxs-lookup"><span data-stu-id="2522a-128">Store Microsoft Application Id and Password</span></span>

* <span data-ttu-id="2522a-129">Observe a `Application Id` exibido na **registro** página.</span><span class="sxs-lookup"><span data-stu-id="2522a-129">Note the `Application Id` displayed on the **Registration** page.</span></span>

* <span data-ttu-id="2522a-130">Toque **gerar nova senha** na **segredos do aplicativo** seção.</span><span class="sxs-lookup"><span data-stu-id="2522a-130">Tap **Generate New Password** in the **Application Secrets** section.</span></span> <span data-ttu-id="2522a-131">Isso exibe uma caixa em que você pode copiar a senha de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2522a-131">This displays a box where you can copy the application password:</span></span>

![Caixa de diálogo Nova senha gerada](index/_static/MicrosoftDevPassword.png)

<span data-ttu-id="2522a-133">Vincular as configurações confidenciais, como a Microsoft `Application ID` e `Password` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2522a-133">Link sensitive settings like Microsoft `Application ID` and `Password` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2522a-134">Para os fins deste tutorial, nomeie os tokens `Authentication:Microsoft:ApplicationId` e `Authentication:Microsoft:Password`.</span><span class="sxs-lookup"><span data-stu-id="2522a-134">For the purposes of this tutorial, name the tokens `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="2522a-135">Configurar a autenticação de conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="2522a-135">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="2522a-136">O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) pacote já está instalado.</span><span class="sxs-lookup"><span data-stu-id="2522a-136">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) package is already installed.</span></span>

* <span data-ttu-id="2522a-137">Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2522a-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="2522a-138">Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="2522a-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2522a-139">Adicione o serviço Microsoft Account na `ConfigureServices` método no *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2522a-139">Add the Microsoft Account service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="2522a-140">Adicione o middleware Account da Microsoft na `Configure` método no *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2522a-140">Add the Microsoft Account middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

<span data-ttu-id="2522a-141">Embora a terminologia usada no Portal do desenvolvedor Microsoft nomeia esses tokens `ApplicationId` e `Password`, elas são expostas como `ClientId` e `ClientSecret` para a API de configuração.</span><span class="sxs-lookup"><span data-stu-id="2522a-141">Although the terminology used on Microsoft Developer Portal names these tokens `ApplicationId` and `Password`, they're exposed as `ClientId` and `ClientSecret` to the configuration API.</span></span>

<span data-ttu-id="2522a-142">Consulte a [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação Account da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2522a-142">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="2522a-143">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="2522a-143">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="2522a-144">Entrar com conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="2522a-144">Sign in with Microsoft Account</span></span>

<span data-ttu-id="2522a-145">Executar o aplicativo e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="2522a-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="2522a-146">Uma opção para entrar com a Microsoft será exibida:</span><span class="sxs-lookup"><span data-stu-id="2522a-146">An option to sign in with Microsoft appears:</span></span>

![Log de aplicativo na página de Web: Usuário não autenticado](index/_static/DoneMicrosoft.png)

<span data-ttu-id="2522a-148">Quando você clica na Microsoft, você será redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="2522a-148">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="2522a-149">Após entrar com sua Account da Microsoft (se ainda não estiver conectado), você será solicitado para permitir que o aplicativo acessar suas informações:</span><span class="sxs-lookup"><span data-stu-id="2522a-149">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

![Caixa de diálogo de autenticação Microsoft](index/_static/MicrosoftLogin.png)

<span data-ttu-id="2522a-151">Toque **Sim** e você será redirecionado para o site da web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="2522a-151">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="2522a-152">Agora você está conectado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="2522a-152">You are now logged in using your Microsoft credentials:</span></span>

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="2522a-154">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="2522a-154">Troubleshooting</span></span>

* <span data-ttu-id="2522a-155">Se o provedor Microsoft Account redireciona você para uma página de erro de entrada, observe os erro título e descrição de cadeia de caracteres parâmetros de consulta diretamente após o `#` (hashtag) no Uri.</span><span class="sxs-lookup"><span data-stu-id="2522a-155">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="2522a-156">Embora pareça a mensagem de erro indicar um problema com a autenticação da Microsoft, a causa mais comum é seu Uri não corresponda a um aplicativo de **URIs de redirecionamento** especificado para o **Web** plataforma .</span><span class="sxs-lookup"><span data-stu-id="2522a-156">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="2522a-157">**ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="2522a-157">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="2522a-158">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="2522a-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="2522a-159">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="2522a-159">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="2522a-160">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="2522a-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2522a-161">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2522a-161">Next steps</span></span>

* <span data-ttu-id="2522a-162">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2522a-162">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="2522a-163">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="2522a-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="2522a-164">Depois de publicar seu site da web para aplicativo web do Azure, você deve criar um novo `Password` no Portal do desenvolvedor Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2522a-164">Once you publish your web site to Azure web app, you should create a new `Password` in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="2522a-165">Defina as `Authentication:Microsoft:ApplicationId` e `Authentication:Microsoft:Password` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="2522a-165">Set the `Authentication:Microsoft:ApplicationId` and `Authentication:Microsoft:Password` as application settings in the Azure portal.</span></span> <span data-ttu-id="2522a-166">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2522a-166">The configuration system is set up to read keys from environment variables.</span></span>
