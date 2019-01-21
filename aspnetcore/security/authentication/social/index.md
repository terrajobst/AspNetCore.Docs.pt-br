---
title: Autenticação de Facebook, Google e de provedor externo no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra como criar um aplicativo do ASP.NET Core 2.x usando o OAuth 2.0 com provedores de autenticação externa.
ms.author: riande
ms.custom: mvc
ms.date: 1/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: 48dd8b772234ff18158423a36ed1716102bc2f31
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396136"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Autenticação de Facebook, Google e de provedor externo no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial demonstra como criar um aplicativo ASP.NET Core 2.2, que permite aos usuários fazer logon usando o OAuth 2.0 com as credenciais de provedores de autenticação externa.

Os provedores [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) e [Microsoft](xref:security/authentication/microsoft-logins) são abordados nas seções a seguir. Outros provedores estão disponíveis em pacotes de terceiros, como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

![Ícones de mídia social do Facebook, Twitter, Google+ e Windows](index/_static/social.png)

Permitir que os usuários entrem com suas credenciais existentes é conveniente para os usuários e transfere muitas das complexidades de gerenciar o processo de entrada para um terceiro. Para obter exemplos de como os logons sociais podem impulsionar o tráfego e as conversões de clientes, consulte os estudos de caso do [Facebook](https://www.facebook.com/unsupportedbrowser) e do [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Criar um novo projeto ASP.NET Core

* No Visual Studio 2017, crie um projeto na Página Inicial ou por meio de **Arquivo** > **Novo** > **Projeto**.

* Selecione o modelo **Aplicativo Web ASP.NET Core**, disponível na categoria **Visual C#** > **.NET Core**:
* Selecione **Alterar Autenticação** e defina a autenticação para **Contas de Usuário Individuais**.

## <a name="apply-migrations"></a>Aplicar migrações

* Execute o aplicativo e selecione o link **Registrar**.
* Insira o email e a senha para a nova conta e, em seguida, selecione **Registrar**.
* Siga as instruções para aplicar as migrações.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Usar o SecretManager para armazenar os tokens atribuídos por provedores de logon

Os provedores de logon social atribuem tokens de **ID do Aplicativo** e **Segredo do Aplicativo** durante o processo de registro. Os nomes de token exatos variam de acordo com o provedor. Esses tokens representam as credenciais que seu aplicativo usa para acessar a API. Os tokens constituem os "segredos" que podem ser vinculados à configuração de aplicativo com a ajuda do [Secret Manager](xref:security/app-secrets#secret-manager). O Secret Manager é uma alternativa mais segura para armazenar os tokens em um arquivo de configuração, como *appsettings.json*.

> [!IMPORTANT]
> O Secret Manager destina-se apenas para fins de desenvolvimento. Você pode armazenar e proteger os segredos de teste e produção do Azure com o [provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration).

Siga as etapas no tópico [Armazenamento seguro dos segredos do aplicativo em desenvolvimento no ASP.NET Core](xref:security/app-secrets) para armazenar os tokens atribuídos por cada provedor de logon abaixo.

## <a name="setup-login-providers-required-by-your-application"></a>Configurar os provedores de logon necessários para o aplicativo

Use os seguintes tópicos para configurar seu aplicativo para usar os respectivos provedores:

* Instruções do [Facebook](xref:security/authentication/facebook-logins)
* Instruções do [Twitter](xref:security/authentication/twitter-logins)
* Instruções do [Google](xref:security/authentication/google-logins)
* Instruções da [Microsoft](xref:security/authentication/microsoft-logins)
* [Outras](xref:security/authentication/otherlogins) instruções do provedor

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>Definir a senha opcionalmente

Ao registrar um provedor de logon externo, você não precisa ter uma senha registrada no aplicativo. Isso o alivia da tarefa de criar e lembrar de uma senha para o site, mas também o torna dependente do provedor de logon externo. Se o provedor de logon externo não estiver disponível, você não poderá fazer logon no site.

Para criar uma senha e entrar usando seu email definido durante o processo de entrada com provedores externos:

* Selecione o link **Olá,&lt; &gt;alias de email** na parte superior direita para navegar até a exibição **Gerenciar**.

![Exibição Gerenciar do Aplicativo Web](index/_static/pass1a.png)

* Selecione **Criar**

![Definir a página de senha](index/_static/pass2a.png)

* Defina uma senha válida e use-a para entrar com seu email.

## <a name="next-steps"></a>Próximas etapas

* Este artigo apresentou a autenticação externa e explicou os pré-requisitos necessários para adicionar logons externos ao aplicativo ASP.NET Core.

* Páginas de referência específicas ao provedor para configurar logons para os provedores necessários para o aplicativo.

* Você talvez queira manter os dados adicionais sobre o usuário e seus tokens de atualização e acesso. Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.
