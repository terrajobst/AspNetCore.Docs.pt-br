---
title: Autenticação de Facebook, Google e de provedor externo no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra como criar um aplicativo ASP.NET Core usando o OAuth 2,0 com provedores de autenticação externa.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: 7d0f6647a6f5a4d41067b13acd3d294144027bb7
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727325"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Autenticação de Facebook, Google e de provedor externo no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial demonstra como criar um aplicativo ASP.NET Core 3,0 que permite que os usuários entrem usando o OAuth 2,0 com credenciais de provedores de autenticação externa.

Os provedores do [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)e [Microsoft](xref:security/authentication/microsoft-logins) são abordados nas seções a seguir e usam o projeto inicial criado neste artigo. Outros provedores estão disponíveis em pacotes de terceiros, como [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) e [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).

Permitir que os usuários entrem com suas credenciais existentes:

* É conveniente para os usuários.
* Remove muitas das complexidades de gerenciar o processo de conexão para um terceiro.

Para obter exemplos de como os logons sociais podem impulsionar o tráfego e as conversões de clientes, consulte os estudos de caso do [Facebook](https://www.facebook.com/unsupportedbrowser) e do [Twitter](https://dev.twitter.com/resources/case-studies).

## <a name="create-a-new-aspnet-core-project"></a>Criar um novo projeto ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Crie um novo projeto.
* Selecione **Aplicativo Web ASP.NET Core** e **Próximo**.
* Forneça um **Nome do projeto** e confirme ou altere a **localização**. Selecione **Criar**.
* Selecione a versão mais recente do ASP.NET Core na lista suspensa (**ASP.NET Core {X. Y}** ) e, em seguida, selecione **aplicativo Web**.
* Selecione **Autenticação** selecione **Mudar** e configure a autenticação para **Contas de Usuário Individuais**. Selecione **OK**.
* Na janela **Criar novo aplicativo Web ASP.NET Core**, selecione **Criar**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Abra o terminal.  Para Visual Studio Code você pode abrir o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.

* Para Windows, execute o seguinte comando:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  Para macOS e Linux, execute o seguinte comando:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * O comando `dotnet new` cria um projeto do Razor Pages na pasta *WebApp1*.
  * `-au Individual` cria o código para autenticação individual.
  * `-uld` usa o LocalDB, uma versão leve do SQL Server Express para Windows. Omita `-uld` para usar o SQLite.
  * O comando `code` abre a pasta *WebApp1* em uma nova instância do Visual Studio Code.

---

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

Ao registrar um provedor de logon externo, você não precisa ter uma senha registrada no aplicativo. Isso o alivia da tarefa de criar e lembrar de uma senha para o site, mas também o torna dependente do provedor de logon externo. Se o provedor de logon externo não estiver disponível, você não poderá se conectar ao site.

Para criar uma senha e entrar usando seu email definido durante o processo de entrada com provedores externos:

* Selecione o link **Olá,&lt;&gt;alias de email** no canto superior direito para navegar até a exibição **Gerenciar**.

![Exibição Gerenciar do Aplicativo Web](index/_static/pass1a.png)

* Escolha **Criar**

![Definir a página de senha](index/_static/pass2a.png)

* Defina uma senha válida e use-a para entrar com seu email.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/10563) para obter informações sobre como personalizar os botões de logon.
* Este artigo apresentou a autenticação externa e explicou os pré-requisitos necessários para adicionar logons externos ao aplicativo ASP.NET Core.
* Páginas de referência específicas ao provedor para configurar logons para os provedores necessários para o aplicativo.
* Você talvez queira manter os dados adicionais sobre o usuário e seus tokens de atualização e acesso. Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.
