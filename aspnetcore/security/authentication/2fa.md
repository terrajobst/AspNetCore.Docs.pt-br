---
title: Autenticação de dois fatores com SMS no ASP.NET Core
author: rick-anderson
description: Saiba como configurar a autenticação de dois fatores (2FA) com um aplicativo ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 116249a7cd4faebd0c899e383d86f5c5c3c7146a
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265247"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Autenticação de dois fatores com SMS no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [suíço desenvolvedores](https://github.com/Swiss-Devs)

>[!WARNING]
> Dois aplicativos de autenticador 2FA (autenticação) fator, usando uma baseada em tempo avulso senha algoritmo TOTP (), são o setor de abordagem 2fa recomendado. 2FA usar TOTP é preferencial para SMS 2FA. Para obter mais informações, consulte [geração de código de QR habilitar TOTP para aplicativos de autenticador no ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) para ASP.NET Core 2.0 e versões posteriores.

Este tutorial mostra como configurar a autenticação de dois fatores (2FA) usando o SMS. As instruções são fornecidas para [twilio](https://www.twilio.com/) e [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mas você pode usar qualquer outro provedor SMS. Recomendamos que você conclua [confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm) antes de iniciar este tutorial.

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Como baixar](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Criar um projeto ASP.NET Core

Criar um novo aplicativo web de ASP.NET Core chamado `Web2FA` com contas de usuário individuais. Siga as instruções em <xref:security/enforcing-ssl> para configurar e exigir HTTPS.

### <a name="create-an-sms-account"></a>Criar uma conta do SMS

Criar uma conta SMS, por exemplo, no [twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Registre as credenciais de autenticação (para o twilio: accountSid e authToken para ASPSMS: Userkey e senha).

#### <a name="figuring-out-sms-provider-credentials"></a>Descobrir as credenciais do provedor de SMS

**Twilio:**

Na guia Painel de sua conta do Twilio, copie o **SID da conta** e **token de autenticação**.

**ASPSMS:**

Em suas configurações de conta, navegue até **Userkey** e copie-o junto com seus **senha**.

Posteriormente, armazenaremos esses valores com a ferramenta secret manager nas chaves `SMSAccountIdentification` e `SMSAccountPassword`.

#### <a name="specifying-senderid--originator"></a>Especificando SenderID / originador

**Twilio:** Na guia números, copie o Twilio **número de telefone**.

**ASPSMS:** Dentro do Menu originadores de desbloqueio, desbloquear originadores de um ou mais ou escolha um originador alfanumérico (não é suportado por todas as redes).

Posteriormente, armazenaremos esse valor com a ferramenta secret manager na chave do `SMSAccountFrom`.

### <a name="provide-credentials-for-the-sms-service"></a>Forneça credenciais para o serviço SMS

Vamos usar o [padrão de opções](xref:fundamentals/configuration/options) para acessar as configurações de conta e chave de usuário.

* Crie uma classe para buscar a chave segura do SMS. Para este exemplo, o `SMSoptions` classe é criada na *Services/SMSoptions.cs* arquivo.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Defina as `SMSAccountIdentification`, `SMSAccountPassword` e `SMSAccountFrom` com o [ferramenta secret manager](xref:security/app-secrets). Por exemplo:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Adicione o pacote NuGet do provedor de SMS. Do Manager Console (PMC) execute:

**Twilio:**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Adicione código a *Services/MessageServices.cs* arquivo para habilitar o SMS. Use o Twilio ou seção ASPSMS:

**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Configurar a inicialização para usar `SMSoptions`

Adicione `SMSoptions` ao contêiner de serviço na `ConfigureServices` método na *Startup.cs*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Habilitar a autenticação de dois fatores

Abra o *Views/Manage/Index.cshtml* arquivo de exibição do Razor e remova o comentário caracteres (de modo que nenhuma marcação é comentada).

## <a name="log-in-with-two-factor-authentication"></a>Faça logon com a autenticação de dois fatores

* Execute o aplicativo e registrar um novo usuário

![Exibição do registro de aplicativo Web aberta no Microsoft Edge](2fa/_static/login2fa1.png)

* Toque em seu nome de usuário que ativa o `Index` método de ação no controlador de gerenciar. Em seguida, toque no número de telefone **adicionar** link.

![Gerenciar o modo de exibição – tocar no link "Adicionar"](2fa/_static/login2fa2.png)

* Adicionar um número de telefone que receberá o código de verificação e toque **enviar código de verificação**.

![Adicionar página de número de telefone](2fa/_static/login2fa3.png)

* Você receberá uma mensagem de texto com o código de verificação. Insira-o e toque em **enviar**

![Verifique se a página de número de telefone](2fa/_static/login2fa4.png)

Se você não receber uma mensagem de texto, consulte a página de registro do twilio.

* O modo de gerenciar mostra que o número de telefone foi adicionado com êxito.

![Gerenciar o modo de exibição - número de telefone foi adicionado com êxito](2fa/_static/login2fa5.png)

* Toque **habilitar** para habilitar a autenticação de dois fatores.

![Gerenciar o modo de exibição – habilitar a autenticação de dois fatores](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Autenticação de dois fatores do teste

* Faça logoff.

* Iniciar sessão.

* A conta de usuário tiver habilitado a autenticação de dois fatores, portanto, você precisa fornecer o segundo fator de autenticação. Neste tutorial, você habilitou a verificação por telefone. Os modelos internos permitem que você configure o email como o segundo fator. Você pode configurar os fatores adicionais de segundo para autenticação, como códigos QR. Toque **enviar**.

![Enviar modo de exibição de código de verificação](2fa/_static/login2fa7.png)

* Insira o código que você pode obter na mensagem SMS.

* Clicar na **lembrar deste navegador** caixa de seleção serão isentos da necessidade de usar 2FA para fazer logon usando o mesmo dispositivo e o navegador. Habilitar a 2FA e clicando em **lembrar deste navegador** lhe fornecerá 2FA forte proteção contra usuários mal-intencionados que tentam acessar sua conta, desde que não tiverem acesso ao seu dispositivo. Você pode fazer isso em qualquer dispositivo privado que você usa regularmente. Definindo **lembrar deste navegador**, você obtém a segurança adicional de 2FA de dispositivos que você não usa regularmente, e você obtém a conveniência em não ter que passar por 2FA em seus próprios dispositivos.

![Verifique se o modo de exibição](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Bloqueio de conta para proteção contra ataques de força bruta

Bloqueio de conta é recomendado com 2FA. Depois que um usuário faz logon por meio de uma conta local ou social, cada tentativa com falha no 2FA é armazenada. Se as tentativas de acesso com falha máximo for atingido, o usuário está bloqueado (padrão: 5 minutos bloqueio após 5 falhas em tentativas de acesso). Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio. O máximo falhas em tentativas de acesso e tempo de bloqueio pode ser definido com [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) e [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). O exemplo a seguir configura o bloqueio de conta por 10 minutos após 10 tentativas de acesso:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Confirme [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) define `lockoutOnFailure` para `true`:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
