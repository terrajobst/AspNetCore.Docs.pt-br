---
title: Autenticação de Facebook, Google e de provedor externo no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra como criar um aplicativo do ASP.NET Core 2.x usando o OAuth 2.0 com provedores de autenticação externa.
ms.author: riande
ms.custom: mvc
ms.date: 4/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: 61482481358256dc9ddd1a0a894541040a8a452f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882001"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="3eb3b-103">Autenticação de Facebook, Google e de provedor externo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3eb3b-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="3eb3b-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3eb3b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3eb3b-105">Este tutorial demonstra como criar um aplicativo ASP.NET Core 2.2, que permite aos usuários se conectarem usando o OAuth 2.0 com as credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="3eb3b-106">Os provedores [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) e [Microsoft](xref:security/authentication/microsoft-logins) são abordados nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="3eb3b-107">Outros provedores estão disponíveis em pacotes de terceiros, como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="3eb3b-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Ícones de mídia social do Facebook, Twitter, Google+ e Windows](index/_static/social.png)

<span data-ttu-id="3eb3b-109">Permitir que os usuários entrem com suas credenciais existentes:</span><span class="sxs-lookup"><span data-stu-id="3eb3b-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="3eb3b-110">É conveniente para os usuários.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-110">Is convenient for the users.</span></span>
* <span data-ttu-id="3eb3b-111">Remove muitas das complexidades de gerenciar o processo de conexão para um terceiro.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="3eb3b-112">Para obter exemplos de como os logons sociais podem impulsionar o tráfego e as conversões de clientes, consulte os estudos de caso do [Facebook](https://www.facebook.com/unsupportedbrowser) e do [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="3eb3b-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="3eb3b-113">Criar um novo projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3eb3b-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3eb3b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3eb3b-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3eb3b-115">No menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3eb3b-116">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-116">Create a new ASP.NET Core Web Application.</span></span>
* <span data-ttu-id="3eb3b-117">Selecione **ASP.NET Core 2.2** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-117">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>
* <span data-ttu-id="3eb3b-118">Selecione **Alterar Autenticação** e defina a autenticação para **Contas de Usuário Individuais**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-118">Select **Change Authentication** and set authentication to **Individual User Accounts**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3eb3b-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3eb3b-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3eb3b-120">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3eb3b-120">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="3eb3b-121">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-121">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="3eb3b-122">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3eb3b-122">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o WebApp1
  code -r WebApp1
  ```

  * <span data-ttu-id="3eb3b-123">O comando `dotnet new` cria um projeto do Razor Pages na pasta *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-123">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="3eb3b-124">O comando `code` abre a pasta *WebApp1* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-124">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="3eb3b-125">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes no 'WebApp1'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="3eb3b-125">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span>

* <span data-ttu-id="3eb3b-126">Selecione **Sim**</span><span class="sxs-lookup"><span data-stu-id="3eb3b-126">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3eb3b-127">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3eb3b-127">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3eb3b-128">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3eb3b-128">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o WebApp1
```

<span data-ttu-id="3eb3b-129">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um projeto do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-129">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="3eb3b-130">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="3eb3b-130">Open the project</span></span>

<span data-ttu-id="3eb3b-131">No Visual Studio, selecione **Arquivo > Abrir** e, em seguida, selecione o arquivo *WebApp1.csproj*.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-131">From Visual Studio, select **File > Open**, and then select the *WebApp1.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="apply-migrations"></a><span data-ttu-id="3eb3b-132">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="3eb3b-132">Apply migrations</span></span>

* <span data-ttu-id="3eb3b-133">Execute o aplicativo e selecione o link **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-133">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="3eb3b-134">Insira o email e a senha para a nova conta e, em seguida, selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-134">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="3eb3b-135">Siga as instruções para aplicar as migrações.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-135">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="3eb3b-136">Usar o SecretManager para armazenar os tokens atribuídos por provedores de logon</span><span class="sxs-lookup"><span data-stu-id="3eb3b-136">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="3eb3b-137">Os provedores de logon social atribuem tokens de **ID do Aplicativo** e **Segredo do Aplicativo** durante o processo de registro.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-137">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="3eb3b-138">Os nomes de token exatos variam de acordo com o provedor.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-138">The exact token names vary by provider.</span></span> <span data-ttu-id="3eb3b-139">Esses tokens representam as credenciais que seu aplicativo usa para acessar a API.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-139">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="3eb3b-140">Os tokens constituem os "segredos" que podem ser vinculados à configuração de aplicativo com a ajuda do [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="3eb3b-140">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="3eb3b-141">O Secret Manager é uma alternativa mais segura para armazenar os tokens em um arquivo de configuração, como *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-141">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3eb3b-142">O Secret Manager destina-se apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-142">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="3eb3b-143">Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="3eb3b-143">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="3eb3b-144">Siga as etapas no tópico [Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core](xref:security/app-secrets) para armazenar os tokens atribuídos por cada provedor de logon abaixo.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-144">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="3eb3b-145">Configurar os provedores de logon necessários para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3eb3b-145">Setup login providers required by your application</span></span>

<span data-ttu-id="3eb3b-146">Use os seguintes tópicos para configurar seu aplicativo para usar os respectivos provedores:</span><span class="sxs-lookup"><span data-stu-id="3eb3b-146">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="3eb3b-147">Instruções do [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="3eb3b-147">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="3eb3b-148">Instruções do [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="3eb3b-148">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="3eb3b-149">Instruções do [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="3eb3b-149">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="3eb3b-150">Instruções da [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="3eb3b-150">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="3eb3b-151">[Outras](xref:security/authentication/otherlogins) instruções do provedor</span><span class="sxs-lookup"><span data-stu-id="3eb3b-151">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="3eb3b-152">Definir a senha opcionalmente</span><span class="sxs-lookup"><span data-stu-id="3eb3b-152">Optionally set password</span></span>

<span data-ttu-id="3eb3b-153">Ao registrar um provedor de logon externo, você não precisa ter uma senha registrada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-153">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="3eb3b-154">Isso o alivia da tarefa de criar e lembrar de uma senha para o site, mas também o torna dependente do provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-154">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="3eb3b-155">Se o provedor de logon externo não estiver disponível, você não poderá se conectar ao site.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-155">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="3eb3b-156">Para criar uma senha e entrar usando seu email definido durante o processo de entrada com provedores externos:</span><span class="sxs-lookup"><span data-stu-id="3eb3b-156">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="3eb3b-157">Selecione o link **Olá,&lt; &gt;alias de email** no canto superior direito para navegar até a exibição **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-157">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Exibição Gerenciar do Aplicativo Web](index/_static/pass1a.png)

* <span data-ttu-id="3eb3b-159">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="3eb3b-159">Select **Create**</span></span>

![Definir a página de senha](index/_static/pass2a.png)

* <span data-ttu-id="3eb3b-161">Defina uma senha válida e use-a para entrar com seu email.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-161">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3eb3b-162">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3eb3b-162">Next steps</span></span>

* <span data-ttu-id="3eb3b-163">Este artigo apresentou a autenticação externa e explicou os pré-requisitos necessários para adicionar logons externos ao aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-163">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="3eb3b-164">Páginas de referência específicas ao provedor para configurar logons para os provedores necessários para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-164">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="3eb3b-165">Você talvez queira manter os dados adicionais sobre o usuário e seus tokens de atualização e acesso.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-165">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="3eb3b-166">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="3eb3b-166">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
