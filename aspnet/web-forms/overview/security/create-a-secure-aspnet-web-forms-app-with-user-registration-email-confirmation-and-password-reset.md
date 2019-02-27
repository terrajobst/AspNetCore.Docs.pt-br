---
uid: web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
title: Criar um aplicativo Web Forms do ASP.NET seguro com o registro de usuário, email de confirmação e redefinição de senha (c#) | Microsoft Docs
author: Erikre
description: Este tutorial mostra como criar um aplicativo de Web Forms do ASP.NET com o registro do usuário, confirmação por email e redefinição de senha usando o membro de identidade do ASP.NET...
ms.author: riande
ms.date: 10/02/2014
ms.assetid: 0a8d6044-5fab-4213-82d6-5618d5601358
msc.legacyurl: /web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
msc.type: authoredcontent
ms.openlocfilehash: acc13776840408756901e20589b9efacc83ff2a9
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833677"
---
<a name="create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset-c"></a>Criar um aplicativo de Web Forms do ASP.NET seguro com registro de usuário, confirmação por email e redefinição de senha (C#)
====================
by [Erik Reitan](https://github.com/Erikre)

> Este tutorial mostra como criar um aplicativo de Web Forms do ASP.NET com o registro do usuário, confirmação por email e redefinição de senha usando o sistema de associação do ASP.NET Identity. Este tutorial se baseia de Rick Anderson [tutorial MVC](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md).


## <a name="introduction"></a>Introdução

Este tutorial orienta você pelas etapas necessárias para criar um aplicativo Web Forms do ASP.NET usando o Visual Studio e o ASP.NET 4.5 para criar um aplicativo de formulários da Web seguro com o registro de usuário, redefinição de senha e de confirmação de email.

### <a name="tutorial-tasks-and-information"></a>Tutoriais tarefas e informações:

- [Criar um ASP.NET Web Forms aplicativo](#createWebForms)
- [Enganchar SendGrid](#SG)
- [Exigir Email de confirmação antes de entrar](#require)
- [Redefinição e recuperação de senha](#reset)
- [Reenviar Link de confirmação de Email](#rsend)
- [O aplicativo de solução de problemas](#dbg)
- [Recursos adicionais](#addRes)

<a id="createWebForms"></a>
## <a name="create-an-aspnet-web-forms-app"></a>Criar um aplicativo de formulários da Web do ASP.NET

Comece instalando e executando [Visual Studio Express 2013 para Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Instale [Visual Studio 2013 atualização 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou superior também.

> [!NOTE]
> Aviso: Você deve instalar [Visual Studio 2013 atualização 3](https://go.microsoft.com/fwlink/?LinkId=390465) ou superior para concluir este tutorial.


1. Criar um novo projeto (**arquivo**  - &gt; **novo projeto**) e selecione o **aplicativo Web ASP.NET** modelo e o .NET Framework mais recente versão dos **novo projeto** caixa de diálogo.
2. Dos **novo projeto ASP.NET** caixa de diálogo, selecione o **Web Forms** modelo. Deixe a autenticação padrão como **contas de usuário individuais**. Se você quiser hospedar o aplicativo no Azure, deixe o **hospedar na nuvem** caixa de seleção marcada.   
 Em seguida, clique em **Okey** para criar o novo projeto.  
    ![Caixa de diálogo Novo projeto ASP.NET](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)
3. Habilite o Secure Sockets Layer (SSL) para o projeto. Siga as etapas disponíveis na **habilitar o SSL para o projeto** seção o [Introdução a série de tutoriais de Web Forms](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms).
4. Execute o aplicativo, clique no **registrar** vincular e registrar um novo usuário. Neste ponto, a validação apenas no email se baseia a [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) atributo para garantir que o endereço de email está bem formado. Você modificará o código para adicionar o email de confirmação. Feche a janela do navegador.
5. Na **Gerenciador de servidores** do Visual Studio (**exibição**  - &gt; **Gerenciador de servidores**), navegue até **Connections\ de dados DefaultConnection\Tables\AspNetUsers**, clique com o botão direito e selecione **abrir definição de tabela**. 

    A imagem a seguir mostra o `AspNetUsers` esquema de tabela:

    ![Esquema da tabela AspNetUsers](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image2.png)
6. Na **Gerenciador de servidores**, clique com botão direito no **AspNetUsers** de tabela e selecione **Mostrar dados da tabela**.  
  
    ![Dados da tabela AspNetUsers](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image3.png)  
 Neste ponto o email para o usuário registrado não foi confirmado.
7. Clique na linha e selecione Excluir para excluir o usuário. Você adicionará este email novamente na próxima etapa e enviar uma mensagem de confirmação para o endereço de email.

## <a name="email-confirmation"></a>Email de confirmação

Ele é uma prática recomendada para confirmar o email durante o registro de um novo usuário para verificar se eles não são representando a outra pessoa (ou seja, eles ainda não registrados com o email de outra pessoa). Suponha que você tivesse um fórum de discussão, convém evitar `"bob@cpandl.com"` de registrar como `"joe@contoso.com"`. Sem email de confirmação, `"joe@contoso.com"` poderia obter email indesejado do seu aplicativo. Suponha que Bob acidentalmente registrado como `"bib@cpandl.com"` e ainda não tenha notado, ele não seria capaz de usar a recuperação de senha porque o aplicativo não tiver seu email correto. Email de confirmação fornece apenas proteção limitada de bots e não fornece proteção de remetentes de spam determinados.

Você geralmente deseja impedir que usuários novos lançamentos todos os dados ao seu site antes de confirmar por qualquer email, uma mensagem de texto SMS ou outro mecanismo. Nas seções a seguir, podemos habilitará o email de confirmação e modificar o código para impedir que usuários recém-registrados entrar até que o email foi confirmado. Você usará o serviço de email SendGrid neste tutorial.

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a>Enganchar SendGrid

SendGrid mudou sua API, pois este tutorial foi escrito. Para instruções de SendGrid atuais, consulte [SendGrid](http://sendgrid.com/) ou [habilitar a recuperação de confirmação e a senha da conta](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery).

Embora este tutorial apenas mostra como adicionar notificação por email por meio [SendGrid](http://sendgrid.com/), você pode enviar emails usando SMTP e outros mecanismos (consulte [recursos adicionais](#addRes)).

1. No Visual Studio, abra o **Package Manager Console** (**ferramentas**  - &gt; **Gerenciador de pacotes NuGet**  - &gt; **Package Manager Console**) e digite o seguinte comando:  
    `Install-Package SendGrid`
2. Vá para o [página de inscrição do Azure SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) e registre-se gratuitamente conta do SendGrid. Você pode também se inscrever para uma conta gratuita do SendGrid diretamente na [site do SendGrid](http://www.sendgrid.com).
3. Do **Gerenciador de soluções** abrir o *IdentityConfig.cs* arquivo o *aplicativo\_iniciar* pasta e adicione o seguinte código realçado em amarelo para o `EmailService` classe para configurar **SendGrid**:

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample1.cs?highlight=3,5,8-37)]
4. Além disso, adicione o seguinte `using` instruções ao início dos *IdentityConfig.cs* arquivo: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample2.cs?highlight=1-4)]
5. Para simplificar este exemplo, você armazenará os valores de conta de serviço de email na `appSettings` seção o *Web. config* arquivo. Adicione o seguinte XML realçado em amarelo para o *Web. config* arquivo na raiz do seu projeto:

    [!code-xml[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample3.xml?highlight=2-5)]

    > [!WARNING]
    > Security - nunca armazenar os dados confidenciais em seu código-fonte. Neste exemplo, a conta e as credenciais são armazenadas do **appSetting** seção o *Web. config* arquivo. No Azure, você pode armazenar com segurança esses valores sobre o **[configurar](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** guia no portal do Azure. Para obter informações relacionadas, consulte tópico de Rick Anderson [práticas recomendadas para implantar senhas e outros dados confidenciais no ASP.NET e o Azure](https://go.microsoft.com/fwlink/?LinkId=513141).
6. Adicione os valores do serviço de email para refletir os valores de autenticação do SendGrid (nome de usuário e senha) para que você possa bem-sucedida enviar email de seu aplicativo. Certifique-se de usar seu nome de conta do SendGrid em vez do endereço de email que você forneceu o SendGrid.

### <a name="enable-email-confirmation"></a>Habilitar o Email de confirmação

 Para habilitar o email de confirmação, você modificará o código de registro usando as etapas a seguir.  
 

1. No *conta* pasta, abra o *Register.aspx.cs* code-behind e atualizar o `CreateUser_Click` método para habilitar as seguintes alterações realçadas: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample4.cs?highlight=9-11)]
2. Na **Gerenciador de soluções**, clique com botão direito *default. aspx* e selecione **definir como página inicial**.
3. Execute o aplicativo pressionando **F5.** Depois que a página é exibida, clique no **registrar** link para exibir a página de registro.
4. Insira seu email e senha e clique o **registrar** botão para enviar uma mensagem de email pelo SendGrid.  
   O estado atual do seu projeto e código permitirá que o usuário faça logon após concluir o formulário de registro, mesmo que eles ainda não confirmou a sua conta.
5. Verifique sua conta de email e clique no link para confirmar seu email.  
   Depois de enviar o formulário de registro, você será conectado no.  
    ![Site de exemplo - conectado](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image4.png)

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a>Exigir Email de confirmação antes de entrar

Embora você confirmou a conta de email, neste momento você não precisaria clique no link contido no email de verificação para estar totalmente conectadas. Na seção a seguir, você modificará o código que exigem novos usuários ter um email confirmado antes que eles estejam conectados (autenticado).

1. Na **Gerenciador de soluções** do Visual Studio, atualize o `CreateUser_Click` evento no *Register.aspx.cs* de lógica contida no *contas* pasta com o seguintes alterações realçadas: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample5.cs?highlight=13-14,17-21)]
2. Atualizar o `LogIn` método na *Login.aspx.cs* de lógica com as seguintes alterações realçadas: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample6.cs?highlight=9-19,45-46)]

### <a name="run-the-application"></a>Executar o aplicativo

 Agora que você implementou o código para verificar se o endereço de email do usuário foi confirmado, você pode verificar a funcionalidade em ambos os **registre** e **Login** páginas. 

1. Excluir qualquer conta na **AspNetUsers** tabela que contém o alias de email que você deseja testar.
2. Execute o aplicativo (**F5**) e verifique se você não pode registrar como um usuário até que você confirmar seu endereço de email.
3. Antes de confirmar sua nova conta de email que acabou de ser enviado por meio do, tente fazer logon com a nova conta.  
 Você verá que você não conseguir fazer logon e que você deve ter uma conta de email confirmado.
4. Depois de confirmar seu endereço de email, faça logon no aplicativo.

<a id="reset"></a>
## <a name="password-recovery-and-reset"></a>Redefinição e recuperação de senha

1. No Visual Studio, remova os caracteres de comentário do `Forgot` método na *Forgot.aspx.cs* lógica contida no *conta* pasta, para que o método é exibido como segue: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample7.cs?highlight=16-18)]
2. Abra o *login. aspx* página. Substitua a marcação perto do fim do **loginForm** seção conforme realçado abaixo: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample8.aspx?highlight=52-53)]
3. Abra o *Login.aspx.cs* code-behind e remova a seguinte linha de código realçada em amarelo do `Page_Load` manipulador de eventos: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample9.cs?highlight=5)]
4. Execute o aplicativo pressionando **F5.** Depois que a página é exibida, clique no **faça logon no** link.
5. Clique o **esqueceu sua senha?** link para exibir o **esqueceu a senha** página.
6. Insira seu endereço de email e clique no **enviar** botão para enviar um email para o endereço que permitirá que você redefina sua senha.   
   Verifique sua conta de email e clique no link para exibir o **Redefinir senha** página.
7. Sobre o **Redefinir senha** página, insira seu email, a senha e a senha confirmada. Em seguida, pressione a **redefinir** botão.  
   Quando você redefinir com êxito sua senha, o **senha alterada** página será exibida. Agora você pode fazer logon com sua nova senha.

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a>Reenviar Link de confirmação de Email

Depois que um usuário cria uma nova conta local, eles são enviados por email um link de confirmação que são obrigados a usar antes que eles podem fazer logon. Se o usuário acidentalmente exclui o email de confirmação, ou o email chega nunca, será necessário o link de confirmação enviado novamente. As alterações de código a seguir mostram como habilitar isso.

1. No Visual Studio, abra o **Login.aspx.cs** code-behind e adicione o seguinte manipulador de eventos após a `LogIn` manipulador de eventos:   

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample10.cs)]
2. Modificar a `LogIn` manipulador de eventos em de *Login.aspx.cs* de lógica, alterando o código realçado em amarelo, da seguinte maneira: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample11.cs?highlight=15-17)]
3. Atualizar o *login. aspx* página adicionando o código realçado em amarelo, da seguinte maneira: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample12.aspx?highlight=45-46)]
4. Excluir qualquer conta na **AspNetUsers** tabela que contém o alias de email que você deseja testar.
5. Execute o aplicativo (**F5**) e registrar seu endereço de email.
6. Antes de confirmar sua nova conta de email que acabou de ser enviado por meio do, tente fazer logon com a nova conta.  
   Você verá que você não conseguir fazer logon e que você deve ter uma conta de email confirmado. Além disso, você agora pode reenviar uma mensagem de confirmação para sua conta de email.
