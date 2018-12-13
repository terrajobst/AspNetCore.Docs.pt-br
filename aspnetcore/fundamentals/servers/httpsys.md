---
title: Implementação do servidor Web HTTP.sys no ASP.NET Core
author: guardrex
description: Conheça o HTTP.sys, um servidor Web para o ASP.NET Core executado no Windows. Desenvolvido com base no driver de modo kernel, o HTTP.sys é uma alternativa ao Kestrel, que pode ser usado na conexão direta com a Internet sem o IIS.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8810fd295e8c4269812e712ce2fdc9b9fa2bbb4f
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861687"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementação do servidor Web HTTP.sys no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Este tópico se aplica ao ASP.NET Core 2.0 ou versão superior. Em versões anteriores do ASP.NET Core, o HTTP.sys é chamado [WebListener](xref:fundamentals/servers/weblistener).

O [HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) é um [servidor Web para ASP.NET Core](xref:fundamentals/servers/index) executado apenas no Windows. O HTTP.sys é uma alternativa ao servidor [Kestrel](xref:fundamentals/servers/kestrel) e oferece alguns recursos não disponibilizados pelo Kestrel.

> [!IMPORTANT]
> Não é possível usar o HTTP.sys com o IIS ou o IIS Express, pois ele é incompatível com o [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).

O HTTP.sys dá suporte aos seguintes recursos:

* [Autenticação do Windows](xref:security/authentication/windowsauth)
* Compartilhamento de porta
* HTTPS com SNI
* HTTP/2 sobre TLS (Windows 10 ou posterior)
* Transmissão direta de arquivo
* Cache de resposta
* WebSockets (Windows 8 ou posterior)

Versões do Windows compatíveis:

* Windows 7 ou posterior
* Windows Server 2008 R2 ou posterior

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Quando usar o HTTP.sys

O HTTP.sys é útil nas implantações em que:

