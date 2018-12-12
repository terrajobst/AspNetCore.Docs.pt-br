---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/11/2018
uid: security/authentication/facebook-logins
ms.openlocfilehash: d4f3e210b0d3c79eaf2233f97a29a6d96cd69b39
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284377"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="dad53-103">Configuração de logon externo do Facebook no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dad53-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="dad53-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dad53-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dad53-105">Este tutorial mostra como permitir que os usuários entrem com a conta do Facebook deles usando um projeto de amostra do ASP.NET Core 2.0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dad53-105">This tutorial shows you how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="dad53-106">Autenticação do Facebook requer o [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="dad53-106">Facebook authentication requires the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package.</span></span> <span data-ttu-id="dad53-107">Vamos começar criando um Facebook App ID seguindo as [etapas oficiais](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="dad53-107">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="dad53-108">Criar o aplicativo no Facebook</span><span class="sxs-lookup"><span data-stu-id="dad53-108">Create the app in Facebook</span></span>

* <span data-ttu-id="dad53-109">Navegue até a página [aplicativos no site para desenvolvedores do Facebook](https://developers.facebook.com/apps/).</span><span class="sxs-lookup"><span data-stu-id="dad53-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="dad53-110">Se você ainda não tiver uma conta do Facebook, use o link **Sign up foinscrever-se para o Facebook** na página de logon para criar uma.</span><span class="sxs-lookup"><span data-stu-id="dad53-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="dad53-111">Clique no botão **adicionar um novo aplicativo** no canto superior direito para criar uma nova ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dad53-111">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Abra o Facebook para o portal de desenvolvedores no Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="dad53-113">Preencha o formulário e clique no botão **criar ID do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="dad53-113">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Criar um formulário de nova ID do aplicativo](index/_static/FBNewAppId.png)

* <span data-ttu-id="dad53-115">Na página **selecionar um produto** , clique em **Set Up** no painel **Facebook Login**.</span><span class="sxs-lookup"><span data-stu-id="dad53-115">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![Página de instalação do produto](index/_static/FBProductSetup.png)

* <span data-ttu-id="dad53-117">O assistente **Quickstart** iniciará com **escolher uma plataforma** como a primeira página.</span><span class="sxs-lookup"><span data-stu-id="dad53-117">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="dad53-118">Ignore o assistente clicando no link **configurações** no menu à esquerda:</span><span class="sxs-lookup"><span data-stu-id="dad53-118">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![Início rápido do Skip](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="dad53-120">Você verá a **configurações do cliente OAuth** página:</span><span class="sxs-lookup"><span data-stu-id="dad53-120">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="dad53-122">Insira o URI de desenvolvimento com */signin-facebook* acrescentado para o **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="dad53-122">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="dad53-123">A autenticação do Facebook configurada mais tarde neste tutorial automaticamente manipulará as solicitações no */signin-facebook* rota para implementar o fluxo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="dad53-123">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="dad53-124">O URI */signin-facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="dad53-124">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="dad53-125">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Facebook por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="dad53-125">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="dad53-126">Clique em **salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="dad53-126">Click **Save Changes**.</span></span>

* <span data-ttu-id="dad53-127">Clique em **as configurações** > **básica** link no painel de navegação à esquerda.</span><span class="sxs-lookup"><span data-stu-id="dad53-127">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="dad53-128">Nessa página, anote seu `App ID` e seu `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="dad53-128">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="dad53-129">Você adicionará os dois em seu aplicativo ASP.NET Core na próxima seção:</span><span class="sxs-lookup"><span data-stu-id="dad53-129">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="dad53-130">Ao implantar o site, você precisará voltar para a página de configurações **Logon do Facebook** e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="dad53-130">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="dad53-131">ID do Facebook App Store e o segredo do aplicativo</span><span class="sxs-lookup"><span data-stu-id="dad53-131">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="dad53-132">Vincular as configurações confidenciais, como o Facebook `App ID` e `App Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dad53-132">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="dad53-133">Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="dad53-133">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

<span data-ttu-id="dad53-134">Execute os seguintes comandos para armazenar com segurança `App ID` e `App Secret` usando o Secret Manager:</span><span class="sxs-lookup"><span data-stu-id="dad53-134">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="dad53-135">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="dad53-135">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="dad53-136">Adicione o serviço do Facebook no método `ConfigureServices` do arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="dad53-136">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="dad53-137">Instalar o pacote [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook).</span><span class="sxs-lookup"><span data-stu-id="dad53-137">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="dad53-138">Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="dad53-138">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="dad53-139">Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="dad53-139">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="dad53-140">Adicione o middleware do Facebook no método `Configure` do arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="dad53-140">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="dad53-141">Consulte as referências de API do [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração compatíveis com a autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="dad53-141">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="dad53-142">As opções de configuração podem ser usadas para:</span><span class="sxs-lookup"><span data-stu-id="dad53-142">Configuration options can be used to:</span></span>

* <span data-ttu-id="dad53-143">Solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="dad53-143">Request different information about the user.</span></span>
* <span data-ttu-id="dad53-144">Adicionar argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.</span><span class="sxs-lookup"><span data-stu-id="dad53-144">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="dad53-145">Entrar com o Facebook</span><span class="sxs-lookup"><span data-stu-id="dad53-145">Sign in with Facebook</span></span>

<span data-ttu-id="dad53-146">Executar o aplicativo e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="dad53-146">Run your application and click **Log in**.</span></span> <span data-ttu-id="dad53-147">Você verá uma opção para entrar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="dad53-147">You see an option to sign in with Facebook.</span></span>

![Aplicativo da Web: Usuário não autenticado](index/_static/DoneFacebook.png)

<span data-ttu-id="dad53-149">Quando você clica em **Facebook**, você será redirecionado ao Facebook para autenticação:</span><span class="sxs-lookup"><span data-stu-id="dad53-149">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Página de autenticação do Facebook](index/_static/FBLogin.png)

<span data-ttu-id="dad53-151">Autenticação do Facebook solicita o endereço de email e o perfil público por padrão:</span><span class="sxs-lookup"><span data-stu-id="dad53-151">Facebook authentication requests public profile and email address by default:</span></span>

![Tela de consentimento de página de autenticação do Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="dad53-153">Depois que você insira suas credenciais do Facebook, você será redirecionado para seu site em que você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="dad53-153">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="dad53-154">Agora você está conectado usando suas credenciais do Facebook:</span><span class="sxs-lookup"><span data-stu-id="dad53-154">You are now logged in using your Facebook credentials:</span></span>

![Aplicativo da Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="dad53-156">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="dad53-156">Troubleshooting</span></span>

* <span data-ttu-id="dad53-157">**ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="dad53-157">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="dad53-158">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="dad53-158">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="dad53-159">Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="dad53-159">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="dad53-160">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="dad53-160">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dad53-161">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="dad53-161">Next steps</span></span>

* <span data-ttu-id="dad53-162">Este artigo mostrou como você pode autenticar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="dad53-162">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="dad53-163">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dad53-163">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="dad53-164">Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="dad53-164">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="dad53-165">Defina as `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="dad53-165">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="dad53-166">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="dad53-166">The configuration system is set up to read keys from environment variables.</span></span>
