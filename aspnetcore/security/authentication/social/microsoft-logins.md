---
title: Configuração de logon externo da conta da Microsoft com o ASP.NET Core
author: rick-anderson
description: Este exemplo demonstra a integração do conta Microsoft autenticação de usuário em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082337"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="dac13-103">Configuração de logon externo da conta da Microsoft com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dac13-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="dac13-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dac13-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dac13-105">Este exemplo mostra como permitir que os usuários entrem com seus conta Microsoft usando o projeto ASP.NET Core 2,2 criado na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dac13-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="dac13-106">Criar o aplicativo no portal do desenvolvedor da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dac13-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="dac13-107">Navegue até a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) e crie ou entre em uma conta Microsoft:</span><span class="sxs-lookup"><span data-stu-id="dac13-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="dac13-108">Se você não tiver um conta Microsoft, selecione **criar um**.</span><span class="sxs-lookup"><span data-stu-id="dac13-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="dac13-109">Depois de entrar, você será redirecionado para a página de **registros de aplicativo** :</span><span class="sxs-lookup"><span data-stu-id="dac13-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="dac13-110">Selecionar **novo registro**</span><span class="sxs-lookup"><span data-stu-id="dac13-110">Select **New registration**</span></span>
* <span data-ttu-id="dac13-111">Insira um **nome**.</span><span class="sxs-lookup"><span data-stu-id="dac13-111">Enter a **Name**.</span></span>
* <span data-ttu-id="dac13-112">Selecione uma opção para **tipos de conta com suporte**.</span><span class="sxs-lookup"><span data-stu-id="dac13-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="dac13-113">Em **URI de redirecionamento**, insira a `/signin-microsoft` URL de desenvolvimento com anexado.</span><span class="sxs-lookup"><span data-stu-id="dac13-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="dac13-114">Por exemplo: `https://localhost:44389/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="dac13-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="dac13-115">O esquema de autenticação da Microsoft configurado mais adiante neste exemplo tratará automaticamente `/signin-microsoft` as solicitações na rota para implementar o fluxo OAuth.</span><span class="sxs-lookup"><span data-stu-id="dac13-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="dac13-116">Selecionar **registro**</span><span class="sxs-lookup"><span data-stu-id="dac13-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="dac13-117">Criar segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="dac13-117">Create client secret</span></span>

* <span data-ttu-id="dac13-118">No painel esquerdo, selecione **certificados & segredos**.</span><span class="sxs-lookup"><span data-stu-id="dac13-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="dac13-119">Em **segredos do cliente**, selecione **novo segredo do cliente**</span><span class="sxs-lookup"><span data-stu-id="dac13-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="dac13-120">Adicione uma descrição para o segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="dac13-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="dac13-121">Selecione o botão **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="dac13-121">Select the **Add** button.</span></span>

* <span data-ttu-id="dac13-122">Em **segredos do cliente**, copie o valor do segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="dac13-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="dac13-123">O segmento `/signin-microsoft` URI é definido como o retorno de chamada padrão do provedor de autenticação da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dac13-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="dac13-124">Você pode alterar o URI de retorno de chamada padrão ao configurar o middleware de autenticação da Microsoft por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="dac13-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="dac13-125">Armazenar a ID do cliente da Microsoft e o segredo do cliente</span><span class="sxs-lookup"><span data-stu-id="dac13-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="dac13-126">Execute os seguintes comandos para armazenar `ClientId` e `ClientSecret` usar o Gerenciador de [segredo](xref:security/app-secrets)com segurança:</span><span class="sxs-lookup"><span data-stu-id="dac13-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="dac13-127">Vincule configurações confidenciais como a `ClientId` Microsoft `ClientSecret` e à sua configuração de aplicativo usando o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="dac13-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="dac13-128">Para os fins deste exemplo, nomeie os tokens `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="dac13-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="dac13-129">Configurar a autenticação da conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dac13-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="dac13-130">Adicione o serviço de conta da `Startup.ConfigureServices`Microsoft a:</span><span class="sxs-lookup"><span data-stu-id="dac13-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="dac13-131">Consulte a referência da API do [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) para obter mais informações sobre as opções de configuração com suporte pela autenticação da conta da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dac13-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="dac13-132">Isso pode ser usado para solicitar informações diferentes sobre o usuário.</span><span class="sxs-lookup"><span data-stu-id="dac13-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="dac13-133">Conta de Entrar com a conta da Microsoft</span><span class="sxs-lookup"><span data-stu-id="dac13-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="dac13-134">Execute o e clique em **fazer logon**.</span><span class="sxs-lookup"><span data-stu-id="dac13-134">Run the and click **Log in**.</span></span> <span data-ttu-id="dac13-135">É exibida uma opção para entrar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dac13-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="dac13-136">Ao clicar em Microsoft, você será redirecionado para a Microsoft para autenticação.</span><span class="sxs-lookup"><span data-stu-id="dac13-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="dac13-137">Depois de entrar com sua conta da Microsoft (se ainda não tiver entrado), você será solicitado a permitir que o aplicativo acesse suas informações:</span><span class="sxs-lookup"><span data-stu-id="dac13-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="dac13-138">Toque em **Sim** e você será Redirecionado de volta para o site da Web onde você pode definir seu email.</span><span class="sxs-lookup"><span data-stu-id="dac13-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="dac13-139">Agora você está conectado usando suas credenciais da Microsoft:</span><span class="sxs-lookup"><span data-stu-id="dac13-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="dac13-140">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="dac13-140">Troubleshooting</span></span>

* <span data-ttu-id="dac13-141">Se o provedor de conta da Microsoft o redireciona para uma página de erro de entrada, observe os parâmetros título e descrição da cadeia de caracteres de `#` consulta diretamente seguindo o (hashtag) no URI.</span><span class="sxs-lookup"><span data-stu-id="dac13-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="dac13-142">Embora a mensagem de erro pareça indicar um problema com a autenticação da Microsoft, a causa mais comum é o URI do aplicativo não corresponder a nenhum dos **URIs de redirecionamento** especificados para a plataforma **da Web** .</span><span class="sxs-lookup"><span data-stu-id="dac13-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="dac13-143">Se a identidade não estiver configurada chamando `services.AddIdentity` em `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção ' SignInScheme ' deve ser fornecida*.</span><span class="sxs-lookup"><span data-stu-id="dac13-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="dac13-144">O modelo de projeto usado neste exemplo garante que isso seja feito.</span><span class="sxs-lookup"><span data-stu-id="dac13-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="dac13-145">Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro.</span><span class="sxs-lookup"><span data-stu-id="dac13-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="dac13-146">Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.</span><span class="sxs-lookup"><span data-stu-id="dac13-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dac13-147">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="dac13-147">Next steps</span></span>

* <span data-ttu-id="dac13-148">Este artigo mostrou como você pode autenticar com a Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dac13-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="dac13-149">Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="dac13-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="dac13-150">Depois de publicar seu site no aplicativo Web do Azure, crie novos segredos de cliente no portal do desenvolvedor da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="dac13-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="dac13-151">Defina as `Authentication:Microsoft:ClientId` e `Authentication:Microsoft:ClientSecret` como configurações de aplicativo no portal do Azure.</span><span class="sxs-lookup"><span data-stu-id="dac13-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="dac13-152">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="dac13-152">The configuration system is set up to read keys from environment variables.</span></span>