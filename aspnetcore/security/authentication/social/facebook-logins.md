---
title: Configuração de logon externo do Facebook no ASP.NET Core
author: rick-anderson
description: Tutorial com exemplos de código que demonstram a integração da autenticação de usuário da conta do Facebook em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717070"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="fe86f-103">Configuração de logon externo do Facebook no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe86f-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="fe86f-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fe86f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fe86f-105">Este tutorial com exemplos de código mostra como permitir que os usuários entrem com sua conta do Facebook usando um projeto de exemplo ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="fe86f-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="fe86f-106">Começamos criando uma ID de aplicativo do Facebook seguindo as [etapas oficiais](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="fe86f-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="fe86f-107">Criar o aplicativo no Facebook</span><span class="sxs-lookup"><span data-stu-id="fe86f-107">Create the app in Facebook</span></span>

* <span data-ttu-id="fe86f-108">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="fe86f-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="fe86f-109">Navegue até a página do [aplicativo de desenvolvedores do Facebook](https://developers.facebook.com/apps/) e entre.</span><span class="sxs-lookup"><span data-stu-id="fe86f-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="fe86f-110">Se você ainda não tiver uma conta do Facebook, use o link **inscrever-** se no Facebook na página de logon para criar uma.</span><span class="sxs-lookup"><span data-stu-id="fe86f-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="fe86f-111">Depois de ter uma conta do Facebook, siga as instruções para se registrar como um desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="fe86f-112">No menu **meus aplicativos** , selecione **criar aplicativo** para criar uma nova ID de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe86f-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portal do Facebook for Developers aberto no Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="fe86f-114">Preencha o formulário e toque no botão **criar ID do aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="fe86f-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Criar um novo formulário de ID do aplicativo](index/_static/FBNewAppId.png)

* <span data-ttu-id="fe86f-116">Na nova placa de aplicativo, selecione **Adicionar um produto**.</span><span class="sxs-lookup"><span data-stu-id="fe86f-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="fe86f-117">No cartão de **logon do Facebook** , clique em **Configurar**</span><span class="sxs-lookup"><span data-stu-id="fe86f-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Página configuração do produto](index/_static/FBProductSetup.png)

