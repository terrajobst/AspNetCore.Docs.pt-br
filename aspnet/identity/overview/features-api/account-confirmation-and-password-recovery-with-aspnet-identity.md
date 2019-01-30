---
uid: identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
title: Confirmação de conta e recuperação de senha com a identidade do ASP.NET (c#) | Microsoft Docs
author: HaoK
description: Antes de fazer este tutorial, que você deve concluir primeiro crie um aplicativo de web do ASP.NET MVC 5 seguro com logon, redefinição de senha e de confirmação de email. Este tutorial...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 8d54180d-f826-4df7-b503-7debf5ed9fb3
msc.legacyurl: /identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 47dc2c1044a5964624ba2f8af4f174a2fd99d3e8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236400"
---
# <a name="account-confirmation-and-password-recovery-with-aspnet-identity-c"></a>Confirmação e a senha de recuperação com o ASP.NET Identity da conta (C#)

> Antes de fazer este tutorial você deve primeiro concluir [criar um aplicativo de web do ASP.NET MVC 5 seguro com logon, redefinição de senha e de confirmação de email](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md). Este tutorial contém mais detalhes e mostrará a você como configurar o email de confirmação de conta local e permitir que os usuários a redefinir sua senha esquecida no ASP.NET Identity.

Uma conta de usuário local requer que o usuário crie uma senha para a conta e senha é armazenada (com segurança) no aplicativo web. Identidade do ASP.NET também dá suporte a contas sociais, que não exigem que o usuário crie uma senha para o aplicativo. [Contas sociais](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) usam um terceiro (por exemplo, Google, Twitter, Facebook ou Microsoft) para autenticar usuários. Este tópico aborda o seguinte:

- [Criar um aplicativo ASP.NET MVC](#createMvc) e explorar os recursos de identidade do ASP.NET.
- [Criar o exemplo de identidade](#build)
- [Configurar o email de confirmação](#email)

Novos usuários se registram seu alias de email, que cria uma conta local.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image1.png)

Selecionar o botão registrar envia um email de confirmação que contém um token de validação para o seu endereço de email.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image2.png)

O usuário é enviado um email com um token de confirmação para sua conta.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image3.png)

Selecionando o link confirma a conta.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image4.png)

<a id="passwordReset"></a>

## <a name="password-recoveryreset"></a>Recuperação/redefinição de senha

Usuários locais que esquecem a senha podem ter um token de segurança enviado para a conta de email, habilitá-los a redefinir sua senha.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image5.png)  
  
O usuário receberá em breve um email com um link, permitindo que eles redefinam sua senha.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image6.png)  
Selecionando o link vai levá-los para a página de redefinição.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image7.png)  
  
Selecionando o **redefinir** botão confirmará a senha foi redefinida.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image8.png)

<a id="createMvc"></a>

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP.NET

Comece instalando e executando [Visual Studio 2017](https://visualstudio.microsoft.com/).


1. Crie um novo projeto da Web do ASP.NET e selecione o modelo MVC. Formulários da Web também dão suporte a identidade do ASP.NET, portanto, você pode seguir etapas semelhantes em um aplicativo de formulários da web.
2. Alterar a autenticação do **contas de usuário individuais**.
3. Execute o aplicativo, selecione a **registrar** vincular e registrar um usuário. Neste ponto, a validação apenas no email é com o [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) atributo.
4. No Server Explorer, navegue até **dados Connections\DefaultConnection\Tables\AspNetUsers**, clique com botão direito e selecione **abrir definição de tabela**.

    A imagem a seguir mostra o `AspNetUsers` esquema:

    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image9.png)
5. Clique com botão direito no **AspNetUsers** de tabela e selecione **Mostrar dados da tabela**.  
  
    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image10.png)  
  
   Neste ponto o email não foi confirmado.

