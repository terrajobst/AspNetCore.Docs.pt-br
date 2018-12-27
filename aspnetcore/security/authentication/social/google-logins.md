---
title: Configuração de logon externo do Google no ASP.NET Core
author: rick-anderson
description: Este tutorial demonstra a integração da autenticação de usuário de conta do Google em um aplicativo ASP.NET Core existente.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/11/2018
uid: security/authentication/google-logins
ms.openlocfilehash: 4bb5a36cf654deb694d60da126fa42baf382f729
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735759"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuração de logon externo do Google no ASP.NET Core

Por [Valeriy Novytskyy](https://github.com/01binary) e [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como permitir que seus usuários entrar com sua conta do Google + usando um projeto do ASP.NET Core 2.0 de exemplo criado na [página anterior](xref:security/authentication/social/index). Vamos começar seguindo as [etapas oficiais](https://developers.google.com/identity/sign-in/web/devconsole-project) para criar um novo aplicativo no Console de API do Google.

## <a name="create-the-app-in-google-api-console"></a>Criar o aplicativo no Console de API do Google

* Navegue até [ https://console.developers.google.com/projectselector/apis/library ](https://console.developers.google.com/projectselector/apis/library) e entre. Se você ainda não tiver uma conta do Google, use **mais opções** > **[criar conta](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  link para criar um:

![Console de API do Google](index/_static/GoogleConsoleLogin.png)

* Você será redirecionado para **biblioteca do Gerenciador de API** página:

![Na página da biblioteca do Gerenciador de API de aterrissagem](index/_static/GoogleConsoleSwitchboard.png)

* Toque **Create** e digite sua **nome do projeto**:

![Caixa de diálogo Novo Projeto](index/_static/GoogleConsoleNewProj.png)

* Depois de aceitar a caixa de diálogo, você será redirecionado para a página da biblioteca, permitindo que você escolha os recursos para seu novo aplicativo. Encontre **API do Google +** na lista e clique no respectivo link para adicionar o recurso de API:

![Pesquise "API do Google +" na página da biblioteca do Gerenciador de API](index/_static/GoogleConsoleChooseApi.png)

* A página para a API adicionada recentemente é exibida. Toque **habilitar** para adicionar o Google + sinal de recurso ao seu aplicativo:

![Na página do Gerenciador de API do Google + API de aterrissagem](index/_static/GoogleConsoleEnableApi.png)

* Depois de habilitar a API, toque em **criar credenciais** para configurar os segredos:

![Criar botão de credenciais na página Gerenciador de API do Google + API](index/_static/GoogleConsoleGoCredentials.png)

* Escolha:
  * **Google + API**
  * **Servidor Web (por exemplo, Node. js, Tomcat)**, e
  * **Dados de usuário**:

![Página de credenciais de Gerenciador de API: Descubra quais tipos de credenciais que você precisa de painel](index/_static/GoogleConsoleChooseCred.png)

* Toque **as credenciais que eu preciso?** que leva você até a segunda etapa da configuração de aplicativo **criar uma ID de cliente OAuth 2.0**:

![Página de credenciais de Gerenciador de API: Criar uma ID de cliente OAuth 2.0](index/_static/GoogleConsoleCreateClient.png)

* Como estamos criando um projeto do Google + com apenas um recurso (entrada), podemos inserir os mesmos **nome** para a ID do cliente OAuth 2.0 é usado para o projeto.

* Insira o URI de desenvolvimento com `/signin-google` acrescentados em de **URIs de redirecionamento autorizado** campo (por exemplo: `https://localhost:44320/signin-google`). A autenticação do Google configurada mais tarde neste tutorial automaticamente manipulará as solicitações em `/signin-google` rota para implementar o fluxo de OAuth.

> [!NOTE]
> O segmento URI `/signin-google` é definido como o retorno de chamada padrão do provedor de autenticação do Google. Você pode alterar o retorno de chamada padrão URI ao configurar o middleware de autenticação do Google via o herdadas [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) propriedade da [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) classe.

* Pressione TAB para adicionar o **URIs de redirecionamento autorizados** entrada.

* Toque **criar ID do cliente**, que leva você até a terceira etapa, **configurar a tela de consentimento do OAuth 2.0**:

![Página de credenciais de Gerenciador de API: Configurar a tela de consentimento do OAuth 2.0](index/_static/GoogleConsoleAddCred.png)

* Insira seu voltadas ao pública **endereço de Email** e o **nome do produto** mostrado para seu aplicativo ao Google + solicita ao usuário para entrar. Opções adicionais estão disponíveis em **mais opções de personalização**.

* Toque **Continue** para prosseguir para a última etapa, **baixar credenciais**:

![Página de credenciais de Gerenciador de API: Baixar credenciais](index/_static/GoogleConsoleFinish.png)

* Toque **Baixe** para salvar um arquivo JSON com segredos do aplicativo, e **feito** para concluir a criação do novo aplicativo.

* Ao implantar o site será necessário rever a **Console do Google** e registrar uma nova url pública.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID e ClientSecret

Vincular as configurações confidenciais, como Google `Client ID` e `Client Secret` para sua configuração de aplicativo usando o [Secret Manager](xref:security/app-secrets). Para os fins deste tutorial, nomeie os tokens `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret`.

Os valores para esses tokens podem ser encontrados no arquivo JSON baixado na etapa anterior em `web.client_id` e `web.client_secret`.

## <a name="configure-google-authentication"></a>Configurar a autenticação do Google

::: moniker range=">= aspnetcore-2.0"

Adicione o serviço do Google na `ConfigureServices` método no *Startup.cs* arquivo:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

O modelo de projeto usado neste tutorial garante que [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) pacote é instalado.

* Para instalar este pacote com o Visual Studio 2017, clique com botão direito no projeto e selecione **gerenciar pacotes NuGet**.
* Para instalar o .NET Core CLI, execute o seguinte comando no diretório do projeto:

`dotnet add package Microsoft.AspNetCore.Authentication.Google`

Adicione o middleware do Google na `Configure` método no *Startup.cs* arquivo:

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

::: moniker-end

Consulte a [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) referência da API para obter mais informações sobre opções de configuração com suporte pela autenticação do Google. Isso pode ser usado para solicitar informações diferentes sobre o usuário.

## <a name="sign-in-with-google"></a>Entrar com o Google

Executar o aplicativo e clique em **faça logon no**. Será exibida uma opção para entrar com o Google:

![Aplicativo Web em execução no Microsoft Edge: Usuário não autenticado](index/_static/DoneGoogle.png)

Quando você clica no Google, você será redirecionado para o Google para autenticação:

![Diálogo de autenticação do Google](index/_static/GoogleLogin.png)

Depois de inserir suas credenciais do Google, em seguida, você será redirecionado para o site da web onde você pode definir seu email.

Agora você está conectado usando suas credenciais do Google:

![Aplicativo Web em execução no Microsoft Edge: Usuário autenticado](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Solução de problemas

* Se você receber um `403 (Forbidden)` página de erro de seu próprio aplicativo quando em execução no modo de desenvolvimento (ou interrupção no depurador com o mesmo erro), certifique-se de que **API do Google +** foi habilitada no **debibliotecadoGerenciadordeAPI** seguindo as etapas listadas [anteriormente nesta página](#create-the-app-in-google-api-console). Se a entrada não funciona e você não estiver recebendo erros, alterne para modo de desenvolvimento para que o problema mais fácil de depurar.
* **ASP.NET Core 2.x somente:** Se a identidade não está configurada por meio da chamada `services.AddIdentity` na `ConfigureServices`, a tentativa de autenticar resultará em *ArgumentException: A opção 'SignInScheme' deve ser fornecida*. O modelo de projeto usado neste tutorial garante que isso é feito.
* Se o banco de dados do site não tiver sido criado aplicando-se a migração inicial, você obterá *uma operação de banco de dados falhou ao processar a solicitação* erro. Toque **aplicar migrações** para criar o banco de dados e atualizar para continuar após o erro.

## <a name="next-steps"></a>Próximas etapas

* Este artigo mostrou como você pode autenticar com o Google. Você pode seguir uma abordagem semelhante para autenticar com outros provedores listados na [página anterior](xref:security/authentication/social/index).

* Depois de publicar seu site da web para aplicativo web do Azure, você deve redefinir o `ClientSecret` no Console de API do Google.

* Defina as `Authentication:Google:ClientId` e `Authentication:Google:ClientSecret` como configurações de aplicativo no portal do Azure. Configurar o sistema de configuração para ler as chaves de variáveis de ambiente.
