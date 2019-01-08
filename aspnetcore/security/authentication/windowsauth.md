---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core, usando o IIS Express, o IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/23/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 64178c8fce71445fc6a728a236d811484b21e3e0
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099254"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurar a autenticação do Windows no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)

[Autenticação do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) podem ser configuradas para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).

Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core. Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários. Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Habilitar a autenticação do Windows em um aplicativo ASP.NET Core

O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a>Usar o modelo de aplicativo de autenticação do Windows para um novo projeto

No Visual Studio:

1. Criar um novo **aplicativo Web ASP.NET Core**.
1. Selecione **aplicativo Web** da lista de modelos.
1. Selecione o **alterar autenticação** botão e selecione **autenticação do Windows**.

Execute o aplicativo. O nome de usuário é exibido na interface do usuário do aplicativo renderizado.

### <a name="manual-configuration-for-an-existing-project"></a>Configuração manual para um projeto existente

As propriedades do projeto permitem que você habilitar a autenticação do Windows e desabilite a autenticação anônima:

1. Clique com botão direito no projeto no Visual Studio **Gerenciador de soluções** e selecione **propriedades**.
1. Selecione a guia **Depurar**.
1. Desmarque a caixa de seleção **habilitar a autenticação anônima**.
1. Marque a caixa de seleção **habilitar a autenticação do Windows**.

Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Use o **autenticação do Windows** modelo de aplicativo.

Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:

```console
dotnet new webapp --auth Windows
```

---

## <a name="enable-windows-authentication-with-iis"></a>Habilitar a autenticação do Windows com o IIS

O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core. Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo. As seções a seguir mostram como:

* Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.
* Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.

### <a name="iis-configuration"></a>Configuração do IIS

Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.

Habilite o serviço de função do IIS para autenticação do Windows. Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).

Middleware de integração do IIS está configurado para autenticar solicitações de automaticamente por padrão. Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão. Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Criar um novo site do IIS

Especifique um nome e uma pasta e permitir que ele crie um novo pool de aplicativos.

### <a name="enable-windows-authentication-for-the-app-in-iis"></a>Habilitar a autenticação do Windows para o aplicativo no IIS

Use **qualquer** das seguintes abordagens:

* [Configuração do lado do desenvolvimento antes de publicar o aplicativo](#development-side-configuration-with-a-local-webconfig-file) (*recomendado*)
* [Configuração do lado do servidor depois de publicar o aplicativo](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a>Configuração do lado do desenvolvimento com um arquivo Web. config local

Execute as seguintes etapas **antes de** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).

Adicione o seguinte *Web. config* arquivo para a raiz do projeto:

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

Quando o projeto é publicado pelo SDK (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção. Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.

#### <a name="server-side-configuration-with-the-iis-manager"></a>Configuração do lado do servidor com o Gerenciador do IIS

Execute as seguintes etapas **após** você [publicar e implantar seu projeto](#publish-and-deploy-your-project-to-the-iis-site-folder).

1. No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.
1. Clique duas vezes em **autenticação** na **IIS** área.
1. Selecione **autenticação anônima**. Selecione **desabilite** na **ações** barra lateral.
1. Selecione **autenticação do Windows**. Selecione **habilitar** na **ações** barra lateral.

Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo. Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado. Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual. Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção que o SDK fornecido.

Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor. Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo. Use **qualquer** das abordagens a seguir para gerenciar as configurações:

* Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.
* Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações. Para obter mais informações, consulte o [configuração do lado do desenvolvimento](#development-side-configuration-with-a-local-webconfig-file) seção.

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a>Publicar e implantar seu projeto para a pasta de site do IIS

Usando o Visual Studio ou a CLI do .NET Core, publicar e implantar o aplicativo para a pasta de destino.

Para obter mais informações sobre hospedagem com o IIS, publicação e implantação, consulte os tópicos a seguir:

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

Inicie o aplicativo para verificar se a autenticação do Windows está funcionando.

## <a name="enable-windows-authentication-with-httpsys"></a>Habilitar a autenticação do Windows com o HTTP. sys

Embora o Kestrel não dá suporte a autenticação do Windows, você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys) para dar suporte a cenários são hospedados no Windows. O exemplo a seguir configura o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows:

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

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

ASP.NET Core não implementar a representação. Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo. Se você precisar executar explicitamente uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`. Executar uma única ação nesse contexto e, em seguida, feche o contexto.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos. Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.