O armazenamento de dados padrão para a identidade do ASP.NET é o Entity Framework, mas você pode configurá-lo para usar outros armazenamentos de dados e adicionar outros campos. Ver [recursos adicionais](#addRes) seção no final deste tutorial.

O [classe de inicialização OWIN](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md) ( *Startup.cs* ) é chamado quando o aplicativo é iniciado e invoca o `ConfigureAuth` método na *aplicativo\_Start\Startup.Auth.cs*, que configura o pipeline do OWIN e inicializa a identidade do ASP.NET. Examine o método `ConfigureAuth`. Cada `CreatePerOwinContext` chamada registra um retorno de chamada (salvo no `OwinContext`) que será chamado uma vez por solicitação para criar uma instância do tipo especificado. Você pode definir um ponto de interrupção no construtor e `Create` método de cada tipo (`ApplicationDbContext, ApplicationUserManager`) e verifique se eles são chamados em cada solicitação. Uma instância do `ApplicationDbContext` e `ApplicationUserManager` é armazenado no contexto OWIN, que pode ser acessado em todo o aplicativo. Ganchos de identidade do ASP.NET no pipeline OWIN por meio do middleware de cookie. Para obter mais informações, consulte [por gerenciamento de tempo de vida de solicitação para a classe UserManager no ASP.NET Identity](https://blogs.msdn.com/b/webdev/archive/2014/02/12/per-request-lifetime-management-for-usermanager-class-in-asp-net-identity.aspx).

Quando você altera seu perfil de segurança, um novo carimbo de segurança é gerado e armazenado na `SecurityStamp` campo do *AspNetUsers* tabela. Observe que o `SecurityStamp` campo é diferente do que o cookie de segurança. O cookie de segurança não é armazenado no `AspNetUsers` tabela (ou qualquer outro lugar no banco de dados de identidade). O token de cookie de segurança é autoassinado usando [DPAPI](https://msdn.microsoft.com/library/system.security.cryptography.protecteddata.aspx) e é criado com o `UserId, SecurityStamp` e informações de tempo de expiração.

O middleware de cookie verifica o cookie em cada solicitação. O `SecurityStampValidator` método na `Startup` classe atinge o BD e verifica periodicamente, o carimbo de segurança conforme especificado com o `validateInterval`. Isso ocorre apenas a cada 30 minutos (em nosso exemplo), a menos que você altere seu perfil de segurança. O intervalo de 30 minutos foi escolhido para minimizar as viagens ao banco de dados. Consulte minha [tutorial de autenticação de dois fatores](index.md) para obter mais detalhes.

Por que os comentários no código, o `UseCookieAuthentication` método dá suporte à autenticação de cookie. O `SecurityStamp` associado e campo de código fornece uma camada extra de segurança ao seu aplicativo, quando você altera sua senha, você será conectado fora do navegador com você fez logon. O `SecurityStampValidator.OnValidateIdentity` método permite que o aplicativo validar o token de segurança quando o usuário fizer logon, que é usado quando você altera uma senha ou usa o logon externo. Isso é necessário para garantir que todos os tokens (cookies) gerados com a senha antiga são invalidados. No projeto de exemplo, se você alterar a senha do usuário, em seguida, um novo token é gerada para o usuário, todos os tokens anteriores serão invalidados e o `SecurityStamp` campo é atualizado.

O sistema de identidade permitem que você configure seu aplicativo isso quando o perfil de segurança de usuários é alterado (por exemplo, quando o usuário altera sua senha ou alterações associado logon (por exemplo, Facebook, Google, conta da Microsoft, etc.), o usuário está conectado de todos os instâncias do navegador. Por exemplo, a imagem abaixo mostra os [exemplo de saída única](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) aplicativo, que permite que o usuário saia de todas as instâncias do navegador (nesse caso, IE, Firefox e Chrome), selecionando um botão. Como alternativa, o exemplo permite que você apenas fazer logoff de uma instância de navegador específico.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image11.png)

O [exemplo de saída única](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) aplicativo mostra como a identidade do ASP.NET permite que você regenerar o token de segurança. Isso é necessário para garantir que todos os tokens (cookies) gerados com a senha antiga são invalidados. Esse recurso fornece uma camada extra de segurança para seu aplicativo. Quando você altera sua senha, desconectado no qual você fez logon no aplicativo.

O *App\_Start\IdentityConfig.cs* arquivo contém o `ApplicationUserManager`, `EmailService` e `SmsService` classes. O `EmailService` e `SmsService` classes cada implementam o `IIdentityMessageService` da interface, para que você tenha métodos comuns em cada classe para configurar o email e SMS. Embora este tutorial apenas mostra como adicionar notificação por email por meio [SendGrid](http://sendgrid.com/), você pode enviar emails usando SMTP e outros mecanismos.

O `Startup` classe também contém chapa clichê para adicionar logons sociais (Facebook, Twitter, etc.), consulte meu tutorial [aplicativo do MVC 5 com o Facebook, Twitter, LinkedIn e Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) para obter mais informações.

Examinar o `ApplicationUserManager` classe, que contém as informações de identidade de usuários e configura os seguintes recursos:

- Requisitos de força de senha.
- Bloqueio de usuário (tentativas e tempo).
- Autenticação de dois fatores (2FA). Falarei sobre 2FA e SMS em outro tutorial.
- Vinculando o email e serviços do SMS. (Falarei sobre SMS em outro tutorial).

O `ApplicationUserManager` deriva de classe genérica `UserManager<ApplicationUser>` classe. `ApplicationUser` deriva [IdentityUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser.aspx). `IdentityUser` deriva o genérico `IdentityUser` classe:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample1.cs)]

As propriedades acima coincidirem com as propriedades no `AspNetUsers` tabela, como mostrada acima.

Argumentos genéricos em `IUser` permitem que você derivar uma classe usando diferentes tipos para a chave primária. Consulte a [ChangePK](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) exemplo que mostra como alterar a chave primária da cadeia de caracteres em int ou GUID.

### <a name="applicationuser"></a>ApplicationUser

`ApplicationUser` (`public class ApplicationUserManager : UserManager<ApplicationUser>`) é definida no *Models\IdentityModels.cs* como:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample2.cs?highlight=8-9)]

