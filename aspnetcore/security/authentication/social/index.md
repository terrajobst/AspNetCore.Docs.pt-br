---
title: Autenticação de Facebook, Google e de provedor externo no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra como criar um aplicativo do ASP.NET Core 2.x usando o OAuth 2.0 com provedores de autenticação externa.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082514"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="0beb7-103">Autenticação de Facebook, Google e de provedor externo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0beb7-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="0beb7-104">Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0beb7-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0beb7-105">Este tutorial demonstra como criar um aplicativo ASP.NET Core 2.2, que permite aos usuários se conectarem usando o OAuth 2.0 com as credenciais de provedores de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="0beb7-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="0beb7-106">Os provedores [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) e [Microsoft](xref:security/authentication/microsoft-logins) são abordados nas seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="0beb7-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="0beb7-107">Outros provedores estão disponíveis em pacotes de terceiros, como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span><span class="sxs-lookup"><span data-stu-id="0beb7-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Ícones de mídia social do Facebook, Twitter, Google+ e Windows](index/_static/social.png)

<span data-ttu-id="0beb7-109">Permitir que os usuários entrem com suas credenciais existentes:</span><span class="sxs-lookup"><span data-stu-id="0beb7-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="0beb7-110">É conveniente para os usuários.</span><span class="sxs-lookup"><span data-stu-id="0beb7-110">Is convenient for the users.</span></span>
* <span data-ttu-id="0beb7-111">Remove muitas das complexidades de gerenciar o processo de conexão para um terceiro.</span><span class="sxs-lookup"><span data-stu-id="0beb7-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="0beb7-112">Para obter exemplos de como os logons sociais podem impulsionar o tráfego e as conversões de clientes, consulte os estudos de caso do [Facebook](https://www.facebook.com/unsupportedbrowser) e do [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="0beb7-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="0beb7-113">Criar um novo projeto ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0beb7-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0beb7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0beb7-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0beb7-115">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="0beb7-115">Create a new project.</span></span>
* <span data-ttu-id="0beb7-116">Selecione **Aplicativo Web ASP.NET Core** e **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="0beb7-117">Forneça um **Nome do projeto** e confirme ou altere a **localização**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="0beb7-118">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-118">Select **Create**.</span></span>
* <span data-ttu-id="0beb7-119">Selecione **ASP.NET Core 2.2** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="0beb7-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="0beb7-120">Selecione **aplicativo Web** na lista de modelos.</span><span class="sxs-lookup"><span data-stu-id="0beb7-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="0beb7-121">Selecione **Autenticação** selecione **Mudar** e configure a autenticação para **Contas de Usuário Individuais**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="0beb7-122">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-122">Select **OK**.</span></span>
* <span data-ttu-id="0beb7-123">Na janela **Criar novo aplicativo Web ASP.NET Core**, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0beb7-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0beb7-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0beb7-125">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="0beb7-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0beb7-126">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="0beb7-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="0beb7-127">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0beb7-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="0beb7-128">O comando `dotnet new` cria um projeto do Razor Pages na pasta *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="0beb7-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="0beb7-129">`-uld` usa o LocalDB em vez do SQLite.</span><span class="sxs-lookup"><span data-stu-id="0beb7-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="0beb7-130">Omita `-uld` para usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="0beb7-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="0beb7-131">`-au Individual` cria o código para autenticação individual.</span><span class="sxs-lookup"><span data-stu-id="0beb7-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="0beb7-132">O comando `code` abre a pasta *WebApp1* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0beb7-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="0beb7-133">Uma caixa de diálogo é exibida com a mensagem **Os ativos necessários para build e depuração estão ausentes no 'WebApp1'. Deseja adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="0beb7-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="0beb7-134">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="0beb7-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0beb7-135">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0beb7-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0beb7-136">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="0beb7-137">Selecione o  > **aplicativo** **.NET Core** na barra lateral.</span><span class="sxs-lookup"><span data-stu-id="0beb7-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="0beb7-138">Selecione o modelo **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-138">Select the **Web Application** template.</span></span> <span data-ttu-id="0beb7-139">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-139">Select **Next**.</span></span>
* <span data-ttu-id="0beb7-140">Defina a **estrutura de destino**, na lista suspensa busque o **.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="0beb7-141">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-141">Select **Next**.</span></span>
* <span data-ttu-id="0beb7-142">Forneça um **Nome de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-142">Provide a **Project Name**.</span></span> <span data-ttu-id="0beb7-143">Confirme ou altere a **localização**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="0beb7-144">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="0beb7-145">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="0beb7-145">Apply migrations</span></span>