7. Insira seu endereço de email e senha, em seguida, pressione a **reenviar confirmação** botão.
8. Depois de confirmar seu endereço de email com base em mensagem de email enviadas recentemente, faça logon no aplicativo.

<a id="dbg"></a>
## <a name="troubleshooting-the-app"></a>O aplicativo de solução de problemas

Se você não receber um email contendo o link para verificar suas credenciais:

- Verifique a pasta Lixo eletrônico ou spam.
- Faça logon em sua conta do SendGrid e clique no [link da atividade de Email](https://sendgrid.com/logs/index).
- Ter certeza de que você usou seu nome de conta de usuário do SendGrid como uma *Web. config* valor em vez de seu endereço de email de conta do SendGrid.

<a id="addRes"></a>
## <a name="additional-resources"></a>Recursos adicionais

- [Recursos recomendados de links para a identidade do ASP.NET](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [Confirmação de conta e recuperação de senha com o ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [Série de tutoriais do ASP.NET Web Forms - adicionar um provedor de OAuth 2.0](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#OAuthWebForms)
- [Implantar um aplicativo de formulários da Web do ASP.NET seguro com associação, OAuth e banco de dados SQL no serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)
- [Série de tutoriais de Web Forms do ASP.NET - habilitar o SSL para o projeto](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)