O código realçado acima gera um [ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). Identidade do ASP.NET e OWIN Cookie de autenticação baseada em declarações, portanto o framework requer que o aplicativo para gerar um `ClaimsIdentity` para o usuário. `ClaimsIdentity` contém informações sobre todas as declarações para o usuário, como o nome do usuário, a idade e as funções que o usuário pertence. Você também pode adicionar mais declarações para o usuário neste estágio.

O OWIN `AuthenticationManager.SignIn` método passa o `ClaimsIdentity` e o usuário fizer logon:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample3.cs?highlight=4-6)]

[Aplicativo do MVC 5 com o Facebook, Twitter, LinkedIn e Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) mostra como você pode adicionar propriedades adicionais para o `ApplicationUser` classe.

## <a name="email-confirmation"></a>Email de confirmação

É uma boa ideia para confirmar o email de um novo usuário se registrar para verificar se eles não são representando a outra pessoa (ou seja, eles ainda não registrados com o email de outra pessoa). Suponha que você tivesse um fórum de discussão, convém evitar `"bob@example.com"` de registrar como `"joe@contoso.com"`. Sem email de confirmação, `"joe@contoso.com"` poderia obter email indesejado do seu aplicativo. Suponha que Bob acidentalmente registrado como `"bib@example.com"` e ainda não tenha notado, ele não seria capaz de usar a senha de recuperação porque o aplicativo não tiver seu email correto. Email de confirmação fornece apenas proteção limitada de bots e não fornece proteção de determinado remetentes de spam, eles têm muitos aliases de email de trabalho, que eles podem usar para se registrar. No exemplo a seguir, o usuário não poderá alterar sua senha até que sua conta foi confirmada (por eles um link de confirmação recebido na conta de email, com que eles registraram a seleção.) Você pode aplicar esse fluxo de trabalho a outros cenários, por exemplo, enviando um link para confirmar e redefinir a senha em novas contas criadas pelo administrador, enviar um email de usuário quando eles forem alterados seu perfil e assim por diante. Você geralmente deseja impedir que usuários novos lançamentos de todos os dados para seu site da web antes de confirmar por email, uma mensagem de texto SMS ou outro mecanismo. <a id="build"></a>

