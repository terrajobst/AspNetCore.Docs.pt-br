---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: bd75efb1d7ce08538d1a67be74d2f40f3964614f
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989756"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="1c0ea-103">Configuração de logon externo da conta da Microsoft com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c0ea-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="1c0ea-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1c0ea-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1c0ea-105">Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft usando o projeto ASP.NET Core 3,0 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1c0ea-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="1c0ea-106">Criar o aplicativo no portal do desenvolvedor da Microsoft</span><span class="sxs-lookup"><span data-stu-id="1c0ea-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="1c0ea-107">Adicione o pacote NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="1c0ea-108">Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="1c0ea-109">Se você não tiver um conta Microsoft, selecione **criar um**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="1c0ea-110">Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :</span><span class="sxs-lookup"><span data-stu-id="1c0ea-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="1c0ea-111">Selecionar **novo registro**</span><span class="sxs-lookup"><span data-stu-id="1c0ea-111">Select **New registration**</span></span>
* <span data-ttu-id="1c0ea-112">Insira um **Nome**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-112">Enter a **Name**.</span></span>
* <span data-ttu-id="1c0ea-113">Selecione uma opção para **tipos de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="1c0ea-114">Em **URI de redirecionamento**, insira a URL de desenvolvimento com `/signin-microsoft` acrescentado.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="1c0ea-115">Por exemplo, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="1c0ea-116">O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente as solicitações em `/signin-microsoft` rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="1c0ea-117">Selecionar **registro**</span><span class="sxs-lookup"><span data-stu-id="1c0ea-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="1c0ea-118">Criar segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="1c0ea-118">Create client secret</span></span>

* <span data-ttu-id="1c0ea-119">No painel esquerdo, selecione **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="1c0ea-120">Em **segredos do cliente**, selecione **novo segredo do cliente**</span><span class="sxs-lookup"><span data-stu-id="1c0ea-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="1c0ea-121">Adicione uma descrição para o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="1c0ea-122">Selecione o botão **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-122">Select the **Add** button.</span></span>

* <span data-ttu-id="1c0ea-123">Em **segredos do cliente**, copie o valor do segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="1c0ea-124">O segmento de URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="1c0ea-125">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="1c0ea-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="1c0ea-126">Armazenar a ID e o segredo do cliente Microsoft</span><span class="sxs-lookup"><span data-stu-id="1c0ea-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="1c0ea-127">Armazene configurações confidenciais, como a ID do cliente da Microsoft e os valores secretos com o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1c0ea-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1c0ea-128">Para este exemplo, use as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1c0ea-129">Inicialize o projeto para o armazenamento secreto de acordo com as instruções em [habilitar armazenamento secreto](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="1c0ea-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1c0ea-130">Armazene as configurações confidenciais no repositório de segredo local com as chaves secretas `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="1c0ea-131">Configurar a autenticação da conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="1c0ea-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="1c0ea-132">Adicione o serviço de conta da Microsoft ao `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1c0ea-133">Para obter mais informações sobre as opções de configuração com suporte da autenticação da conta da Microsoft, consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="1c0ea-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="1c0ea-134">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="1c0ea-135">Conta de Entrar com a conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="1c0ea-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="1c0ea-136">Execute o aplicativo e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="1c0ea-137">É exibida uma opção para entrar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="1c0ea-138">Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="1c0ea-139">Depois de entrar com sua conta da Microsoft, você será solicitado a permitir que o aplicativo acesse suas informações:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="1c0ea-140">Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="1c0ea-141">Agora você está conectado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="1c0ea-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="1c0ea-142">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="1c0ea-142">Troubleshooting</span></span>

* <span data-ttu-id="1c0ea-143">Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição de cadeia de caracteres de consulta diretamente seguindo o `#` (hashtag) no URI.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="1c0ea-144">Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .</span><span class="sxs-lookup"><span data-stu-id="1c0ea-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="1c0ea-145">Se a identidade não estiver configurada chamando `services.AddIdentity` no `ConfigureServices`, tentar autenticar resultará em *ArgumentException: a opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1c0ea-146">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="1c0ea-147">Se o banco de dados do site não tiver sido criado aplicando a migração inicial, você obterá *uma operação de banco de dados com falha ao processar o erro de solicitação* .</span><span class="sxs-lookup"><span data-stu-id="1c0ea-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1c0ea-148">Toque em **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1c0ea-149">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="1c0ea-149">Next steps</span></span>

* <span data-ttu-id="1c0ea-150">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="1c0ea-151">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="1c0ea-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1c0ea-152">Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="1c0ea-153">Defina o `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1c0ea-154">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="1c0ea-154">The configuration system is set up to read keys from environment variables.</span></span>
