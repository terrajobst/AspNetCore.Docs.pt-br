---
title: Autenticação de Facebook, Google e de provedor externo no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra como criar um aplicativo ASP.NET Core usando o OAuth 2,0 com provedores de autenticação externa.
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: security/authentication/social/index
ms.openlocfilehash: 06ce9c72f43955345efb61afed2538158810005f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358063"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="2ba1e-103">Autenticação de Facebook, Google e de provedor externo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ba1e-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="2ba1e-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2ba1e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2ba1e-105">Este tutorial demonstra como criar um aplicativo ASP.NET Core 3,0 que permite que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="2ba1e-106">Os provedores do [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)e [Microsoft](xref:security/authentication/microsoft-logins) são abordados nas seções a seguir e usam o projeto inicial criado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="2ba1e-107">Outros provedores estão disponíveis em pacotes de terceiros, como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="2ba1e-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="2ba1e-108">Permitir que os usuários entrem com suas credenciais existentes:</span><span class="sxs-lookup"><span data-stu-id="2ba1e-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="2ba1e-109">É conveniente para os usuários.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-109">Is convenient for the users.</span></span>
* <span data-ttu-id="2ba1e-110">Remove muitas das complexidades de gerenciar o processo de conexão para um terceiro.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="2ba1e-111">Para obter exemplos de como os logons sociais podem impulsionar o tráfego e as conversões de clientes, consulte os estudos de caso do [Facebook](https://www.facebook.com/unsupportedbrowser) e do [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="2ba1e-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="2ba1e-112">Criar um novo projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ba1e-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ba1e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ba1e-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2ba1e-114">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-114">Create a new project.</span></span>
* <span data-ttu-id="2ba1e-115">Selecione **Aplicativo Web ASP.NET Core** e **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="2ba1e-116">Forneça um **Nome do projeto** e confirme ou altere a **localização**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="2ba1e-117">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-117">Select **Create**.</span></span>
* <span data-ttu-id="2ba1e-118">Selecione **ASP.NET Core 3,0** na lista suspensa e, em seguida, selecione **aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-118">Select **ASP.NET Core 3.0** in the drop-down, and then select **Web Application**.</span></span>
* <span data-ttu-id="2ba1e-119">Selecione **Autenticação** selecione **Mudar** e configure a autenticação para **Contas de Usuário Individuais**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="2ba1e-120">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-120">Select **OK**.</span></span>
* <span data-ttu-id="2ba1e-121">Na janela **Criar novo aplicativo Web ASP.NET Core**, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="2ba1e-122">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="2ba1e-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2ba1e-123">Abra o terminal.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-123">Open the terminal.</span></span>  <span data-ttu-id="2ba1e-124">Para Visual Studio Code você pode abrir o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2ba1e-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2ba1e-125">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="2ba1e-126">Para Windows, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2ba1e-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="2ba1e-127">Para macOS e Linux, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2ba1e-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="2ba1e-128">O comando `dotnet new` cria um projeto do Razor Pages na pasta *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="2ba1e-129">`-au Individual` cria o código para autenticação individual.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="2ba1e-130">`-uld` usa o LocalDB, uma versão leve do SQL Server Express para Windows.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="2ba1e-131">Omita `-uld` para usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="2ba1e-132">O comando `code` abre a pasta *WebApp1* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="2ba1e-133">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="2ba1e-133">Apply migrations</span></span>

* <span data-ttu-id="2ba1e-134">Execute o aplicativo e selecione o link **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="2ba1e-135">Insira o email e a senha para a nova conta e, em seguida, selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="2ba1e-136">Siga as instruções para aplicar as migrações.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="2ba1e-137">Usar o SecretManager para armazenar os tokens atribuídos por provedores de logon</span><span class="sxs-lookup"><span data-stu-id="2ba1e-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="2ba1e-138">Os provedores de logon social atribuem tokens de **ID do Aplicativo** e **Segredo do Aplicativo** durante o processo de registro.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="2ba1e-139">Os nomes de token exatos variam de acordo com o provedor.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-139">The exact token names vary by provider.</span></span> <span data-ttu-id="2ba1e-140">Esses tokens representam as credenciais que seu aplicativo usa para acessar a API.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="2ba1e-141">Os tokens constituem os "segredos" que podem ser vinculados à configuração de aplicativo com a ajuda do [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="2ba1e-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="2ba1e-142">O Secret Manager é uma alternativa mais segura para armazenar os tokens em um arquivo de configuração, como *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2ba1e-143">O Secret Manager destina-se apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="2ba1e-144">Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="2ba1e-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="2ba1e-145">Siga as etapas no tópico [Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core](xref:security/app-secrets) para armazenar os tokens atribuídos por cada provedor de logon abaixo.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="2ba1e-146">Configurar os provedores de logon necessários para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="2ba1e-146">Setup login providers required by your application</span></span>

<span data-ttu-id="2ba1e-147">Use os seguintes tópicos para configurar seu aplicativo para usar os respectivos provedores:</span><span class="sxs-lookup"><span data-stu-id="2ba1e-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="2ba1e-148">Instruções do [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="2ba1e-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="2ba1e-149">Instruções do [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="2ba1e-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="2ba1e-150">Instruções do [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="2ba1e-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="2ba1e-151">Instruções da [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="2ba1e-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="2ba1e-152">[Outras](xref:security/authentication/otherlogins) instruções do provedor</span><span class="sxs-lookup"><span data-stu-id="2ba1e-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="2ba1e-153">Definir a senha opcionalmente</span><span class="sxs-lookup"><span data-stu-id="2ba1e-153">Optionally set password</span></span>

<span data-ttu-id="2ba1e-154">Ao registrar um provedor de logon externo, você não precisa ter uma senha registrada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="2ba1e-155">Isso o alivia da tarefa de criar e lembrar de uma senha para o site, mas também o torna dependente do provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="2ba1e-156">Se o provedor de logon externo não estiver disponível, você não poderá se conectar ao site.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="2ba1e-157">Para criar uma senha e entrar usando seu email definido durante o processo de entrada com provedores externos:</span><span class="sxs-lookup"><span data-stu-id="2ba1e-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="2ba1e-158">Selecione o link **Olá,&lt;&gt;alias de email** no canto superior direito para navegar até a exibição **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Exibição Gerenciar do Aplicativo Web](index/_static/pass1a.png)

* <span data-ttu-id="2ba1e-160">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="2ba1e-160">Select **Create**</span></span>

![Definir a página de senha](index/_static/pass2a.png)

* <span data-ttu-id="2ba1e-162">Defina uma senha válida e use-a para entrar com seu email.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2ba1e-163">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="2ba1e-163">Next steps</span></span>

* <span data-ttu-id="2ba1e-164">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/10563) para obter informações sobre como personalizar os botões de logon.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-164">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="2ba1e-165">Este artigo apresentou a autenticação externa e explicou os pré-requisitos necessários para adicionar logons externos ao aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="2ba1e-166">Páginas de referência específicas ao provedor para configurar logons para os provedores necessários para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="2ba1e-167">Você talvez queira manter os dados adicionais sobre o usuário e seus tokens de atualização e acesso.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="2ba1e-168">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="2ba1e-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
