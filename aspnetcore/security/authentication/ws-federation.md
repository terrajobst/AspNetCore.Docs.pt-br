---
title: Autenticar usuários com o WS-Federation no ASP.NET Core
author: chlowell
description: Este tutorial demonstra como usar o WS-Federation em um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655424"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Autenticar usuários com o WS-Federation no ASP.NET Core

Este tutorial demonstra como permitir que os usuários entrem com um provedor de autenticação do WS-Federation, como Serviços de Federação do Active Directory (AD FS) (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD). Ele usa o aplicativo de exemplo ASP.NET Core 2,0 descrito no [Facebook, no Google e na autenticação de provedor externo](xref:security/authentication/social/index).

Para os aplicativos ASP.NET Core 2,0, o suporte ao WS-Federation é fornecido pela [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Esse componente é movido de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) e compartilha muitas das mecânicas desse componente. No entanto, os componentes diferem de algumas maneiras importantes.

Por padrão, o novo middleware:

* Não permite logons não solicitados. Esse recurso do protocolo WS-Federation é vulnerável a ataques XSRF. No entanto, ele pode ser habilitado com a opção `AllowUnsolicitedLogins`.
* Não verifica cada postagem de formulário para mensagens de entrada. Somente as solicitações para os `CallbackPath` são verificadas quanto a entradas. `CallbackPath` usa como padrão a `/signin-wsfed`, mas pode ser alterada por meio da propriedade herdada [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) da classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Esse caminho pode ser compartilhado com outros provedores de autenticação habilitando a opção [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Registrar o aplicativo com Active Directory

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory

* Abra o **Assistente Adicionar confiança** de terceira parte confiável do servidor no console de gerenciamento do ADFS:

![Adicionar Assistente de confiança de terceira parte confiável: Bem-vindo](ws-federation/_static/AdfsAddTrust.png)

* Escolha inserir dados manualmente:

![Adicionar Assistente de confiança de terceira parte confiável: selecionar fonte de dados](ws-federation/_static/AdfsSelectDataSource.png)

* Insira um nome de exibição para a terceira parte confiável. O nome não é importante para o aplicativo ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) não tem suporte para criptografia de token, portanto, não configure um certificado de criptografia de token:

![Adicionar Assistente de confiança de terceira parte confiável: configurar certificado](ws-federation/_static/AdfsConfigureCert.png)

* Habilite o suporte para o protocolo passivo do WS-Federation, usando a URL do aplicativo. Verifique se a porta está correta para o aplicativo:

![Adicionar Assistente de confiança de terceira parte confiável: configurar URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Deve ser uma URL HTTPS. IIS Express pode fornecer um certificado autoassinado ao hospedar o aplicativo durante o desenvolvimento. O Kestrel requer configuração manual de certificado. Consulte a [documentação do Kestrel](xref:fundamentals/servers/kestrel) para obter mais detalhes.

* Clique em **Avançar** no restante do assistente e **feche** no final.

* ASP.NET Core identidade requer uma declaração de **ID de nome** . Adicione um da caixa de diálogo **Editar regras de declaração** :

![Editar regras de declaração](ws-federation/_static/EditClaimRules.png)

* No **Assistente Adicionar regra de declaração de transformação**, deixe o modelo **Enviar atributos LDAP padrão como declarações** selecionado e clique em **Avançar**. Adicione um mapeamento de regra ao atributo **Sam-Account-Name** LDAP para a declaração de saída **ID de nome** :

![Assistente para Adicionar regra de declaração de transformação: configurar regra de declaração](ws-federation/_static/AddTransformClaimRule.png)

* Clique em **concluir** > **OK** na janela **Editar regras de declaração** .

### <a name="azure-active-directory"></a>Active Directory do Azure

* Navegue até a folha de registros do aplicativo do locatário do AAD. Clique em **novo registro de aplicativo**:

![Azure Active Directory: Registros de aplicativo](ws-federation/_static/AadNewAppRegistration.png)

* Insira um nome para o registro do aplicativo. Isso não é importante para o aplicativo ASP.NET Core.
* Insira a URL na qual o aplicativo escutará como a **URL de logon**:

![Azure Active Directory: criar registro de aplicativo](ws-federation/_static/AadCreateAppRegistration.png)

* Clique em **pontos de extremidade** e anote a URL do documento de metadados de **Federação** . Este é o `MetadataAddress`do middleware do WS-Federation:

![Azure Active Directory: pontos de extremidade](ws-federation/_static/AadFederationMetadataDocument.png)

* Navegue até o novo registro do aplicativo. Clique em **configurações** > **Propriedades** e anote o URI da **ID do aplicativo**. Este é o `Wtrealm`do middleware do WS-Federation:

![Azure Active Directory: Propriedades de registro do aplicativo](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Usar o WS-Federation sem identidade ASP.NET Core

O middleware do WS-Federation pode ser usado sem identidade. Por exemplo:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Adicionar o WS-Federation como um provedor de logon externo para ASP.NET Core identidade

* Adicione uma dependência em [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ao projeto.
* Adicione o WS-Federation a `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Fazer logon com o WS-Federation

Navegue até o aplicativo e clique no link **fazer logon** no cabeçalho de navegação. Há uma opção para fazer logon com o WsFederation: ![log na página](ws-federation/_static/WsFederationButton.png)

Com o ADFS como o provedor, o botão redireciona para uma página de entrada do ADFS: ![página de entrada do ADFS](ws-federation/_static/AdfsLoginPage.png)

Com Azure Active Directory como o provedor, o botão redireciona para uma página de entrada do AAD: ![página de entrada do AAD](ws-federation/_static/AadSignIn.png)

Uma entrada bem-sucedida para um novo usuário redireciona para a página de registro do usuário do aplicativo: ![página de registro](ws-federation/_static/Register.png)