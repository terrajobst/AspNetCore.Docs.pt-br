---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 802ba446af04df6a35ac73187ad693b8ec80c654
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814843"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Confirmação de conta e de recuperação de senha no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), e [Joe Audette](https://twitter.com/joeaudette)

Este tutorial mostra como criar um aplicativo ASP.NET Core com a redefinição de senha e de confirmação de email. Este tutorial é **não** um tópico de início. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Autenticação](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

Ver [esse arquivo PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) para a versão do ASP.NET Core 1.1.

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>Pré-requisitos

[SDK do .NET core 3.0 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Criar e testar um aplicativo web com autenticação

Execute os seguintes comandos para criar um aplicativo web com autenticação.

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Execute o aplicativo, selecione a **registrar** vincular e registrar um usuário. Depois de registrado, você será redirecionado para a para `/Identity/Account/RegisterConfirmation` página que contém um link para simular o email de confirmação:

* Selecione o `Click here to confirm your account` link.
* Selecione o **Login** vincular e entrar com as mesmas credenciais.
* Selecione o `Hello YourEmail@provider.com!` link, que redirecionará você para o `/Identity/Account/Manage/PersonalData` página.
* Selecione o **dados pessoais** guia à esquerda e, em seguida, selecione **excluir**.

### <a name="configure-an-email-provider"></a>Configurar um provedor de email

Neste tutorial [SendGrid](https://sendgrid.com) é usado para enviar email. Você precisa de uma conta do SendGrid e uma chave para enviar email. Você pode usar outros provedores de email. É recomendável que usar o SendGrid ou outro serviço de email para enviar email. SMTP é difícil proteger e configurado corretamente.

Crie uma classe para buscar a chave de email seguro. Para este exemplo, crie *Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configurar segredos de usuário do SendGrid

Defina as `SendGridUser` e `SendGridKey` com o [ferramenta secret manager](xref:security/app-secrets). Por exemplo:

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

No Windows, o Secret Manager armazena os pares de chaves/valor em uma *Secrets* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.

O conteúdo a *Secrets* arquivo não são criptografadas. A marcação a seguir mostra a *Secrets* arquivo. O `SendGridKey` valor tiver sido removido.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalar o SendGrid

Este tutorial mostra como adicionar notificações de email por meio [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando SMTP e outros mecanismos.

Instalar o `SendGrid` pacote do NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No Console do Gerenciador de pacotes, digite o seguinte comando:

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

No console, digite o seguinte comando:

```cli
dotnet add package SendGrid
```

---

Ver [inicie gratuitamente com o SendGrid](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.

### <a name="implement-iemailsender"></a>Implementar IEmailSender

Para implementar `IEmailSender`, crie *Services/EmailSender.cs* com um código semelhante ao seguinte:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar a inicialização para dar suporte a email

Adicione o seguinte código para o `ConfigureServices` método na *Startup.cs* arquivo:

* Adicionar `EmailSender` como um serviço transitório.
* Registrar o `AuthMessageSenderOptions` instância de configuração.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>Registrar, confirme se o email e redefinição de senha

Executar o aplicativo web e testar o fluxo de recuperação de senha e confirmação de conta.

* Execute o aplicativo e registrar um novo usuário
* Verifique seu email para o link de confirmação de conta. Ver [depurar email](#debug) se você não receber o email.
* Clique no link para confirmar seu email.
* Entrar com seu email e senha.
* Saia do serviço.

### <a name="test-password-reset"></a>Teste a redefinição de senha

* Se você estiver conectado, selecione **Logout**.
* Selecione o **login** link e selecione o **esqueceu sua senha?** link.
* Insira o email usado para registrar a conta.
* Um email com um link para redefinir sua senha é enviado. Verifique seu email e clique no link para redefinir sua senha. Depois que sua senha foi redefinida com êxito, você pode entrar com seu email e a nova senha.

## <a name="change-email-and-activity-timeout"></a>Tempo limite de email e a atividade de alteração

O tempo de limite de inatividade padrão é 14 dias. O código a seguir define o tempo limite de inatividade para cinco dias:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alterar todos os empreguem de token de proteção de dados

O código a seguir altera todos os dados proteção tokens tempo limite para 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Internos em tokens de identidade do usuário (consulte [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tem um [tempo limite de um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Alterar o tempo de vida de token de email

O tempo de vida de token no padrão de [os tokens de identidade do usuário](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é [um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Esta seção mostra como alterar o tempo de vida de token de email.

Adicionar um personalizado [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Adicione o provedor personalizado para o contêiner de serviço:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>Reenviar email de confirmação

Ver [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar o email

Se você não é possível obter o trabalho de email:

* Defina um ponto de interrupção no `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.
* Criar uma [aplicativo de console para enviar email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando um código semelhante ao `EmailSender.Execute`.
* Examine os [atividade de Email](https://sendgrid.com/docs/User_Guide/email_activity.html) página.
* Verifique sua pasta de spam.
* Tente outro alias de email em outro provedor de email (Microsoft, Yahoo, Gmail, etc.)
* Tente enviar para contas de email diferente.

**Uma prática recomendada de segurança** é **não** use segredos de produção em desenvolvimento e teste. Se você publicar o aplicativo no Azure, defina os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.

## <a name="combine-social-and-local-login-accounts"></a>Combine as contas de logon social e local

Para concluir esta seção, você deve primeiro habilitar um provedor de autenticação externa. Ver [Facebook, Google e a autenticação de provedor externo](xref:security/authentication/social/index).

Você pode combinar as contas locais e sociais clicando no link seu email. Na sequência a seguir, "RickAndMSFT@gmail.com" é criado como um logon local; no entanto, você pode criar a conta como um logon social primeiro e adicionar um logon local.

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

Clique no **gerenciar** link. Observe externo 0 (logons sociais) associado a essa conta.

![Gerenciar o modo de exibição](accconfirm/_static/manage.png)

Clique no link para outro serviço de logon e aceitar as solicitações do aplicativo. Na imagem a seguir, o Facebook é o provedor de autenticação externa:

![Gerenciar seu modo de exibição de logons externos lista Facebook](accconfirm/_static/fb.png)

As duas contas foram combinadas. É possível entrar com qualquer uma das contas. Convém que os usuários adicionem contas locais no caso de seu serviço de autenticação de logon social está inoperante ou, mais provavelmente eles tiver perdido o acesso à sua conta social.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar confirmação de conta depois que um site tem usuários

Habilitando a confirmação de conta em um site com usuários bloqueia todos os usuários existentes. Os usuários existentes estão bloqueados porque suas contas não são confirmadas. Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:

* Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmada.
* Confirme se os usuários existentes. Por exemplo, envio em lote-emails com links de confirmação.

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>Pré-requisitos

[SDK do .NET core 2.2 ou posterior](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>Criar um aplicativo web e o scaffolding de identidade

Execute os seguintes comandos para criar um aplicativo web com autenticação.

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a>Novo registro de usuário de teste

Execute o aplicativo, selecione a **registrar** vincular e registrar um usuário. Neste ponto, a validação apenas no email é com o [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo. Depois de enviar o registro, você está conectado ao aplicativo. Posteriormente no tutorial, o código é atualizado para que novos usuários poderão entrar depois que o email é validado.

[!INCLUDE[](~/includes/view-identity-db.md)]

Observe a tabela `EmailConfirmed` campo é `False`.

Você talvez queira usar este email novamente na próxima etapa quando o aplicativo envia um email de confirmação. Clique com botão direito na linha e selecione **excluir**. Excluir o alias de email torna mais fácil nas etapas a seguir.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Exigir email de confirmação

É uma prática recomendada para confirmar o email de um novo registro de usuário. Ajuda a confirmação para verificar se eles não estiver representando alguém de email (ou seja, eles ainda não registrados com o email de outra pessoa). Suponha que você tinha um fórum de discussão e quisesse impedir "yli@example.com"de registrar-se como"nolivetto@contoso.com". Sem email de confirmação "nolivetto@contoso.com" pode receber emails indesejados de seu aplicativo. Suponha que o usuário registrado acidentalmente como "ylo@example.com" e ainda não tenha notado o erro de ortografia de "yli". Eles não seria capazes de usar a recuperação de senha porque o aplicativo não tiver seu email correto. Email de confirmação oferece proteção limitada contra bots. Email de confirmação não fornece proteção contra usuários mal-intencionados com muitas contas de email.

Geralmente você deseja impedir que novos usuários incluam dados em seu site até que eles tenham um email confirmado.

Atualização `Startup.ConfigureServices` para exigir um email confirmado:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` impede que os usuários registrados entrar até que o email seja confirmado.

### <a name="configure-email-provider"></a>Configurar o provedor de email

Neste tutorial [SendGrid](https://sendgrid.com) é usado para enviar email. Você precisa de uma conta do SendGrid e uma chave para enviar email. Você pode usar outros provedores de email. ASP.NET Core 2.x inclui `System.Net.Mail`, que permite que você enviar um email de seu aplicativo. É recomendável que usar o SendGrid ou outro serviço de email para enviar email. SMTP é difícil proteger e configurado corretamente.

Crie uma classe para buscar a chave de email seguro. Para este exemplo, crie *Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Configurar segredos de usuário do SendGrid

Defina as `SendGridUser` e `SendGridKey` com o [ferramenta secret manager](xref:security/app-secrets). Por exemplo:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

No Windows, o Secret Manager armazena os pares de chaves/valor em uma *Secrets* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.

O conteúdo a *Secrets* arquivo não são criptografadas. A marcação a seguir mostra a *Secrets* arquivo. O `SendGridKey` valor tiver sido removido.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalar o SendGrid

Este tutorial mostra como adicionar notificações de email por meio [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando SMTP e outros mecanismos.

Instalar o `SendGrid` pacote do NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No Console do Gerenciador de pacotes, digite o seguinte comando:

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

No console, digite o seguinte comando:

```cli
dotnet add package SendGrid
```

---

Ver [inicie gratuitamente com o SendGrid](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.

### <a name="implement-iemailsender"></a>Implementar IEmailSender

Para implementar `IEmailSender`, crie *Services/EmailSender.cs* com um código semelhante ao seguinte:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Configurar a inicialização para dar suporte a email

Adicione o seguinte código para o `ConfigureServices` método na *Startup.cs* arquivo:

* Adicionar `EmailSender` como um serviço transitório.
* Registrar o `AuthMessageSenderOptions` instância de configuração.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Habilitar a recuperação de confirmação e a senha da conta

O modelo tem o código para recuperação de confirmação e a senha da conta. Localizar o `OnPostAsync` método no *Areas/Identity/Pages/Account/Register.cshtml.cs*.

Impedir que usuários recém-registrados serem conectados automaticamente comentando a linha a seguir:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

O método complete é mostrado com a linha alterada realçada:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrar, confirme se o email e redefinição de senha

Executar o aplicativo web e testar o fluxo de recuperação de senha e confirmação de conta.

* Execute o aplicativo e registrar um novo usuário
* Verifique seu email para o link de confirmação de conta. Ver [depurar email](#debug) se você não receber o email.
* Clique no link para confirmar seu email.
* Entrar com seu email e senha.
* Saia do serviço.

### <a name="view-the-manage-page"></a>Exibir a página Gerenciar

Selecione seu nome de usuário no navegador: ![janela do navegador com o nome de usuário](accconfirm/_static/un.png)

A página Gerenciar é exibida com o **perfil** guia selecionada. O **Email** mostra uma caixa de seleção que indica o email foi confirmada.

### <a name="test-password-reset"></a>Teste a redefinição de senha

* Se você estiver conectado, selecione **Logout**.
* Selecione o **login** link e selecione o **esqueceu sua senha?** link.
* Insira o email usado para registrar a conta.
* Um email com um link para redefinir sua senha é enviado. Verifique seu email e clique no link para redefinir sua senha. Depois que sua senha foi redefinida com êxito, você pode entrar com seu email e a nova senha.

## <a name="change-email-and-activity-timeout"></a>Tempo limite de email e a atividade de alteração

O tempo de limite de inatividade padrão é 14 dias. O código a seguir define o tempo limite de inatividade para cinco dias:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alterar todos os empreguem de token de proteção de dados

O código a seguir altera todos os dados proteção tokens tempo limite para 3 horas:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Internos em tokens de identidade do usuário (consulte [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) tem um [tempo limite de um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Alterar o tempo de vida de token de email

O tempo de vida de token no padrão de [os tokens de identidade do usuário](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é [um dia](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). Esta seção mostra como alterar o tempo de vida de token de email.

Adicionar um personalizado [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Adicione o provedor personalizado para o contêiner de serviço:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Reenviar email de confirmação

Ver [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Depurar o email

Se você não é possível obter o trabalho de email:

* Defina um ponto de interrupção no `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.
* Criar uma [aplicativo de console para enviar email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando um código semelhante ao `EmailSender.Execute`.
* Examine os [atividade de Email](https://sendgrid.com/docs/User_Guide/email_activity.html) página.
* Verifique sua pasta de spam.
* Tente outro alias de email em outro provedor de email (Microsoft, Yahoo, Gmail, etc.)
* Tente enviar para contas de email diferente.

**Uma prática recomendada de segurança** é **não** use segredos de produção em desenvolvimento e teste. Se você publicar o aplicativo no Azure, você pode definir os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.

## <a name="combine-social-and-local-login-accounts"></a>Combine as contas de logon social e local

Para concluir esta seção, você deve primeiro habilitar um provedor de autenticação externa. Ver [Facebook, Google e a autenticação de provedor externo](xref:security/authentication/social/index).

Você pode combinar as contas locais e sociais clicando no link seu email. Na sequência a seguir, "RickAndMSFT@gmail.com" é criado como um logon local; no entanto, você pode criar a conta como um logon social primeiro e adicionar um logon local.

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

Clique no **gerenciar** link. Observe externo 0 (logons sociais) associado a essa conta.

![Gerenciar o modo de exibição](accconfirm/_static/manage.png)

Clique no link para outro serviço de logon e aceitar as solicitações do aplicativo. Na imagem a seguir, o Facebook é o provedor de autenticação externa:

![Gerenciar seu modo de exibição de logons externos lista Facebook](accconfirm/_static/fb.png)

As duas contas foram combinadas. É possível entrar com qualquer uma das contas. Convém que os usuários adicionem contas locais no caso de seu serviço de autenticação de logon social está inoperante ou, mais provavelmente eles tiver perdido o acesso à sua conta social.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Habilitar confirmação de conta depois que um site tem usuários

Habilitando a confirmação de conta em um site com usuários bloqueia todos os usuários existentes. Os usuários existentes estão bloqueados porque suas contas não são confirmadas. Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:

* Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmada.
* Confirme se os usuários existentes. Por exemplo, envio em lote-emails com links de confirmação.

::: moniker-end
