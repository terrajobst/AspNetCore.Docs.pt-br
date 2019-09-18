---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código que demonstram a integração da autenticação de usuário da conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: f7b21de7e5fe9d77804588280c3d8be9df8afee5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082545"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="7f8ee-103">Configuração de logon externo do Facebook no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7f8ee-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="7f8ee-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f8ee-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7f8ee-105">Este tutorial com exemplos de código mostra como permitir que os usuários entrem com sua conta do Facebook usando um projeto de exemplo ASP.NET Core 2,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="7f8ee-106">Vamos começar criando um Facebook App ID seguindo as [etapas oficiais](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="7f8ee-107">Criar o aplicativo no Facebook</span><span class="sxs-lookup"><span data-stu-id="7f8ee-107">Create the app in Facebook</span></span>

* <span data-ttu-id="7f8ee-108">Navegue até a página [aplicativos no site para desenvolvedores do Facebook](https://developers.facebook.com/apps/).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-108">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="7f8ee-109">Se você ainda não tiver uma conta do Facebook, use o link **Sign up foinscrever-se para o Facebook** na página de logon para criar uma.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-109">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>

* <span data-ttu-id="7f8ee-110">Clique no botão **adicionar um novo aplicativo** no canto superior direito para criar uma nova ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-110">Tap the **Add a New App** button in the upper right corner to create a new App ID.</span></span>

   ![Abra o Facebook para o portal de desenvolvedores no Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="7f8ee-112">Preencha o formulário e clique no botão **criar ID do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-112">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Criar um formulário de nova ID do aplicativo](index/_static/FBNewAppId.png)

* <span data-ttu-id="7f8ee-114">Na página **selecionar um produto** , clique em **Set Up** no painel **Facebook Login**.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-114">On the **Select a product** page, click **Set Up** on the **Facebook Login** card.</span></span>

  ![Página de instalação do produto](index/_static/FBProductSetup.png)

* <span data-ttu-id="7f8ee-116">O assistente **Quickstart** iniciará com **escolher uma plataforma** como a primeira página.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-116">The **Quickstart** wizard will launch with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="7f8ee-117">Ignore o assistente clicando no link **configurações** no menu à esquerda:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-117">Bypass the wizard for now by clicking the **Settings** link in the menu on the left:</span></span>

  ![Início rápido do Skip](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="7f8ee-119">Você verá a **configurações do cliente OAuth** página:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-119">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="7f8ee-121">Insira o URI de desenvolvimento com */signin-facebook* acrescentado para o **URIs de redirecionamento de OAuth válido** campo (por exemplo: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-121">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="7f8ee-122">A autenticação do Facebook configurada mais tarde neste tutorial automaticamente manipulará as solicitações no */signin-facebook* rota para implementar o fluxo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-122">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="7f8ee-123">O URI */signin-facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-123">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="7f8ee-124">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Facebook por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-124">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="7f8ee-125">Clique em **salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-125">Click **Save Changes**.</span></span>

* <span data-ttu-id="7f8ee-126">Clique em **as configurações** > **básica** link no painel de navegação à esquerda.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-126">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="7f8ee-127">Nessa página, anote seu `App ID` e seu `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-127">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="7f8ee-128">Você adicionará os dois em seu aplicativo ASP.NET Core na próxima seção:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-128">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="7f8ee-129">Ao implantar o site, você precisará voltar para a página de configurações **Logon do Facebook** e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-129">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="7f8ee-130">ID do Facebook App Store e o segredo do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7f8ee-130">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="7f8ee-131">Vincular as configurações confidenciais, como o Facebook `App ID` e `App Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-131">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="7f8ee-132">Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-132">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7f8ee-133">Execute os seguintes comandos para armazenar com segurança `App ID` e `App Secret` usando o Secret Manager:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-133">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="7f8ee-134">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="7f8ee-134">Configure Facebook Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7f8ee-135">Adicione o serviço do Facebook no método `ConfigureServices` do arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="7f8ee-135">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

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

<span data-ttu-id="7f8ee-136">Instalar o pacote [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-136">Install the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) package.</span></span>

* <span data-ttu-id="7f8ee-137">Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-137">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="7f8ee-138">Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-138">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

<span data-ttu-id="7f8ee-139">Adicione o middleware do Facebook no método `Configure` do arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-139">Add the Facebook middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

<span data-ttu-id="7f8ee-140">Consulte as referências de API do [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração compatíveis com a autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="7f8ee-141">As opções de configuração podem ser usadas para:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="7f8ee-142">Solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-142">Request different information about the user.</span></span>
* <span data-ttu-id="7f8ee-143">Adicionar argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="7f8ee-144">Entrar com o Facebook</span><span class="sxs-lookup"><span data-stu-id="7f8ee-144">Sign in with Facebook</span></span>

<span data-ttu-id="7f8ee-145">Executar o aplicativo e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="7f8ee-146">Você verá uma opção para entrar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-146">You see an option to sign in with Facebook.</span></span>

![Aplicativo Web: Usuário não autenticado](index/_static/DoneFacebook.png)

<span data-ttu-id="7f8ee-148">Quando você clica em **Facebook**, você será redirecionado ao Facebook para autenticação:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Página de autenticação do Facebook](index/_static/FBLogin.png)

<span data-ttu-id="7f8ee-150">Autenticação do Facebook solicita o endereço de email e o perfil público por padrão:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-150">Facebook authentication requests public profile and email address by default:</span></span>

![Tela de consentimento de página de autenticação do Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="7f8ee-152">Depois que você insira suas credenciais do Facebook, você será redirecionado para seu site em que você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="7f8ee-153">Agora você está conectado usando suas credenciais do Facebook:</span><span class="sxs-lookup"><span data-stu-id="7f8ee-153">You are now logged in using your Facebook credentials:</span></span>

![Aplicativo Web: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="7f8ee-155">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="7f8ee-155">Troubleshooting</span></span>

* <span data-ttu-id="7f8ee-156">**Somente ASP.NET Core 2. x:** Se a identidade não estiver configurada chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7f8ee-157">O modelo de projeto usado neste tutorial garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="7f8ee-158">Se o banco de dados do site não foi criado por meio da aplicação a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7f8ee-159">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7f8ee-160">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7f8ee-160">Next steps</span></span>

* <span data-ttu-id="7f8ee-161">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) ao seu projeto para cenários de autenticação avançada do Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-161">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to your project for advanced Facebook authentication scenarios.</span></span> <span data-ttu-id="7f8ee-162">Este pacote não é necessário para integrar a funcionalidade de logon externo do Facebook ao seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-162">This package isn't required to integrate Facebook external login functionality with your app.</span></span> 

* <span data-ttu-id="7f8ee-163">Este artigo mostrou como você pode autenticar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-163">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="7f8ee-164">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7f8ee-164">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="7f8ee-165">Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-165">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="7f8ee-166">Defina as `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-166">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="7f8ee-167">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7f8ee-167">The configuration system is set up to read keys from environment variables.</span></span>
