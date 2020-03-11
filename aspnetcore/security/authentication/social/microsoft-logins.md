---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659792"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="f7e99-103">Configuração de logon externo da conta da Microsoft com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7e99-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="f7e99-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7e99-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7e99-105">Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f7e99-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="f7e99-106">Criar o aplicativo no portal do desenvolvedor da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7e99-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="f7e99-107">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="f7e99-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="f7e99-108">Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f7e99-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="f7e99-109">Se você não tiver um conta Microsoft, selecione **criar um**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="f7e99-110">Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :</span><span class="sxs-lookup"><span data-stu-id="f7e99-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="f7e99-111">Selecionar **novo registro**</span><span class="sxs-lookup"><span data-stu-id="f7e99-111">Select **New registration**</span></span>
* <span data-ttu-id="f7e99-112">Insira um **Nome**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-112">Enter a **Name**.</span></span>
* <span data-ttu-id="f7e99-113">Selecione uma opção para **tipos de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="f7e99-114">Em **URI de redirecionamento**, insira a URL de desenvolvimento com `/signin-microsoft` acrescentado.</span><span class="sxs-lookup"><span data-stu-id="f7e99-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="f7e99-115">Por exemplo, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="f7e99-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="f7e99-116">O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-microsoft` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="f7e99-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="f7e99-117">Selecionar **registro**</span><span class="sxs-lookup"><span data-stu-id="f7e99-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="f7e99-118">Criar segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f7e99-118">Create client secret</span></span>

* <span data-ttu-id="f7e99-119">No painel esquerdo, selecione **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="f7e99-120">Em **segredos do cliente**, selecione **novo segredo do cliente**</span><span class="sxs-lookup"><span data-stu-id="f7e99-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="f7e99-121">Adicione uma descrição para o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7e99-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="f7e99-122">Selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-122">Select the **Add** button.</span></span>

* <span data-ttu-id="f7e99-123">Em **segredos do cliente**, copie o valor do segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7e99-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="f7e99-124">O segmento de URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7e99-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="f7e99-125">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="f7e99-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="f7e99-126">Armazenar a ID do cliente da Microsoft e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f7e99-126">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="f7e99-127">Execute os comandos a seguir para armazenar com segurança `ClientId` e `ClientSecret` usando o [Gerenciador de segredo](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="f7e99-127">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="f7e99-128">Vincule configurações confidenciais, como o Microsoft `ClientId` e `ClientSecret` à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f7e99-128">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f7e99-129">Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="f7e99-129">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="f7e99-130">Configurar a autenticação da conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7e99-130">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="f7e99-131">Adicione o serviço de conta da Microsoft ao `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7e99-131">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="f7e99-132">Para obter mais informações sobre as opções de configuração com suporte da autenticação da conta da Microsoft, consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="f7e99-132">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="f7e99-133">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="f7e99-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="f7e99-134">Conta de Entrar com a conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7e99-134">Sign in with Microsoft Account</span></span>

<span data-ttu-id="f7e99-135">Execute o aplicativo e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="f7e99-135">Run the app and click **Log in**.</span></span> <span data-ttu-id="f7e99-136">É exibida uma opção para entrar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7e99-136">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="f7e99-137">Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="f7e99-137">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="f7e99-138">Depois de entrar com sua conta da Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:</span><span class="sxs-lookup"><span data-stu-id="f7e99-138">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="f7e99-139">Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="f7e99-139">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="f7e99-140">Agora você está conectado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f7e99-140">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="f7e99-141">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f7e99-141">Troubleshooting</span></span>

* <span data-ttu-id="f7e99-142">Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição de cadeia de caracteres de consulta diretamente seguindo o `#` (hashtag) no URI.</span><span class="sxs-lookup"><span data-stu-id="f7e99-142">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="f7e99-143">Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .</span><span class="sxs-lookup"><span data-stu-id="f7e99-143">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="f7e99-144">Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="f7e99-144">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f7e99-145">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="f7e99-145">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="f7e99-146">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="f7e99-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f7e99-147">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="f7e99-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f7e99-148">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f7e99-148">Next steps</span></span>

* <span data-ttu-id="f7e99-149">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7e99-149">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="f7e99-150">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f7e99-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="f7e99-151">Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7e99-151">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="f7e99-152">Defina o `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="f7e99-152">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f7e99-153">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f7e99-153">The configuration system is set up to read keys from environment variables.</span></span>
