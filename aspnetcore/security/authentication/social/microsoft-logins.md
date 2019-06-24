---
title: Configuração de logon externo Account da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração da autenticação de usuário de conta da Microsoft em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 16ec2d5f2bccc59958b884869ef42af9cfa13df0
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316599"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="f7f07-103">Configuração de logon externo Account da Microsoft com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7f07-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="f7f07-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7f07-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7f07-105">Este exemplo mostra como habilitar usuários entrar com sua conta da Microsoft usando o projeto do ASP.NET Core 2.2 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f7f07-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="f7f07-106">Criar o aplicativo no Portal do desenvolvedor da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7f07-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="f7f07-107">Navegue até a [portal do Azure - registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) página e criar ou entrar em uma conta da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f7f07-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="f7f07-108">Se você não tiver uma conta da Microsoft, selecione **criar um**.</span><span class="sxs-lookup"><span data-stu-id="f7f07-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="f7f07-109">Depois de entrar, você será redirecionado para o **registros de aplicativo** página:</span><span class="sxs-lookup"><span data-stu-id="f7f07-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="f7f07-110">Selecione **novo registro**</span><span class="sxs-lookup"><span data-stu-id="f7f07-110">Select **New registration**</span></span>
* <span data-ttu-id="f7f07-111">Insira um **nome**.</span><span class="sxs-lookup"><span data-stu-id="f7f07-111">Enter a **Name**.</span></span>
* <span data-ttu-id="f7f07-112">Selecione uma opção para **suporte para tipos de conta**.</span><span class="sxs-lookup"><span data-stu-id="f7f07-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="f7f07-113">Sob **URI de redirecionamento**, insira a URL do desenvolvimento com `/signin-microsoft` acrescentado.</span><span class="sxs-lookup"><span data-stu-id="f7f07-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="f7f07-114">Por exemplo, `https://localhost:44389/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="f7f07-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="f7f07-115">O esquema de autenticação da Microsoft configurado mais tarde neste exemplo automaticamente manipulará as solicitações em `/signin-microsoft` rota para implementar o fluxo de OAuth.</span><span class="sxs-lookup"><span data-stu-id="f7f07-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="f7f07-116">Selecione **registrar**</span><span class="sxs-lookup"><span data-stu-id="f7f07-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="f7f07-117">Criar o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="f7f07-117">Create client secret</span></span>

* <span data-ttu-id="f7f07-118">No painel esquerdo, selecione **certificados e segredos**.</span><span class="sxs-lookup"><span data-stu-id="f7f07-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="f7f07-119">Sob **segredos de cliente**, selecione **novo segredo do cliente**</span><span class="sxs-lookup"><span data-stu-id="f7f07-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="f7f07-120">Adicione uma descrição para o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7f07-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="f7f07-121">Selecione o **adicionar** botão.</span><span class="sxs-lookup"><span data-stu-id="f7f07-121">Select the **Add** button.</span></span>

* <span data-ttu-id="f7f07-122">Sob **segredos de cliente**, copie o valor do segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7f07-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="f7f07-123">O segmento URI `/signin-microsoft` é definido como o retorno de chamada padrão do provedor de autenticação do Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7f07-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="f7f07-124">Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação da Microsoft por meio de herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) classe.</span><span class="sxs-lookup"><span data-stu-id="f7f07-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="f7f07-125">Store o segredo de cliente e a ID de cliente do Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7f07-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="f7f07-126">Execute os seguintes comandos para armazenar com segurança `ClientId` e `ClientSecret` usando [Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="f7f07-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="f7f07-127">Vincular as configurações confidenciais, como a Microsoft `ClientId` e `ClientSecret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f7f07-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f7f07-128">Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="f7f07-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="f7f07-129">Configurar a autenticação de conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7f07-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="f7f07-130">Adicionar o serviço da Microsoft Account `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f7f07-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="f7f07-131">Consulte a [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação Account da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7f07-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="f7f07-132">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="f7f07-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="f7f07-133">Entrar com conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="f7f07-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="f7f07-134">Execute o e clique em **faça logon no**.</span><span class="sxs-lookup"><span data-stu-id="f7f07-134">Run the and click **Log in**.</span></span> <span data-ttu-id="f7f07-135">Uma opção para entrar com a Microsoft é exibida.</span><span class="sxs-lookup"><span data-stu-id="f7f07-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="f7f07-136">Quando você clica na Microsoft, você será redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="f7f07-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="f7f07-137">Após entrar com sua Account da Microsoft (se ainda não estiver conectado), você será solicitado para permitir que o aplicativo acessar suas informações:</span><span class="sxs-lookup"><span data-stu-id="f7f07-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="f7f07-138">Toque **Sim** e você será redirecionado para o site da web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="f7f07-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="f7f07-139">Agora você está conectado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="f7f07-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="f7f07-140">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f7f07-140">Troubleshooting</span></span>

* <span data-ttu-id="f7f07-141">Se o provedor Microsoft Account redireciona você para uma página de erro de entrada, observe os erro título e descrição de cadeia de caracteres parâmetros de consulta diretamente após o `#` (hashtag) no Uri.</span><span class="sxs-lookup"><span data-stu-id="f7f07-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="f7f07-142">Embora pareça a mensagem de erro indicar um problema com a autenticação da Microsoft, a causa mais comum é seu Uri não corresponda a um aplicativo de **URIs de redirecionamento** especificado para o **Web** plataforma .</span><span class="sxs-lookup"><span data-stu-id="f7f07-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="f7f07-143">Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="f7f07-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f7f07-144">O modelo de projeto usado neste exemplo garante que isso é feito.</span><span class="sxs-lookup"><span data-stu-id="f7f07-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="f7f07-145">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="f7f07-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f7f07-146">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="f7f07-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f7f07-147">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f7f07-147">Next steps</span></span>

* <span data-ttu-id="f7f07-148">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7f07-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="f7f07-149">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f7f07-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="f7f07-150">Depois de publicar seu site da web para aplicativo web do Azure, crie um novo cliente segredos no Portal do desenvolvedor Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7f07-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="f7f07-151">Defina as `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="f7f07-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f7f07-152">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="f7f07-152">The configuration system is set up to read keys from environment variables.</span></span>