* <span data-ttu-id="fe86f-119">O assistente de **início rápido** é iniciado com **escolher uma plataforma** como a primeira página.</span><span class="sxs-lookup"><span data-stu-id="fe86f-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="fe86f-120">Ignore o assistente por enquanto clicando no link **configurações** de logon do Facebook no menu na parte inferior esquerda:</span><span class="sxs-lookup"><span data-stu-id="fe86f-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Ignorar Início Rápido](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="fe86f-122">Você verá a página **configurações de OAuth do cliente** :</span><span class="sxs-lookup"><span data-stu-id="fe86f-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Página de configurações do OAuth do cliente](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="fe86f-124">Insira seu URI de desenvolvimento com */SignIn-Facebook* anexados ao campo **URIs de redirecionamento OAuth válido** (por exemplo: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="fe86f-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="fe86f-125">A autenticação do Facebook configurada posteriormente neste tutorial tratará automaticamente as solicitações na rota */SignIn-Facebook* para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="fe86f-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="fe86f-126">O URI */SignIn-Facebook* é definido como o retorno de chamada padrão do provedor de autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="fe86f-127">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação do Facebook por meio da propriedade [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) herdada da classe [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="fe86f-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="fe86f-128">Clique em **salvar alterações**.</span><span class="sxs-lookup"><span data-stu-id="fe86f-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="fe86f-129">Clique em **configurações** > link **básico** no painel de navegação esquerdo.</span><span class="sxs-lookup"><span data-stu-id="fe86f-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="fe86f-130">Nessa página, anote sua `App ID` e sua `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="fe86f-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="fe86f-131">Você adicionará ambos ao seu aplicativo ASP.NET Core na próxima seção:</span><span class="sxs-lookup"><span data-stu-id="fe86f-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="fe86f-132">Ao implantar o site, você precisa revisitar a página de configuração de **logon do Facebook** e registrar um novo URI público.</span><span class="sxs-lookup"><span data-stu-id="fe86f-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="fe86f-133">Armazenar ID do aplicativo do Facebook e segredo do aplicativo</span><span class="sxs-lookup"><span data-stu-id="fe86f-133">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="fe86f-134">Vincule configurações confidenciais como o Facebook `App ID` e `App Secret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="fe86f-134">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="fe86f-135">Para os fins deste tutorial, nomeie os tokens `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="fe86f-135">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="fe86f-136">Execute os comandos a seguir para armazenar com segurança `App ID` e `App Secret` usando o Gerenciador de segredo:</span><span class="sxs-lookup"><span data-stu-id="fe86f-136">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="fe86f-137">Configurar a autenticação do Facebook</span><span class="sxs-lookup"><span data-stu-id="fe86f-137">Configure Facebook Authentication</span></span>

<span data-ttu-id="fe86f-138">Adicione o serviço do Facebook no método `ConfigureServices` no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="fe86f-138">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="fe86f-139">Consulte a referência da API do [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) para obter mais informações sobre as opções de configuração com suporte na autenticação do Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-139">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="fe86f-140">As opções de configuração podem ser usadas para:</span><span class="sxs-lookup"><span data-stu-id="fe86f-140">Configuration options can be used to:</span></span>

* <span data-ttu-id="fe86f-141">Solicite informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="fe86f-141">Request different information about the user.</span></span>
* <span data-ttu-id="fe86f-142">Adicione argumentos de cadeia de caracteres de consulta para personalizar a experiência de logon.</span><span class="sxs-lookup"><span data-stu-id="fe86f-142">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="fe86f-143">Entrar com o Facebook</span><span class="sxs-lookup"><span data-stu-id="fe86f-143">Sign in with Facebook</span></span>

<span data-ttu-id="fe86f-144">Execute o aplicativo e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="fe86f-144">Run your application and click **Log in**.</span></span> <span data-ttu-id="fe86f-145">Você verá uma opção para entrar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-145">You see an option to sign in with Facebook.</span></span>

![Aplicativo Web: usuário não autenticado](index/_static/DoneFacebook.png)

<span data-ttu-id="fe86f-147">Ao clicar em **Facebook**, você será redirecionado para o Facebook para autenticação:</span><span class="sxs-lookup"><span data-stu-id="fe86f-147">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Página de autenticação do Facebook](index/_static/FBLogin.png)

<span data-ttu-id="fe86f-149">A autenticação do Facebook solicita o perfil público e o endereço de email por padrão:</span><span class="sxs-lookup"><span data-stu-id="fe86f-149">Facebook authentication requests public profile and email address by default:</span></span>

![Tela de consentimento da página de autenticação do Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="fe86f-151">Depois de inserir suas credenciais do Facebook, você será Redirecionado de volta para o site onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="fe86f-151">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="fe86f-152">Agora você está conectado usando suas credenciais do Facebook:</span><span class="sxs-lookup"><span data-stu-id="fe86f-152">You are now logged in using your Facebook credentials:</span></span>

![Aplicativo Web: usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="fe86f-154">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="fe86f-154">Troubleshooting</span></span>

* <span data-ttu-id="fe86f-155">**Somente ASP.NET Core 2. x:** Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="fe86f-155">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="fe86f-156">O modelo de projeto usado neste tutorial garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="fe86f-156">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="fe86f-157">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="fe86f-157">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="fe86f-158">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="fe86f-158">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fe86f-159">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="fe86f-159">Next steps</span></span>

* <span data-ttu-id="fe86f-160">Este artigo mostrou como você pode autenticar com o Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-160">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="fe86f-161">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="fe86f-161">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="fe86f-162">Depois de publicar seu site no aplicativo Web do Azure, você deve redefinir o `AppSecret` no portal do desenvolvedor do Facebook.</span><span class="sxs-lookup"><span data-stu-id="fe86f-162">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="fe86f-163">Defina o `Authentication:Facebook:AppId` e `Authentication:Facebook:AppSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="fe86f-163">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="fe86f-164">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="fe86f-164">The configuration system is set up to read keys from environment variables.</span></span>
