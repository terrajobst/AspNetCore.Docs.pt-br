---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core para IIS e o HTTP. sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 900bbf5f14b1876ad537b2b77e4ba07d7aa168f2
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750158"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurar a autenticação do Windows no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [Luke Latham](https://github.com/guardrex)

[Autenticação do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) podem ser configuradas para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index) ou [HTTP. sys](xref:fundamentals/servers/httpsys).

Autenticação do Windows se baseia no sistema operacional para autenticar usuários de aplicativos ASP.NET Core. Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando identidades de domínio do Active Directory ou contas do Windows para identificar os usuários. Autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores web pertencem ao mesmo domínio do Windows.

## <a name="iisiis-express"></a>IIS/IIS Express

Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Configurações (depurador) de inicialização

Configuração para configurações de inicialização afeta apenas o *Properties/launchSettings.json* de arquivo para o IIS Express e não configura o IIS para a autenticação do Windows. Configuração do servidor é explicada com o [IIS](#iis) seção.

O **aplicativo Web** modelo disponível por meio do Visual Studio ou a CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows, que atualiza o *Properties/launchSettings.json* arquivo automaticamente.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**novo projeto**

1. Crie um novo projeto.
1. Selecione **Aplicativo Web ASP.NET Core**. Selecione **Avançar**.
1. Forneça um nome na **nome do projeto** campo. Confirme se o **local** entrada está correta ou fornecer um local para o projeto. Selecione **Criar**.
1. Selecione **alteração** sob **autenticação**.
1. No **alterar autenticação** janela, selecione **autenticação do Windows**. Selecione **OK**.
1. Selecione **Aplicativo Web**.
1. Selecione **Criar**.

Execute o aplicativo. O nome de usuário é exibido na interface do usuário do aplicativo renderizado.

**Projeto existente**

Propriedades do projeto, habilitar a autenticação do Windows e desabilite a autenticação anônima:

1. Clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Propriedades**.
1. Selecione a guia **Depurar**.
1. Desmarque a caixa de seleção **habilitar a autenticação anônima**.
1. Marque a caixa de seleção **habilitar a autenticação do Windows**.
1. Salve e feche a página de propriedades.

Como alternativa, as propriedades podem ser configuradas na `iisSettings` nó do *launchsettings. JSON* arquivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/CLI do .NET Core](#tab/visual-studio-code+netcore-cli)

**novo projeto**

Execute o [dotnet new](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (aplicativo Web do ASP.NET Core) e `--auth Windows` mudar:

```console
dotnet new webapp --auth Windows
```

**Projeto existente**

Atualizar o `iisSettings` nó do *launchsettings. JSON* arquivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [metapacote do Microsoft](xref:fundamentals/metapackage-app) **ou** o [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) pacote do NuGet.

### <a name="iis"></a>IIS

O IIS usa a [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar aplicativos ASP.NET Core. Autenticação do Windows está configurada para o IIS por meio de *Web. config* arquivo. As seções a seguir mostram como:

* Forneça um local *Web. config* arquivo que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.
* Use o Gerenciador do IIS para configurar o *Web. config* arquivo de um aplicativo ASP.NET Core que já tenha sido implantado no servidor.

Se você ainda não fez isso, habilite o IIS para hospedar aplicativos ASP.NET Core. Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.

Habilite o serviço de função do IIS para autenticação do Windows. Para obter mais informações, consulte [habilitar autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).

[Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) está configurado para autenticar solicitações de automaticamente por padrão. Para obter mais informações, consulte [Host ASP.NET Core no Windows com o IIS: Opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

O módulo do ASP.NET está configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão. Para obter mais informações, consulte [referência de configuração do módulo do ASP.NET Core: Atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Use **qualquer** das seguintes abordagens:

* **Antes de publicar e implantar o projeto** adicione o seguinte *Web. config* arquivo para a raiz do projeto:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Quando o projeto é publicado pelo SDK do .NET Core (sem o `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), publicado *Web. config* arquivo inclui o `<location><system.webServer><security><authentication>` seção. Para obter mais informações sobre o `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file>.

* **Após a publicação e implantação de projeto,** executar a configuração do lado do servidor com o Gerenciador do IIS:

  1. No Gerenciador do IIS, selecione o site do IIS sob o **Sites** nó do **conexões** barra lateral.
  1. Clique duas vezes em **autenticação** na **IIS** área.
  1. Selecione **autenticação anônima**. Selecione **desabilite** na **ações** barra lateral.
  1. Selecione **autenticação do Windows**. Selecione **habilitar** na **ações** barra lateral.

  Quando essas ações são executadas, o Gerenciador do IIS modifica o aplicativo *Web. config* arquivo. Um `<system.webServer><security><authentication>` nó for adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication`:

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  O `<system.webServer>` seção adicionada para o *Web. config* arquivo pelo Gerenciador do IIS está fora do aplicativo `<location>` seção adicionada pelo SDK do .NET Core quando o aplicativo for publicado. Porque a seção é adicionada fora das `<location>` nó, as configurações são herdadas por qualquer [subaplicativos](xref:host-and-deploy/iis/index#sub-applications) ao aplicativo atual. Para impedir a herança, mova a adicionada `<security>` seção dentro do `<location><system.webServer>` seção SDK do .NET Core é fornecido.

  Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta somente o aplicativo *Web. config* arquivo no servidor. Uma implantação subsequente do aplicativo pode substituir as configurações no servidor se a cópia do servidor do *Web. config* é substituído do projeto *Web. config* arquivo. Use **qualquer** das abordagens a seguir para gerenciar as configurações:

  * Use o Gerenciador do IIS para redefinir as configurações na *Web. config* depois que o arquivo será substituído na implantação de arquivos.
  * Adicionar um *arquivo Web. config* para o aplicativo localmente com as configurações.

## <a name="httpsys"></a>HTTP.sys

Em cenários de hospedagem interna [Kestrel](xref:fundamentals/servers/kestrel) não suporte à autenticação do Windows, mas você pode usar [HTTP. sys](xref:fundamentals/servers/httpsys).

Adicionar serviços de autenticação, invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) em `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Configurar o host de web do aplicativo para usar o HTTP. sys com a autenticação do Windows (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> está no <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos. Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys. A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário. Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.

> [!NOTE]
> Não há suporte para http. sys no Nano Server versão 1709 ou posterior. Para usar a autenticação do Windows e o HTTP. sys com o Nano Server, use uma [contêiner de Server Core (microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Para obter mais informações sobre o Server Core, consulte [qual é a opção de instalação Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizar usuários

Estado da configuração do acesso anônimo determina o modo no qual o `[Authorize]` e `[AllowAnonymous]` atributos são usados no aplicativo. As seções a seguir explicam como lidar com os estados de configuração não permitidos e têm permissão de acesso anônimo.

### <a name="disallow-anonymous-access"></a>Não permitir acesso anônimo

Quando a autenticação do Windows está habilitada e o acesso anônimo é desabilitado, o `[Authorize]` e `[AllowAnonymous]` atributos não têm nenhum efeito. Se um site do IIS é configurado para não permitir acesso anônimo, a solicitação nunca alcance o aplicativo. Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.

### <a name="allow-anonymous-access"></a>Permitir o acesso anônimo

Quando a autenticação do Windows e o acesso anônimo estão habilitados, use o `[Authorize]` e `[AllowAnonymous]` atributos. O `[Authorize]` atributo permite proteger pontos de extremidade do aplicativo que exige autenticação. O `[AllowAnonymous]` substituições de atributo a `[Authorize]` atributo em aplicativos que permitem acesso anônimo. Para obter detalhes de uso do atributo, consulte <xref:security/authorization/simple>.

> [!NOTE]
> Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia. O [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma melhor experiência de "Acesso negado".

## <a name="impersonation"></a>Representação

ASP.NET Core não implementar a representação. Aplicativos executados com a identidade do aplicativo para todas as solicitações, usando a identidade de pool ou processo do aplicativo. Se o aplicativo deve executar uma ação em nome do usuário, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) em `Startup.Configure`. Executar uma única ação nesse contexto e, em seguida, feche o contexto.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos. Por exemplo, solicitações de todas ou cadeias de middleware de encapsulamento não é tem suporte ou recomendado.

## <a name="claims-transformations"></a>Transformações de declarações

Ao hospedar com o modo de em processo do IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Para obter mais informações e um exemplo de código que ativa as transformações de declarações quando no processo de hospedagem, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.

## <a name="additional-resources"></a>Recursos adicionais

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