## <a name="build-a-more-complete-sample"></a>Criar um exemplo mais completo

Nesta seção, você usará o NuGet para baixar um exemplo mais completo, com que trabalharemos.

1. Criar um novo ***vazio*** projeto Web do ASP.NET.
2. No Console do Gerenciador de pacotes, digite os seguintes comandos: 

    [!code-console[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample4.cmd)]

   Neste tutorial, usaremos [SendGrid](http://sendgrid.com/) para enviar email. O `Identity.Samples` pacote instala o código que podemos trabalhará.
3. Defina as [projeto para usar SSL](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
4. Criação de conta local de teste, executando o aplicativo, selecionando a **registrar** vincular e, em seguida, postar o formulário de registro.
5. Selecione o link de email de demonstração, que simula o email de confirmação.
6. Remover o código de confirmação de link de email de demonstração de exemplo (o `ViewBag.Link` código no controlador da conta. Consulte a `DisplayEmail` e `ForgotPasswordConfirmation` métodos de ação e as exibições do razor).

> [!WARNING]
> Se você alterar as configurações de segurança neste exemplo, aplicativos de produções precisará passar por uma auditoria de segurança que chama explicitamente as alterações feitas.


## <a name="examine-the-code-in-appstartidentityconfigcs"></a>Examine o código no aplicativo\_Start\IdentityConfig.cs

O exemplo mostra como criar uma conta e adicioná-lo para o *Admin* função. Você deve substituir o email no exemplo com o email que você usará para a conta de administrador. A maneira mais fácil no momento para criar uma conta de administrador está em modo programático o `Seed` método. Esperamos ter no futuro uma ferramenta que permitirá que você criar e administrar usuários e funções. O exemplo de código permitem que você criar e gerenciar usuários e funções, mas você deve primeiramente ter uma conta de administradores para executar as funções e as páginas de administração de usuário. Neste exemplo, a conta de administrador é criada quando o BD será propagado.

Alterar a senha e altere o nome para uma conta em que você pode receber notificações por email.

> [!WARNING]
> Security - nunca armazenar os dados confidenciais em seu código-fonte.

Conforme mencionado anteriormente, o `app.CreatePerOwinContext` chamada na classe startup adiciona os retornos de chamada para o `Create` método os aplicativo banco de dados de conteúdo, manager e a função Gerenciador de classes de usuário. Chamadas de pipeline do OWIN a `Create` método sobre essas classes para cada solicitação e armazena o contexto para cada classe. O controlador de conta expõe o gerente do usuário do contexto HTTP (que contém o contexto do OWIN):

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample5.cs)]

Quando um usuário registra uma conta local, o `HTTP Post Register` método é chamado:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample6.cs)]

O código acima usa os dados de modelo para criar uma nova conta de usuário usando o email e a senha digitada. Se o alias de email está no repositório de dados, falha na criação da conta e o formulário é exibido novamente. O `GenerateEmailConfirmationTokenAsync` método cria um token de confirmação seguro e o armazena no armazenamento de dados de identidade do ASP.NET. O [Action](https://msdn.microsoft.com/library/dd505232(v=vs.118).aspx) método cria um link que contém o `UserId` e token de confirmação. Esse link, em seguida, é enviado por email para o usuário, o usuário pode selecionar no link no seu aplicativo de email para confirmar sua conta.

<a id="email"></a>

## <a name="set-up-email-confirmation"></a>Configurar o email de confirmação

Vá para o [página de inscrição do Azure SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) e registrar a conta gratuita. Adicione código semelhante ao seguinte para configurar o SendGrid:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample7.cs?highlight=5)]

