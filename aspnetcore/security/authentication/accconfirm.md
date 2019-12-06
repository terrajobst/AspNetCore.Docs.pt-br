---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: a4ecc2d91fb72915703dfaa146260f0c1360bded
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880762"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="e1f10-103">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1f10-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="e1f10-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e1f10-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="e1f10-105">Este tutorial mostra como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="e1f10-106">Este tutorial **não** é um tópico inicial.</span><span class="sxs-lookup"><span data-stu-id="e1f10-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="e1f10-107">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="e1f10-107">You should be familiar with:</span></span>

* [<span data-ttu-id="e1f10-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1f10-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="e1f10-109">Autenticação</span><span class="sxs-lookup"><span data-stu-id="e1f10-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="e1f10-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e1f10-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="e1f10-111">Consulte [este arquivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="e1f10-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="e1f10-112">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="e1f10-112">Prerequisites</span></span>

[<span data-ttu-id="e1f10-113">SDK do .NET Core 3,0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e1f10-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="e1f10-114">Criar e testar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="e1f10-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="e1f10-115">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="e1f10-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="e1f10-116">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="e1f10-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="e1f10-117">Depois de registrado, você será redirecionado para a página para `/Identity/Account/RegisterConfirmation` que contém um link para simular a confirmação de email:</span><span class="sxs-lookup"><span data-stu-id="e1f10-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="e1f10-118">Selecione o link `Click here to confirm your account`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="e1f10-119">Selecione o link de **logon** e entre com as mesmas credenciais.</span><span class="sxs-lookup"><span data-stu-id="e1f10-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="e1f10-120">Selecione o link `Hello YourEmail@provider.com!`, que o redireciona para a página `/Identity/Account/Manage/PersonalData`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="e1f10-121">Selecione a guia **dados pessoais** à esquerda e, em seguida, selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="e1f10-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="e1f10-122">Configurar um provedor de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-122">Configure an email provider</span></span>

<span data-ttu-id="e1f10-123">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="e1f10-124">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="e1f10-125">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-125">You can use other email providers.</span></span> <span data-ttu-id="e1f10-126">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="e1f10-127">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="e1f10-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="e1f10-128">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="e1f10-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="e1f10-129">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e1f10-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="e1f10-130">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="e1f10-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="e1f10-131">Defina o `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e1f10-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e1f10-132">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1f10-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="e1f10-133">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um arquivo *segredos. JSON* no diretório `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="e1f10-134">O conteúdo do arquivo *segredos. JSON* não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="e1f10-135">A marcação a seguir mostra o arquivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e1f10-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="e1f10-136">O valor de `SendGridKey` foi removido.</span><span class="sxs-lookup"><span data-stu-id="e1f10-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="e1f10-137">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e1f10-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="e1f10-138">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="e1f10-138">Install SendGrid</span></span>

<span data-ttu-id="e1f10-139">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="e1f10-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="e1f10-140">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="e1f10-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e1f10-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1f10-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1f10-142">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e1f10-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e1f10-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e1f10-144">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="e1f10-145">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="e1f10-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="e1f10-146">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="e1f10-146">Implement IEmailSender</span></span>

<span data-ttu-id="e1f10-147">Para implementar `IEmailSender`, crie *Services/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e1f10-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="e1f10-148">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="e1f10-148">Configure startup to support email</span></span>

<span data-ttu-id="e1f10-149">Adicione o seguinte código ao método `ConfigureServices` no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1f10-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="e1f10-150">Adicione `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="e1f10-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="e1f10-151">Registre a instância de configuração de `AuthMessageSenderOptions`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="e1f10-152">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="e1f10-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="e1f10-153">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="e1f10-154">Execute o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="e1f10-154">Run the app and register a new user</span></span>
* <span data-ttu-id="e1f10-155">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="e1f10-156">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="e1f10-157">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="e1f10-158">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="e1f10-159">Saia.</span><span class="sxs-lookup"><span data-stu-id="e1f10-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="e1f10-160">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="e1f10-160">Test password reset</span></span>

* <span data-ttu-id="e1f10-161">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="e1f10-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="e1f10-162">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="e1f10-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="e1f10-163">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="e1f10-164">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="e1f10-165">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="e1f10-166">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="e1f10-167">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="e1f10-167">Change email and activity timeout</span></span>

<span data-ttu-id="e1f10-168">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="e1f10-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="e1f10-169">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="e1f10-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="e1f10-170">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="e1f10-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="e1f10-171">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="e1f10-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="e1f10-172">Os tokens de usuário de identidade interna (consulte [AspNetCore/src/Identity/Extensions. Core/src/tokenoptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e1f10-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="e1f10-173">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-173">Change the email token lifespan</span></span>

<span data-ttu-id="e1f10-174">A vida útil do token padrão dos [tokens de usuário de identidade](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e1f10-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="e1f10-175">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="e1f10-176">Adicione um [DataProtectorTokenProvider personalizado\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="e1f10-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="e1f10-177">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="e1f10-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="e1f10-178">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-178">Resend email confirmation</span></span>

<span data-ttu-id="e1f10-179">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="e1f10-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="e1f10-180">Depurar email</span><span class="sxs-lookup"><span data-stu-id="e1f10-180">Debug email</span></span>

<span data-ttu-id="e1f10-181">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-181">If you can't get email working:</span></span>

* <span data-ttu-id="e1f10-182">Defina um ponto de interrupção no `EmailSender.Execute` para verificar se `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="e1f10-183">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="e1f10-184">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="e1f10-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="e1f10-185">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="e1f10-185">Check your spam folder.</span></span>
* <span data-ttu-id="e1f10-186">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="e1f10-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="e1f10-187">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="e1f10-188">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e1f10-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="e1f10-189">Se você publicar o aplicativo no Azure, defina os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f10-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="e1f10-190">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1f10-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="e1f10-191">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="e1f10-191">Combine social and local login accounts</span></span>

<span data-ttu-id="e1f10-192">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="e1f10-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="e1f10-193">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e1f10-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e1f10-194">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="e1f10-195">Na sequência a seguir, "RickAndMSFT@gmail.com" é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="e1f10-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="e1f10-197">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="e1f10-197">Click on the **Manage** link.</span></span> <span data-ttu-id="e1f10-198">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-198">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="e1f10-200">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1f10-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="e1f10-201">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="e1f10-201">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="e1f10-203">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-203">The two accounts have been combined.</span></span> <span data-ttu-id="e1f10-204">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-204">You are able to sign in with either account.</span></span> <span data-ttu-id="e1f10-205">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="e1f10-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="e1f10-206">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="e1f10-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="e1f10-207">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="e1f10-208">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="e1f10-209">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e1f10-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="e1f10-210">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="e1f10-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="e1f10-211">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-211">Confirm existing users.</span></span> <span data-ttu-id="e1f10-212">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="e1f10-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e1f10-213">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="e1f10-213">Prerequisites</span></span>

[<span data-ttu-id="e1f10-214">SDK do .NET Core 2,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e1f10-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="e1f10-215">Criar um aplicativo Web e uma identidade Scaffold</span><span class="sxs-lookup"><span data-stu-id="e1f10-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="e1f10-216">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="e1f10-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="e1f10-217">Testar novo registro de usuário</span><span class="sxs-lookup"><span data-stu-id="e1f10-217">Test new user registration</span></span>

<span data-ttu-id="e1f10-218">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="e1f10-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="e1f10-219">Neste ponto, a única validação no email é com o atributo [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) .</span><span class="sxs-lookup"><span data-stu-id="e1f10-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="e1f10-220">Depois de enviar o registro, você está conectado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1f10-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="e1f10-221">Posteriormente no tutorial, o código será atualizado para que novos usuários não possam entrar até que seu email seja validado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="e1f10-222">Observe que o campo de `EmailConfirmed` da tabela é `False`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="e1f10-223">Você pode querer usar esse email novamente na próxima etapa quando o aplicativo enviar um email de confirmação.</span><span class="sxs-lookup"><span data-stu-id="e1f10-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="e1f10-224">Clique com o botão direito do mouse na linha e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="e1f10-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="e1f10-225">A exclusão do alias de email torna mais fácil as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1f10-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="e1f10-226">Exigir confirmação de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-226">Require email confirmation</span></span>

<span data-ttu-id="e1f10-227">É uma prática recomendada confirmar o email de um novo registro de usuário.</span><span class="sxs-lookup"><span data-stu-id="e1f10-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="e1f10-228">A confirmação por email ajuda a verificar se eles não estão representando outra pessoa (ou seja, se eles não se registraram no email de outra pessoa).</span><span class="sxs-lookup"><span data-stu-id="e1f10-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="e1f10-229">Suponha que você tenha um fórum de discussão e queira impedir "yli@example.com" de se registrar como "nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="e1f10-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="e1f10-230">Sem confirmação por email, "nolivetto@contoso.com" pode receber emails indesejados de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1f10-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="e1f10-231">Suponha que o usuário se registrou acidentalmente como "ylo@example.com" e não tenha notado a grafia incorreta de "Yli".</span><span class="sxs-lookup"><span data-stu-id="e1f10-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="e1f10-232">Eles não poderão usar a recuperação de senha porque o aplicativo não tem seu email correto.</span><span class="sxs-lookup"><span data-stu-id="e1f10-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="e1f10-233">A confirmação por email fornece proteção limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="e1f10-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="e1f10-234">A confirmação por email não fornece proteção contra usuários mal-intencionados com muitas contas de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="e1f10-235">Geralmente você deseja impedir que novos usuários incluam dados em seu site até que eles tenham um email confirmado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="e1f10-236">Atualize `Startup.ConfigureServices` para exigir um email confirmado:</span><span class="sxs-lookup"><span data-stu-id="e1f10-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="e1f10-237">`config.SignIn.RequireConfirmedEmail = true;` impede que os usuários registrados façam logon até que seu email seja confirmado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="e1f10-238">Configurar provedor de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-238">Configure email provider</span></span>

<span data-ttu-id="e1f10-239">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="e1f10-240">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="e1f10-241">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-241">You can use other email providers.</span></span> <span data-ttu-id="e1f10-242">O ASP.NET Core 2. x inclui `System.Net.Mail`, que permite enviar emails do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1f10-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="e1f10-243">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="e1f10-244">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="e1f10-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="e1f10-245">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="e1f10-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="e1f10-246">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e1f10-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="e1f10-247">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="e1f10-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="e1f10-248">Defina o `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e1f10-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e1f10-249">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1f10-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="e1f10-250">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um arquivo *segredos. JSON* no diretório `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="e1f10-251">O conteúdo do arquivo *segredos. JSON* não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="e1f10-252">A marcação a seguir mostra o arquivo *Secrets. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e1f10-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="e1f10-253">O valor de `SendGridKey` foi removido.</span><span class="sxs-lookup"><span data-stu-id="e1f10-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="e1f10-254">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e1f10-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="e1f10-255">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="e1f10-255">Install SendGrid</span></span>

<span data-ttu-id="e1f10-256">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="e1f10-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="e1f10-257">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="e1f10-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e1f10-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1f10-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e1f10-259">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e1f10-260">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e1f10-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e1f10-261">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="e1f10-262">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="e1f10-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="e1f10-263">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="e1f10-263">Implement IEmailSender</span></span>

<span data-ttu-id="e1f10-264">Para implementar `IEmailSender`, crie *Services/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e1f10-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="e1f10-265">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="e1f10-265">Configure startup to support email</span></span>

<span data-ttu-id="e1f10-266">Adicione o seguinte código ao método `ConfigureServices` no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e1f10-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="e1f10-267">Adicione `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="e1f10-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="e1f10-268">Registre a instância de configuração de `AuthMessageSenderOptions`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="e1f10-269">Habilitar confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="e1f10-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="e1f10-270">O modelo tem o código para confirmação de conta e recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="e1f10-271">Localize o método `OnPostAsync` em *áreas/identidade/páginas/conta/registro. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="e1f10-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="e1f10-272">Impedir que usuários registrados recentemente façam logon automaticamente comentando a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="e1f10-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="e1f10-273">O método Complete é mostrado com a linha alterada realçada:</span><span class="sxs-lookup"><span data-stu-id="e1f10-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="e1f10-274">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="e1f10-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="e1f10-275">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="e1f10-276">Execute o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="e1f10-276">Run the app and register a new user</span></span>
* <span data-ttu-id="e1f10-277">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="e1f10-278">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="e1f10-279">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="e1f10-280">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="e1f10-281">Saia.</span><span class="sxs-lookup"><span data-stu-id="e1f10-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="e1f10-282">Exibir a página Gerenciar</span><span class="sxs-lookup"><span data-stu-id="e1f10-282">View the manage page</span></span>

<span data-ttu-id="e1f10-283">Selecione seu nome de usuário no navegador: ![janela do navegador com o nome de usuário](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="e1f10-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="e1f10-284">A página Gerenciar é exibida com a guia **perfil** selecionada.</span><span class="sxs-lookup"><span data-stu-id="e1f10-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="e1f10-285">O **email** mostra uma caixa de seleção indicando que o email foi confirmado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="e1f10-286">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="e1f10-286">Test password reset</span></span>

* <span data-ttu-id="e1f10-287">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="e1f10-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="e1f10-288">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="e1f10-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="e1f10-289">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="e1f10-290">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="e1f10-291">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="e1f10-292">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="e1f10-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="e1f10-293">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="e1f10-293">Change email and activity timeout</span></span>

<span data-ttu-id="e1f10-294">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="e1f10-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="e1f10-295">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="e1f10-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="e1f10-296">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="e1f10-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="e1f10-297">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="e1f10-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="e1f10-298">Os tokens de usuário de identidade interna (consulte [AspNetCore/src/Identity/Extensions. Core/src/tokenoptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e1f10-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="e1f10-299">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-299">Change the email token lifespan</span></span>

<span data-ttu-id="e1f10-300">A vida útil do token padrão dos [tokens de usuário de identidade](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e1f10-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="e1f10-301">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="e1f10-302">Adicione um [DataProtectorTokenProvider personalizado\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="e1f10-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="e1f10-303">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="e1f10-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="e1f10-304">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="e1f10-304">Resend email confirmation</span></span>

<span data-ttu-id="e1f10-305">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="e1f10-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="e1f10-306">Depurar email</span><span class="sxs-lookup"><span data-stu-id="e1f10-306">Debug email</span></span>

<span data-ttu-id="e1f10-307">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="e1f10-307">If you can't get email working:</span></span>

* <span data-ttu-id="e1f10-308">Defina um ponto de interrupção no `EmailSender.Execute` para verificar se `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="e1f10-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="e1f10-309">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="e1f10-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="e1f10-310">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="e1f10-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="e1f10-311">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="e1f10-311">Check your spam folder.</span></span>
* <span data-ttu-id="e1f10-312">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="e1f10-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="e1f10-313">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="e1f10-314">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e1f10-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="e1f10-315">Se você publicar o aplicativo no Azure, poderá definir os segredos do SendGrid como configurações do aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="e1f10-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="e1f10-316">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="e1f10-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="e1f10-317">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="e1f10-317">Combine social and local login accounts</span></span>

<span data-ttu-id="e1f10-318">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="e1f10-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="e1f10-319">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e1f10-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e1f10-320">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="e1f10-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="e1f10-321">Na sequência a seguir, "RickAndMSFT@gmail.com" é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="e1f10-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="e1f10-323">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="e1f10-323">Click on the **Manage** link.</span></span> <span data-ttu-id="e1f10-324">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="e1f10-324">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="e1f10-326">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e1f10-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="e1f10-327">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="e1f10-327">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="e1f10-329">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-329">The two accounts have been combined.</span></span> <span data-ttu-id="e1f10-330">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-330">You are able to sign in with either account.</span></span> <span data-ttu-id="e1f10-331">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="e1f10-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="e1f10-332">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="e1f10-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="e1f10-333">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="e1f10-334">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="e1f10-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="e1f10-335">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e1f10-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="e1f10-336">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="e1f10-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="e1f10-337">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="e1f10-337">Confirm existing users.</span></span> <span data-ttu-id="e1f10-338">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="e1f10-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