* É necessário expor o servidor diretamente à Internet sem usar o IIS.

  ![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

* As implantações internas exigem um recurso que não está disponível no Kestrel, como a [Autenticação do Windows](xref:security/authentication/windowsauth).

  ![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

O HTTP.sys é uma tecnologia madura que protege contra vários tipos de ataques e proporciona as propriedades de robustez, segurança e escalabilidade de um servidor Web completo. O próprio IIS é executado como um ouvinte HTTP sobre o HTTP.sys.

## <a name="http2-support"></a>Compatibilidade com HTTP/2

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará habilitado para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:

* Windows Server 2016/Windows 10 ou posterior
* Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)
* Conexão TLS 1.2 ou posterior

::: moniker range=">= aspnetcore-2.2"

Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.

::: moniker-end

O HTTP/2 está habilitado por padrão. Se uma conexão HTTP/2 não tiver sido estabelecida, a conexão retornará para HTTP/1.1. Em uma versão futura do Windows, os sinalizadores de configuração de HTTP/2 estarão disponíveis e contarão com a capacidade de desabilitar o HTTP/2 com HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Autenticação de modo kernel com Kerberos

O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos. Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys. A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário. Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.

## <a name="how-to-use-httpsys"></a>Como usar o HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Configurar o aplicativo ASP.NET Core para usar o HTTP.sys

1. Não é necessário usar uma referência do pacote no arquivo de projeto ao usar o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 ou posterior). Se não estiver usando o metapacote `Microsoft.AspNetCore.App`, adicione uma referência do pacote a [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

2. Chame o método de extensão [UseHttpSys](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderhttpsysextensions.usehttpsys) quando criar o Host Web, especificando todas as [opções do HTTP.sys](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions) necessárias:

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   A configuração adicional do HTTP.sys é tratada por meio das [configurações do registro](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Opções do HTTP.sys**

   | Propriedade | Descrição | Padrão |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.allowsynchronousio) | Controlar quando a Entrada/Saída síncrona deve ser permitida para `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `true` |
   | [Authentication.AllowAnonymous](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.allowanonymous) | Permitir solicitações anônimas. | `true` |
   | [Authentication.Schemes](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.schemes) | Especificar os esquemas de autenticação permitidos. É possível modificar a qualquer momento antes de descartar o ouvinte. Os valores são fornecidos pela [enumeração AuthenticationSchemes](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationschemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
   | [EnableResponseCaching](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.enableresponsecaching) | Tentativa de cache do [modo kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) para obtenção de respostas com cabeçalhos qualificados. A resposta pode não incluir `Set-Cookie`, `Vary` ou cabeçalhos `Pragma`. Ela deve incluir um cabeçalho `Cache-Control` que seja `public` e um valor `shared-max-age` ou `max-age`, ou um cabeçalho `Expires`. | `true` |
   | [MaxAccepts](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxaccepts) | O número máximo de aceitações simultâneas. | 5 &times; [Ambiente.<br>ProcessorCount](/dotnet/api/system.environment.processorcount) |
   | [MaxConnections](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxconnections) | O número máximo de conexões simultâneas a serem aceitas. Usar `-1` como infinito. Usar `null` a fim de usar a configuração que abranja toda máquina do registro. | `null`<br>(ilimitado) |
   | [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) | Confira a seção <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 de bytes<br>(28,6 MB) |
   | [RequestQueueLimit](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.requestqueuelimit) | O número máximo de solicitações que podem ser colocadas na fila. | 1000 |
   | [ThrowWriteExceptions](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.throwwriteexceptions) | Indica se as gravações do corpo da resposta que falham quando o cliente se desconecta devem gerar exceções ou serem concluídas normalmente. | `false`<br>(concluir normalmente) |
   | [Timeouts](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.timeouts) | Expor a configuração [TimeoutManager](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager) do HTTP.sys, que também pode ser definida no registro. Siga os links de API para saber mais sobre cada configuração, inclusive os valores padrão:<ul><li>[TimeoutManager.DrainEntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.drainentitybody) &ndash; O tempo permitido para que a API do servidor HTTP esvazie o corpo da entidade em uma conexão Keep-Alive.</li><li>[TimeoutManager.EntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.entitybody) &ndash; O tempo permitido para a chegada do corpo da entidade de solicitação.</li><li>[TimeoutManager.HeaderWait](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.headerwait) &ndash; O tempo permitido para que a API do servidor HTTP analise o cabeçalho da solicitação.</li><li>[TimeoutManager.IdleConnection](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.idleconnection) &ndash; O tempo permitido para uma conexão ociosa.</li><li>[TimeoutManager.MinSendBytesPerSecond](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.minsendbytespersecond) &ndash; A taxa de envio mínima para a resposta.</li><li>[TimeoutManager.RequestQueue](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.requestqueue) &ndash; O tempo permitido para que a solicitação permaneça na fila de solicitações até o aplicativo coletá-la.</li></ul> |  |
   | [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) | Especifique a [UrlPrefixCollection](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection) para registrar com o HTTP.sys. A mais útil é [UrlPrefixCollection.Add](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection.add), que é usada para adicionar um prefixo à coleção. É possível modificá-las a qualquer momento antes de descartar o ouvinte. |  |

   <a name="maxrequestbodysize"></a>
   **MaxRequestBodySize**

   O tamanho máximo permitido em bytes para todos os corpos de solicitação. Quando é definido como `null`, o tamanho máximo do corpo da solicitação é ilimitado. Esse limite não afeta as conexões atualizadas que são sempre ilimitadas.

   O método recomendado para substituir o limite em um aplicativo ASP.NET Core MVC para um único `IActionResult` é usar o atributo [RequestSizeLimitAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) em um método de ação:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Uma exceção é gerada quando o aplicativo tenta configurar o limite de uma solicitação, depois que o aplicativo inicia a leitura da solicitação. É possível usar uma propriedade `IsReadOnly` para indicar se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.

   Se o aplicativo tiver que substituir [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) por solicitação, use [IHttpMaxRequestBodySizeFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpmaxrequestbodysizefeature):

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Quando usar o Visual Studio, verifique se que o aplicativo está configurado para executar o IIS ou IIS Express.

   No Visual Studio, o perfil de inicialização padrão destina-se ao IIS Express. Para executar o projeto como um aplicativo de console, altere manualmente o perfil selecionado, conforme mostrado na captura de tela a seguir:

   ![Selecionar o perfil do aplicativo de console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurar o Windows Server

1. Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd), instale o .NET Core, o .NET Framework ou ambos (caso o aplicativo .NET Core seja direcionado ao .NET Framework).

   * **.NET Core** &ndash; Se o aplicativo requer o .NET Core, obtenha e execute o instalador do .NET Core em [Todos os downloads do .NET](https://www.microsoft.com/net/download/all).
   * **.NET Framework** &ndash; Se o aplicativo exigir o .NET Framework, confira [Guia de instalação do .NET Framework](/dotnet/framework/install/) para obter instruções de instalação. Instale o .NET Framework necessário. O instalador do .NET Framework mais recente pode ser encontrado em [Todos os downloads do .NET](https://www.microsoft.com/net/download/all).

2. Configurar URLs e portas para o aplicativo.

   Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Para configurar portas e prefixos de URL, as opções incluem usar:

   * [UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
   * O argumento de linha de comando `urls`
   * A variável de ambiente `ASPNETCORE_URLS`
   * [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes)

   O exemplo de código a seguir mostra como usar [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes):

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=11)]

   Uma vantagem de usar `UrlPrefixes` é que uma mensagem de erro é gerada imediatamente no caso de prefixos formatados de forma incorreta.

   As configurações de `UrlPrefixes` substituem as configurações `UseUrls`/`urls`/`ASPNETCORE_URLS`. Portanto, uma vantagem de usar `UseUrls`, `urls` e a variável de ambiente `ASPNETCORE_URLS` é que fica mais fácil alternar entre o Kestrel e o HTTP.sys. Para saber mais sobre `UseUrls`, `urls` e `ASPNETCORE_URLS`, confira o tópico [Host no ASP.NET Core](xref:fundamentals/host/index).

   O HTTP.sys usa os [formatos de cadeia de caracteres UrlPrefix da API do Servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas. Associações de curinga de nível superior podem abrir o aplicativo para vulnerabilidades de segurança. Isso se aplica a curingas fortes e fracos. Use nomes de host explícitos em vez de curingas. Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não têm esse risco de segurança se você controlar o domínio pai completo (em vez de `*.com`, o qual é vulnerável). Veja [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) para obter mais informações.

3. Faça o pré-registro dos prefixos de URL para associá-los ao HTTP.sys e configurar certificados X.509.

   Se os prefixos de URL não estiverem pré-registrados no Windows, execute o aplicativo com privilégios de administrador. A única exceção ocorre durante a associação ao localhost usando HTTP (não HTTPS) com um número de porta superior a 1024. Nesse caso, não é necessário usar privilégios de administrador.

   1. O *netsh.exe* é a ferramenta interna destinada a configurar o HTTP.sys. Com o *netsh.exe*, é possível reservar prefixos de URL e atribuir certificados X.509. A ferramenta exige privilégios de administrador.

      O exemplo a seguir mostra os comandos necessários para reservar prefixos de URL para as portas 80 e 443:

      ```console
      netsh http add urlacl url=http://+:80/ user=Users
      netsh http add urlacl url=https://+:443/ user=Users
      ```

      O exemplo a seguir mostra como atribuir um certificado X.509:

      ```console
      netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid="{00000000-0000-0000-0000-000000000000}"
      ```

      Documentação de referência do *netsh.exe*:

      * [Comandos do Netsh para o protocolo HTTP](https://technet.microsoft.com/library/cc725882.aspx)
      * [Cadeias de caracteres de UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   2. Crie certificados X.509 autoassinados, quando necessário.

      [!INCLUDE [How to make an X.509 cert](../../includes/make-x509-cert.md)]

4. Abra as portas do firewall para permitir que o tráfego chegue ao HTTP.sys. Use o *netsh.exe* ou os [cmdlets do PowerShell](https://technet.microsoft.com/library/jj554906).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Para aplicativos hospedados pelo HTTP.sys que interagem com solicitações da Internet ou de uma rede corporativa, podem ser necessárias configurações adicionais ao hospedar atrás de balanceadores de carga e de servidores proxy. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Recursos adicionais

* [API do servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [Repositório aspnet/HttpSysServer do GitHub (código-fonte)](https://github.com/aspnet/HttpSysServer/)
* <xref:fundamentals/host/index>
* <xref:test/troubleshoot>