* <span data-ttu-id="0beb7-146">Execute o aplicativo e selecione o link **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="0beb7-147">Insira o email e a senha para a nova conta e, em seguida, selecione **Registrar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="0beb7-148">Siga as instruções para aplicar as migrações.</span><span class="sxs-lookup"><span data-stu-id="0beb7-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="0beb7-149">Usar o SecretManager para armazenar os tokens atribuídos por provedores de logon</span><span class="sxs-lookup"><span data-stu-id="0beb7-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="0beb7-150">Os provedores de logon social atribuem tokens de **ID do Aplicativo** e **Segredo do Aplicativo** durante o processo de registro.</span><span class="sxs-lookup"><span data-stu-id="0beb7-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="0beb7-151">Os nomes de token exatos variam de acordo com o provedor.</span><span class="sxs-lookup"><span data-stu-id="0beb7-151">The exact token names vary by provider.</span></span> <span data-ttu-id="0beb7-152">Esses tokens representam as credenciais que seu aplicativo usa para acessar a API.</span><span class="sxs-lookup"><span data-stu-id="0beb7-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="0beb7-153">Os tokens constituem os "segredos" que podem ser vinculados à configuração de aplicativo com a ajuda do [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="0beb7-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="0beb7-154">O Secret Manager é uma alternativa mais segura para armazenar os tokens em um arquivo de configuração, como *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0beb7-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0beb7-155">O Secret Manager destina-se apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0beb7-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="0beb7-156">Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="0beb7-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="0beb7-157">Siga as etapas no tópico [Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core](xref:security/app-secrets) para armazenar os tokens atribuídos por cada provedor de logon abaixo.</span><span class="sxs-lookup"><span data-stu-id="0beb7-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="0beb7-158">Configurar os provedores de logon necessários para o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0beb7-158">Setup login providers required by your application</span></span>

<span data-ttu-id="0beb7-159">Use os seguintes tópicos para configurar seu aplicativo para usar os respectivos provedores:</span><span class="sxs-lookup"><span data-stu-id="0beb7-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="0beb7-160">Instruções do [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="0beb7-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="0beb7-161">Instruções do [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="0beb7-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="0beb7-162">Instruções do [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="0beb7-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="0beb7-163">Instruções da [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="0beb7-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="0beb7-164">[Outras](xref:security/authentication/otherlogins) instruções do provedor</span><span class="sxs-lookup"><span data-stu-id="0beb7-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="0beb7-165">Definir a senha opcionalmente</span><span class="sxs-lookup"><span data-stu-id="0beb7-165">Optionally set password</span></span>

<span data-ttu-id="0beb7-166">Ao registrar um provedor de logon externo, você não precisa ter uma senha registrada no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0beb7-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="0beb7-167">Isso o alivia da tarefa de criar e lembrar de uma senha para o site, mas também o torna dependente do provedor de logon externo.</span><span class="sxs-lookup"><span data-stu-id="0beb7-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="0beb7-168">Se o provedor de logon externo não estiver disponível, você não poderá se conectar ao site.</span><span class="sxs-lookup"><span data-stu-id="0beb7-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="0beb7-169">Para criar uma senha e entrar usando seu email definido durante o processo de entrada com provedores externos:</span><span class="sxs-lookup"><span data-stu-id="0beb7-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="0beb7-170">Selecione o link **Olá,&lt; &gt;alias de email** no canto superior direito para navegar até a exibição **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="0beb7-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Exibição Gerenciar do Aplicativo Web](index/_static/pass1a.png)

* <span data-ttu-id="0beb7-172">Selecione **Criar**</span><span class="sxs-lookup"><span data-stu-id="0beb7-172">Select **Create**</span></span>

![Definir a página de senha](index/_static/pass2a.png)

* <span data-ttu-id="0beb7-174">Defina uma senha válida e use-a para entrar com seu email.</span><span class="sxs-lookup"><span data-stu-id="0beb7-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0beb7-175">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="0beb7-175">Next steps</span></span>

* <span data-ttu-id="0beb7-176">Este artigo apresentou a autenticação externa e explicou os pré-requisitos necessários para adicionar logons externos ao aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0beb7-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="0beb7-177">Páginas de referência específicas ao provedor para configurar logons para os provedores necessários para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0beb7-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="0beb7-178">Você talvez queira manter os dados adicionais sobre o usuário e seus tokens de atualização e acesso.</span><span class="sxs-lookup"><span data-stu-id="0beb7-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="0beb7-179">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="0beb7-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