> [!NOTE]
> Clientes de email com frequência aceitam apenas mensagens de texto (nenhum HTML). Você deve fornecer a mensagem de texto e HTML. No exemplo de SendGrid acima, isso é feito com o `myMessage.Text` e `myMessage.Html` código mostrado acima.


O código a seguir mostra como enviar email usando o [MailMessage](https://msdn.microsoft.com/library/system.net.mail.mailmessage.aspx) classe onde `message.Body` retorna somente o link.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample8.cs)]

> [!WARNING]
> Security - nunca armazenar os dados confidenciais em seu código-fonte. A conta e as credenciais são armazenadas na appSetting. No Azure, você pode armazenar com segurança esses valores sobre o **[configurar](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** guia no portal do Azure. Ver [práticas recomendadas para implantar senhas e outros dados confidenciais no ASP.NET e o Azure](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).


Insira suas credenciais do SendGrid, executar o aplicativo, registre-se com um alias de email pode selecionar o link de confirmação em seu email. Para ver como fazer isso com seus [Outlook.com](http://outlook.com) conta de email, consulte de John Atten [ C# configuração de SMTP para o Host de SMTP do Outlook.Com](http://typecastexception.com/post/2013/12/20/C-SMTP-Configuration-for-OutlookCom-SMTP-Host.aspx) e o seu[ASP.NET 2.0 de identidade: Configuração de backup de validação da conta e a autorização de dois fatores](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) postagens.

Depois que um usuário seleciona o **registrar** botão um email de confirmação que contém um token de validação é enviado para seu endereço de email.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image12.png)

O usuário é enviado um email com um token de confirmação para sua conta.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image13.png)

## <a name="examine-the-code"></a>Examinar o código

O código a seguir mostra o método `POST ForgotPassword`.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample9.cs)]

O método falhará silenciosamente se o email do usuário não foi confirmado. Se um erro foi lançado para um endereço de email inválido, usuários mal-intencionados poderiam usar essas informações para localizar a userId válido (alias de email) a ataques.

O seguinte código mostra o `ConfirmEmail` método no controlador da conta que é chamado quando o usuário seleciona o link de confirmação no email enviado para eles:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample10.cs)]

Depois que um token de senha esquecida tiver sido usado, ele será invalidado. A seguinte alteração de código na `Create` método (em de *App\_Start\IdentityConfig.cs* arquivo) define os tokens para expirar em 3 horas.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample11.cs?highlight=6-8)]

Com o código acima, a senha esquecida e os tokens de confirmação de email irá expirar em 3 horas. O padrão `TokenLifespan` é um dia.

O código a seguir mostra o método de confirmação de email:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample12.cs)]

 Para tornar seu aplicativo mais seguro, a identidade do ASP.NET oferece suporte a autenticação de dois fatores (2FA). Consulte [identidade do ASP.NET 2.0: Configurando a validação da conta e a autorização de dois fatores](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) por John Atten. Embora você possa definir o bloqueio de conta em falhas de tentativa de senha de logon, essa abordagem torna seu logon suscetíveis a [DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack) bloqueios. É recomendável que você use o bloqueio de conta apenas com 2FA.  
<a id="addRes"></a>

## <a name="additional-resources"></a>Recursos adicionais

- [Visão geral de provedores de armazenamento personalizados para a Identidade do ASP.NET](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md)
- [Aplicativo do MVC 5 com o Facebook, Twitter, LinkedIn e Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) também mostra como adicionar informações de perfil para a tabela de usuários.
- [ASP.NET MVC e identidade 2.0: Noções básicas sobre os conceitos básicos](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx) por John Atten.
- [Introdução à Identidade do ASP.NET](../getting-started/introduction-to-aspnet-identity.md)
- [Anunciando o RTM do ASP.NET Identity 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx) de Pranav Rastogi.
