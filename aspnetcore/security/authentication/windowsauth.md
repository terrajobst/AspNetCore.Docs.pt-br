---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core, usando o IIS Express, o IIS e o HTTP. sys.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/18/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 94dff2f47b2b076cb15f8d385239179b52786678
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637814"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurar a autenticação do Windows no ASP.NET Core

Por [Steve Smith](https://ardalis.com) e [Scott Addie](https://twitter.com/Scott_Addie)

Autenticação do Windows podem ser configurada para aplicativos ASP.NET Core hospedados com o IIS ou [HTTP. sys](xref:fundamentals/servers/httpsys).

## <a name="windows-authentication"></a>Autenticação do Windows

Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core. Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou outras contas do Windows para identificar os usuários. Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.

[Saiba mais sobre a autenticação do Windows e instalá-lo para o IIS](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Habilitar a autenticação do Windows em um aplicativo ASP.NET Core

O modelo de aplicativo Web Visual Studio pode ser configurado para dar suporte à autenticação do Windows.

### <a name="use-the-windows-authentication-app-template"></a>Use o modelo de aplicativo de autenticação do Windows

No Visual Studio:

1. Crie um novo Aplicativo Web ASP.NET Core.
1. Selecione o aplicativo Web na lista de modelos.
1. Selecione o **alterar autenticação** botão e selecione **autenticação do Windows**.

Execute o aplicativo. O nome de usuário aparece na parte superior direita do aplicativo.

![Captura de tela de navegador de autenticação Windows](windowsauth/_static/browser-screenshot.png)

Para trabalhos de desenvolvimento usando o IIS Express, o modelo fornece toda a configuração necessária para usar a autenticação do Windows. A seção a seguir mostra como configurar manualmente um aplicativo ASP.NET Core para autenticação do Windows.

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a>Configurações do Visual Studio para Windows e autenticação anônima

O projeto do Visual Studio **propriedades** da página **depurar** guia fornece caixas de seleção para a autenticação do Windows e autenticação anônima.

![Captura de tela do navegador de autenticação de Windows com as opções de autenticação realçadas](windowsauth/_static/vs-auth-property-menu.png)

Como alternativa, essas duas propriedades podem ser configuradas na *launchsettings. JSON* arquivo:

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a>Habilitar a autenticação do Windows com o IIS

O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core. Autenticação do Windows está configurada no IIS, não o aplicativo. As seções a seguir mostram como usar o Gerenciador do IIS para configurar um aplicativo ASP.NET Core para usar a autenticação do Windows.

### <a name="iis-configuration"></a>Configuração do IIS

Habilite o serviço de função do IIS para autenticação do Windows. Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).

Middleware de integração do IIS está configurado para autenticar solicitações de automaticamente por padrão. Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão. Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Criar um novo site do IIS

Especifique um nome e uma pasta e permitir que ele crie um novo pool de aplicativos.

### <a name="customize-authentication"></a>Personalizar autenticação

Abra os recursos de autenticação para o site.

![Menu de autenticação do IIS](windowsauth/_static/iis-authentication-menu.png)

Desabilitar a autenticação anônima e habilitar a autenticação do Windows.

![Configurações de autenticação do IIS](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a>Publicar seu projeto para a pasta de site do IIS

Usando o Visual Studio ou a CLI do .NET Core, publica o aplicativo para a pasta de destino.

![Caixa de diálogo de publicação do Visual Studio](windowsauth/_static/vs-publish-app.png)

Saiba mais sobre [publicar no IIS](xref:host-and-deploy/iis/index).

Inicie o aplicativo para verificar se a autenticação do Windows está funcionando.

## <a name="enable-windows-authentication-with-httpsys"></a>Habilitar a autenticação do Windows com o HTTP. sys

Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys) para dar suporte a cenários são hospedados no Windows. O exemplo a seguir configura o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows:

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

> [!NOTE]
> O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos. Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys. A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário. Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.

> [!NOTE]
> Não há suporte para http. sys no Nano Server versão 1709 ou posterior. Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="work-with-windows-authentication"></a>Trabalhar com a autenticação do Windows

Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo. As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.

### <a name="disallow-anonymous-access"></a>Não permitir acesso anônimo

Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito. Se o site do IIS (ou HTTP. sys) estiver configurado para não permitir acesso anônimo, a solicitação nunca atinge seu aplicativo. Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.

### <a name="allow-anonymous-access"></a>Permitir o acesso anônimo

Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos. O `[Authorize]` atributo permite que você possa proteger partes do aplicativo que realmente exigem a autenticação do Windows. O `[AllowAnonymous]` substituições de atributo `[Authorize]` atributo uso dentro de aplicativos que permitem o acesso anônimo. Ver [autorização simples](xref:security/authorization/simple) para obter detalhes de uso do atributo.

No ASP.NET Core 2.x, o `[Authorize]` atributo requer configuração adicional no *Startup.cs* desafiar solicitações anônimas para a autenticação do Windows. A configuração recomendada varia um pouco com base no servidor web que está sendo usado.

> [!NOTE]
> Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia. O [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".

#### <a name="iis"></a>IIS

Se usar o IIS, adicione o seguinte para o `ConfigureServices` método:

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

Se usando HTTP. sys, adicione o seguinte para o `ConfigureServices` método:

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Representação

ASP.NET Core não implementar a representação. Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo. Se você precisar executar explicitamente uma ação em nome do usuário, use `WindowsIdentity.RunImpersonated`. Executar uma única ação nesse contexto e, em seguida, feche o contexto.

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

Observe que `RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos. Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.
