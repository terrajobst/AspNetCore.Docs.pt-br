---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email.
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 3bfc2ce46cfbc2ee308940f9e04eb2ffeec09073
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265492"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="4f598-103">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f598-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="4f598-104">Ver [esse arquivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para o ASP.NET Core 1.1 e a versão 2.1.</span><span class="sxs-lookup"><span data-stu-id="4f598-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="4f598-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="4f598-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="4f598-106">Este tutorial mostra como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email.</span><span class="sxs-lookup"><span data-stu-id="4f598-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="4f598-107">Este tutorial é **não** um tópico de início.</span><span class="sxs-lookup"><span data-stu-id="4f598-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="4f598-108">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="4f598-108">You should be familiar with:</span></span>

* [<span data-ttu-id="4f598-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f598-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4f598-110">Autenticação</span><span class="sxs-lookup"><span data-stu-id="4f598-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="4f598-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="4f598-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="4f598-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4f598-112">Prerequisites</span></span>

[<span data-ttu-id="4f598-113">SDK do .NET core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="4f598-113">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="4f598-114">Criar um aplicativo web e o scaffolding de identidade</span><span class="sxs-lookup"><span data-stu-id="4f598-114">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="4f598-115">Execute os seguintes comandos para criar um aplicativo web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="4f598-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="4f598-116">Novo registro de usuário de teste</span><span class="sxs-lookup"><span data-stu-id="4f598-116">Test new user registration</span></span>

<span data-ttu-id="4f598-117">Execute o aplicativo, selecione a **registrar** vincular e registrar um usuário.</span><span class="sxs-lookup"><span data-stu-id="4f598-117">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="4f598-118">Neste ponto, a validação apenas no email é com o [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="4f598-118">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="4f598-119">Depois de enviar o registro, você está conectado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f598-119">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="4f598-120">Posteriormente no tutorial, o código é atualizado para que novos usuários poderão entrar depois que o email é validado.</span><span class="sxs-lookup"><span data-stu-id="4f598-120">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="4f598-121">Observe a tabela `EmailConfirmed` campo é `False`.</span><span class="sxs-lookup"><span data-stu-id="4f598-121">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="4f598-122">Você talvez queira usar este email novamente na próxima etapa quando o aplicativo envia um email de confirmação.</span><span class="sxs-lookup"><span data-stu-id="4f598-122">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="4f598-123">Clique com botão direito na linha e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="4f598-123">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="4f598-124">Excluir o alias de email torna mais fácil nas etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="4f598-124">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="4f598-125">Exigir email de confirmação</span><span class="sxs-lookup"><span data-stu-id="4f598-125">Require email confirmation</span></span>

<span data-ttu-id="4f598-126">É uma prática recomendada para confirmar o email de um novo registro de usuário.</span><span class="sxs-lookup"><span data-stu-id="4f598-126">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="4f598-127">Ajuda a confirmação para verificar se eles não estiver representando alguém de email (ou seja, eles ainda não registrados com o email de outra pessoa).</span><span class="sxs-lookup"><span data-stu-id="4f598-127">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="4f598-128">Suponha que você tinha um fórum de discussão e quisesse impedir "yli@example.com"de registrar-se como"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="4f598-128">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="4f598-129">Sem email de confirmação "nolivetto@contoso.com" pode receber emails indesejados de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f598-129">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="4f598-130">Suponha que o usuário registrado acidentalmente como "ylo@example.com" e ainda não tenha notado o erro de ortografia de "yli".</span><span class="sxs-lookup"><span data-stu-id="4f598-130">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="4f598-131">Eles não seria capazes de usar a recuperação de senha porque o aplicativo não tiver seu email correto.</span><span class="sxs-lookup"><span data-stu-id="4f598-131">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="4f598-132">Email de confirmação oferece proteção limitada contra bots.</span><span class="sxs-lookup"><span data-stu-id="4f598-132">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="4f598-133">Email de confirmação não fornece proteção contra usuários mal-intencionados com muitas contas de email.</span><span class="sxs-lookup"><span data-stu-id="4f598-133">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="4f598-134">Geralmente você deseja impedir que novos usuários incluam dados em seu site até que eles tenham um email confirmado.</span><span class="sxs-lookup"><span data-stu-id="4f598-134">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="4f598-135">Atualização `Startup.ConfigureServices` para exigir um email confirmado:</span><span class="sxs-lookup"><span data-stu-id="4f598-135">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="4f598-136">`config.SignIn.RequireConfirmedEmail = true;` impede que os usuários registrados entrar até que o email seja confirmado.</span><span class="sxs-lookup"><span data-stu-id="4f598-136">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="4f598-137">Configurar o provedor de email</span><span class="sxs-lookup"><span data-stu-id="4f598-137">Configure email provider</span></span>

<span data-ttu-id="4f598-138">Neste tutorial [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="4f598-138">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="4f598-139">Você precisa de uma conta do SendGrid e uma chave para enviar email.</span><span class="sxs-lookup"><span data-stu-id="4f598-139">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="4f598-140">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="4f598-140">You can use other email providers.</span></span> <span data-ttu-id="4f598-141">ASP.NET Core 2.x inclui `System.Net.Mail`, que permite que você enviar um email de seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f598-141">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="4f598-142">É recomendável que usar o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="4f598-142">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="4f598-143">SMTP é difícil proteger e configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="4f598-143">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="4f598-144">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="4f598-144">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="4f598-145">Para este exemplo, crie *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f598-145">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="4f598-146">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="4f598-146">Configure SendGrid user secrets</span></span>

<span data-ttu-id="4f598-147">Defina as `SendGridUser` e `SendGridKey` com o [ferramenta secret manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="4f598-147">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="4f598-148">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4f598-148">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="4f598-149">No Windows, o Secret Manager armazena os pares de chaves/valor em uma *Secrets* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span><span class="sxs-lookup"><span data-stu-id="4f598-149">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="4f598-150">O conteúdo a *Secrets* arquivo não são criptografadas.</span><span class="sxs-lookup"><span data-stu-id="4f598-150">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="4f598-151">A marcação a seguir mostra a *Secrets* arquivo.</span><span class="sxs-lookup"><span data-stu-id="4f598-151">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="4f598-152">O `SendGridKey` valor tiver sido removido.</span><span class="sxs-lookup"><span data-stu-id="4f598-152">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="4f598-153">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4f598-153">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="4f598-154">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="4f598-154">Install SendGrid</span></span>

<span data-ttu-id="4f598-155">Este tutorial mostra como adicionar notificações de email por meio [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="4f598-155">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="4f598-156">Instalar o `SendGrid` pacote do NuGet:</span><span class="sxs-lookup"><span data-stu-id="4f598-156">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4f598-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4f598-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4f598-158">No Console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="4f598-158">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4f598-159">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="4f598-159">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4f598-160">No console, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="4f598-160">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

------

<span data-ttu-id="4f598-161">Ver [inicie gratuitamente com o SendGrid](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="4f598-161">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="4f598-162">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="4f598-162">Implement IEmailSender</span></span>

<span data-ttu-id="4f598-163">Para implementar `IEmailSender`, crie *Services/EmailSender.cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="4f598-163">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="4f598-164">Configurar a inicialização para dar suporte a email</span><span class="sxs-lookup"><span data-stu-id="4f598-164">Configure startup to support email</span></span>

<span data-ttu-id="4f598-165">Adicione o seguinte código para o `ConfigureServices` método na *Startup.cs* arquivo:</span><span class="sxs-lookup"><span data-stu-id="4f598-165">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="4f598-166">Adicionar `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="4f598-166">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="4f598-167">Registrar o `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="4f598-167">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="4f598-168">Habilitar a recuperação de confirmação e a senha da conta</span><span class="sxs-lookup"><span data-stu-id="4f598-168">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="4f598-169">O modelo tem o código para recuperação de confirmação e a senha da conta.</span><span class="sxs-lookup"><span data-stu-id="4f598-169">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="4f598-170">Localizar o `OnPostAsync` método no *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="4f598-170">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="4f598-171">Impedir que usuários recém-registrados serem conectados automaticamente comentando a linha a seguir:</span><span class="sxs-lookup"><span data-stu-id="4f598-171">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="4f598-172">O método complete é mostrado com a linha alterada realçada:</span><span class="sxs-lookup"><span data-stu-id="4f598-172">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="4f598-173">Registrar, confirme se o email e redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="4f598-173">Register, confirm email, and reset password</span></span>

<span data-ttu-id="4f598-174">Executar o aplicativo web e testar o fluxo de recuperação de senha e confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="4f598-174">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="4f598-175">Execute o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="4f598-175">Run the app and register a new user</span></span>
* <span data-ttu-id="4f598-176">Verifique seu email para o link de confirmação de conta.</span><span class="sxs-lookup"><span data-stu-id="4f598-176">Check your email for the account confirmation link.</span></span> <span data-ttu-id="4f598-177">Ver [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="4f598-177">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="4f598-178">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="4f598-178">Click the link to confirm your email.</span></span>
* <span data-ttu-id="4f598-179">Entrar com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="4f598-179">Sign in with your email and password.</span></span>
* <span data-ttu-id="4f598-180">Saia do serviço.</span><span class="sxs-lookup"><span data-stu-id="4f598-180">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="4f598-181">Exibir a página Gerenciar</span><span class="sxs-lookup"><span data-stu-id="4f598-181">View the manage page</span></span>

<span data-ttu-id="4f598-182">Selecione seu nome de usuário no navegador: ![janela do navegador com o nome de usuário](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="4f598-182">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="4f598-183">A página Gerenciar é exibida com o **perfil** guia selecionada.</span><span class="sxs-lookup"><span data-stu-id="4f598-183">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="4f598-184">O **Email** mostra uma caixa de seleção que indica o email foi confirmada.</span><span class="sxs-lookup"><span data-stu-id="4f598-184">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="4f598-185">Teste a redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="4f598-185">Test password reset</span></span>

* <span data-ttu-id="4f598-186">Se você estiver conectado, selecione **Logout**.</span><span class="sxs-lookup"><span data-stu-id="4f598-186">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="4f598-187">Selecione o **login** link e selecione o **esqueceu sua senha?** link.</span><span class="sxs-lookup"><span data-stu-id="4f598-187">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="4f598-188">Insira o email usado para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="4f598-188">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="4f598-189">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="4f598-189">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="4f598-190">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="4f598-190">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="4f598-191">Depois que sua senha foi redefinida com êxito, você pode entrar com seu email e a nova senha.</span><span class="sxs-lookup"><span data-stu-id="4f598-191">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="4f598-192">Tempo limite de email e a atividade de alteração</span><span class="sxs-lookup"><span data-stu-id="4f598-192">Change email and activity timeout</span></span>

<span data-ttu-id="4f598-193">O tempo de limite de inatividade padrão é 14 dias.</span><span class="sxs-lookup"><span data-stu-id="4f598-193">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="4f598-194">O código a seguir define o tempo limite de inatividade para cinco dias:</span><span class="sxs-lookup"><span data-stu-id="4f598-194">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="4f598-195">Alterar todos os empreguem de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="4f598-195">Change all data protection token lifespans</span></span>

<span data-ttu-id="4f598-196">O código a seguir altera todos os dados proteção tokens tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="4f598-196">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="4f598-197">Internos em tokens de identidade do usuário (consulte [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tem um [tempo limite de um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="4f598-197">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="4f598-198">Alterar o tempo de vida de token de email</span><span class="sxs-lookup"><span data-stu-id="4f598-198">Change the email token lifespan</span></span>

<span data-ttu-id="4f598-199">O tempo de vida de token no padrão de [os tokens de identidade do usuário](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é [um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="4f598-199">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="4f598-200">Esta seção mostra como alterar o tempo de vida de token de email.</span><span class="sxs-lookup"><span data-stu-id="4f598-200">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="4f598-201">Adicionar um personalizado [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="4f598-201">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="4f598-202">Adicione o provedor personalizado para o contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="4f598-202">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="4f598-203">Reenviar email de confirmação</span><span class="sxs-lookup"><span data-stu-id="4f598-203">Resend email confirmation</span></span>

<span data-ttu-id="4f598-204">Ver [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="4f598-204">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="4f598-205">Depurar o email</span><span class="sxs-lookup"><span data-stu-id="4f598-205">Debug email</span></span>

<span data-ttu-id="4f598-206">Se você não é possível obter o trabalho de email:</span><span class="sxs-lookup"><span data-stu-id="4f598-206">If you can't get email working:</span></span>

* <span data-ttu-id="4f598-207">Defina um ponto de interrupção no `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="4f598-207">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="4f598-208">Criar uma [aplicativo de console para enviar email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando um código semelhante ao `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="4f598-208">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="4f598-209">Examine os [atividade de Email](https://sendgrid.com/docs/User_Guide/email_activity.html) página.</span><span class="sxs-lookup"><span data-stu-id="4f598-209">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="4f598-210">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="4f598-210">Check your spam folder.</span></span>
* <span data-ttu-id="4f598-211">Tente outro alias de email em outro provedor de email (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="4f598-211">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="4f598-212">Tente enviar para contas de email diferente.</span><span class="sxs-lookup"><span data-stu-id="4f598-212">Try sending to different email accounts.</span></span>

<span data-ttu-id="4f598-213">**Uma prática recomendada de segurança** é **não** use segredos de produção em desenvolvimento e teste.</span><span class="sxs-lookup"><span data-stu-id="4f598-213">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="4f598-214">Se você publicar o aplicativo no Azure, você pode definir os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="4f598-214">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="4f598-215">Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="4f598-215">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="4f598-216">Combine as contas de logon social e local</span><span class="sxs-lookup"><span data-stu-id="4f598-216">Combine social and local login accounts</span></span>

<span data-ttu-id="4f598-217">Para concluir esta seção, você deve primeiro habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="4f598-217">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="4f598-218">Ver [Facebook, Google e a autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="4f598-218">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="4f598-219">Você pode combinar as contas locais e sociais clicando no link seu email.</span><span class="sxs-lookup"><span data-stu-id="4f598-219">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="4f598-220">Na sequência a seguir, "RickAndMSFT@gmail.com" é criado como um logon local; no entanto, você pode criar a conta como um logon social primeiro e adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="4f598-220">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="4f598-222">Clique no **gerenciar** link.</span><span class="sxs-lookup"><span data-stu-id="4f598-222">Click on the **Manage** link.</span></span> <span data-ttu-id="4f598-223">Observe externo 0 (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="4f598-223">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar o modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="4f598-225">Clique no link para outro serviço de logon e aceitar as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4f598-225">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="4f598-226">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="4f598-226">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seu modo de exibição de logons externos lista Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="4f598-228">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="4f598-228">The two accounts have been combined.</span></span> <span data-ttu-id="4f598-229">É possível entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="4f598-229">You are able to sign in with either account.</span></span> <span data-ttu-id="4f598-230">Convém que os usuários adicionem contas locais no caso de seu serviço de autenticação de logon social está inoperante ou, mais provavelmente eles tiver perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="4f598-230">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="4f598-231">Habilitar confirmação de conta depois que um site tem usuários</span><span class="sxs-lookup"><span data-stu-id="4f598-231">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="4f598-232">Habilitando a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="4f598-232">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="4f598-233">Os usuários existentes estão bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="4f598-233">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="4f598-234">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4f598-234">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="4f598-235">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmada.</span><span class="sxs-lookup"><span data-stu-id="4f598-235">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="4f598-236">Confirme se os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="4f598-236">Confirm existing users.</span></span> <span data-ttu-id="4f598-237">Por exemplo, envio em lote-emails com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="4f598-237">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
