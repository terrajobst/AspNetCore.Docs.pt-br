---
title: Implementação do servidor Web HTTP.sys no ASP.NET Core
author: guardrex
description: Conheça o HTTP.sys, um servidor Web para o ASP.NET Core executado no Windows. Desenvolvido com base no driver de modo kernel, o HTTP.sys é uma alternativa ao Kestrel, que pode ser usado na conexão direta com a Internet sem o IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/20/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: eefe507efadb5ef0a03854d931402f9eaa23a266
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394767"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementação do servidor Web HTTP.sys no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Luke Latham](https://github.com/guardrex)

O [HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) é um [servidor Web para ASP.NET Core](xref:fundamentals/servers/index) executado apenas no Windows. O HTTP.sys é uma alternativa ao servidor [Kestrel](xref:fundamentals/servers/kestrel) e oferece alguns recursos não disponibilizados pelo Kestrel.

> [!IMPORTANT]
> O HTTP.sys não é compatível com o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e não pode ser usado com IIS ou IIS Express.

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

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([como baixar](xref:index#how-to-download-a-sample))

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

2. Chame o método de extensão <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> ao compilar o host, especificando qualquer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> necessária. O exemplo a seguir define as opções para seus valores padrão:

::: moniker range=">= aspnetcore-3.0"

   ```csharp
   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .UseStartup<Startup>()
          .UseHttpSys(options =>
          {
              options.AllowSynchronousIO = false;
              options.Authentication.Schemes = AuthenticationSchemes.None;
              options.Authentication.AllowAnonymous = true;
              options.MaxConnections = null;
              options.MaxRequestBodySize = 30000000;
              options.UrlPrefixes.Add("http://localhost:5000");
          });
   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

::: moniker-end

   A configuração adicional do HTTP.sys é tratada por meio das [configurações do registro](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **Opções do HTTP.sys**

::: moniker range=">= aspnetcore-3.0"

   | Propriedade | DESCRIÇÃO | Padrão |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Controlar quando a Entrada/Saída síncrona deve ser permitida para `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `false` |
   | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Permitir solicitações anônimas. | `true` |
   | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Especificar os esquemas de autenticação permitidos. É possível modificar a qualquer momento antes de descartar o ouvinte. Os valores são fornecidos pela [enumeração AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tentativa de cache do [modo kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) para obtenção de respostas com cabeçalhos qualificados. A resposta pode não incluir `Set-Cookie`, `Vary` ou cabeçalhos `Pragma`. Ela deve incluir um cabeçalho `Cache-Control` que seja `public` e um valor `shared-max-age` ou `max-age`, ou um cabeçalho `Expires`. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | O número máximo de aceitações simultâneas. | 5 &times; [Ambiente.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | O número máximo de conexões simultâneas a serem aceitas. Usar `-1` como infinito. Usar `null` a fim de usar a configuração que abranja toda máquina do registro. | `null`<br>(ilimitado) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Confira a seção <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 de bytes<br>(28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | O número máximo de solicitações que podem ser colocadas na fila. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indica se as gravações do corpo da resposta que falham quando o cliente se desconecta devem gerar exceções ou serem concluídas normalmente. | `false`<br>(concluir normalmente) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expor a configuração <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> do HTTP.sys, que também pode ser configurado no Registro. Siga os links de API para saber mais sobre cada configuração, inclusive os valores padrão:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; O tempo permitido para que a API do servidor HTTP esvazie o corpo da entidade em uma conexão Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; O tempo permitido para a chegada do corpo da entidade de solicitação.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; O tempo permitido para que a API do servidor HTTP analise o cabeçalho da solicitação.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; O tempo permitido para uma conexão ociosa.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; A taxa de envio mínima para a resposta.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; O tempo permitido para que a solicitação permaneça na fila de solicitações até o aplicativo coletá-la.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Especifique o <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> para registrar com o HTTP.sys. A mais útil é [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), que é usada para adicionar um prefixo à coleção. É possível modificá-las a qualquer momento antes de descartar o ouvinte. |  |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

   | Propriedade | DESCRIÇÃO | Padrão |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Controlar quando a Entrada/Saída síncrona deve ser permitida para `HttpContext.Request.Body` e `HttpContext.Response.Body`. | `true` |
   | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Permitir solicitações anônimas. | `true` |
   | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Especificar os esquemas de autenticação permitidos. É possível modificar a qualquer momento antes de descartar o ouvinte. Os valores são fornecidos pela [enumeração AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`. | `None` |
   | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Tentativa de cache do [modo kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) para obtenção de respostas com cabeçalhos qualificados. A resposta pode não incluir `Set-Cookie`, `Vary` ou cabeçalhos `Pragma`. Ela deve incluir um cabeçalho `Cache-Control` que seja `public` e um valor `shared-max-age` ou `max-age`, ou um cabeçalho `Expires`. | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | O número máximo de aceitações simultâneas. | 5 &times; [Ambiente.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | O número máximo de conexões simultâneas a serem aceitas. Usar `-1` como infinito. Usar `null` a fim de usar a configuração que abranja toda máquina do registro. | `null`<br>(ilimitado) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Confira a seção <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 de bytes<br>(28,6 MB) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | O número máximo de solicitações que podem ser colocadas na fila. | 1000 |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indica se as gravações do corpo da resposta que falham quando o cliente se desconecta devem gerar exceções ou serem concluídas normalmente. | `false`<br>(concluir normalmente) |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expor a configuração <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> do HTTP.sys, que também pode ser configurado no Registro. Siga os links de API para saber mais sobre cada configuração, inclusive os valores padrão:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; O tempo permitido para que a API do servidor HTTP esvazie o corpo da entidade em uma conexão Keep-Alive.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; O tempo permitido para a chegada do corpo da entidade de solicitação.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; O tempo permitido para que a API do servidor HTTP analise o cabeçalho da solicitação.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; O tempo permitido para uma conexão ociosa.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; A taxa de envio mínima para a resposta.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; O tempo permitido para que a solicitação permaneça na fila de solicitações até o aplicativo coletá-la.</li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Especifique o <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> para registrar com o HTTP.sys. A mais útil é [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), que é usada para adicionar um prefixo à coleção. É possível modificá-las a qualquer momento antes de descartar o ouvinte. |  |

::: moniker-end

   <a name="maxrequestbodysize"></a>

   **MaxRequestBodySize**

   O tamanho máximo permitido em bytes para todos os corpos de solicitação. Quando é definido como `null`, o tamanho máximo do corpo da solicitação é ilimitado. Esse limite não afeta as conexões atualizadas que são sempre ilimitadas.

   O método recomendado para substituir o limite em um aplicativo ASP.NET Core MVC para um único `IActionResult` é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Uma exceção é gerada quando o aplicativo tenta configurar o limite de uma solicitação, depois que o aplicativo inicia a leitura da solicitação. É possível usar uma propriedade `IsReadOnly` para indicar se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.

   Se o aplicativo precisar substituir <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> mediante solicitação, use o <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Quando usar o Visual Studio, verifique se que o aplicativo está configurado para executar o IIS ou IIS Express.

   No Visual Studio, o perfil de inicialização padrão destina-se ao IIS Express. Para executar o projeto como um aplicativo de console, altere manualmente o perfil selecionado, conforme mostrado na captura de tela a seguir:

   ![Selecionar o perfil do aplicativo de console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Configurar o Windows Server

1. Determine as portas que serão abertas para o aplicativo e use o [Firewall do Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou o cmdlet do PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) para abrir as portas de firewall e permitir que o tráfego chegue até o HTTP.sys. Nos seguintes comandos e configuração de aplicativo, a porta 443 é usada.

1. Ao implantar em uma VM do Azure, abra as portas no [Grupo de Segurança de Rede](/azure/virtual-machines/windows/nsg-quickstart-portal). Nos seguintes comandos e configuração de aplicativo, a porta 443 é usada.

1. Obtenha e instale os certificados X.509, se precisar.

   No Windows, crie certificados autoassinados, usando o [cmdlet do PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Para ver um exemplo sem suporte, confira [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Instale certificados autoassinados ou assinados pela AC no repositório **Computador Local** > **Pessoal** do servidor.

1. Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd), instale o .NET Core, o .NET Framework ou ambos (caso o aplicativo .NET Core seja direcionado ao .NET Framework).

   * **.NET Core** &ndash; Se o aplicativo exigir o .NET Core, obtenha e execute o instalador do **Tempo de Execução do .NET Core** em [Downloads do .NET Core](https://dotnet.microsoft.com/download). Não instale o SDK completo no servidor.
   * **.NET framework** &ndash; Se o aplicativo exigir o .NET Framework, confira o [Guia de instalação do .NET Framework](/dotnet/framework/install/). Instale o .NET Framework necessário. O instalador do .NET Framework mais recente está disponível na página [Downloads do .NET Core](https://dotnet.microsoft.com/download).

   Se o aplicativo for uma [implantação autocontida](/dotnet/core/deploying/#framework-dependent-deployments-scd), ele incluirá o tempo de execução em sua implantação. Nenhuma instalação do framework é necessária no servidor.

1. Configure URLs e portas no aplicativo.

   Por padrão, o ASP.NET Core é associado a `http://localhost:5000`. Para configurar portas e prefixos de URL, as opções incluem:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * O argumento de linha de comando `urls`
   * A variável de ambiente `ASPNETCORE_URLS`
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   O exemplo de código a seguir mostra como usar <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> com o endereço IP local do servidor `10.0.0.4` na porta 443:

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=6)]

   Uma vantagem de usar `UrlPrefixes` é que uma mensagem de erro é gerada imediatamente no caso de prefixos formatados de forma incorreta.

   As configurações de `UrlPrefixes` substituem as configurações `UseUrls`/`urls`/`ASPNETCORE_URLS`. Portanto, uma vantagem de usar `UseUrls`, `urls` e a variável de ambiente `ASPNETCORE_URLS` é que fica mais fácil alternar entre o Kestrel e o HTTP.sys.

   O HTTP.sys usa os [formatos de cadeia de caracteres UrlPrefix da API do Servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas. Associações de curinga de nível superior criam vulnerabilidades de segurança no aplicativo. Isso se aplica a curingas fortes e fracos. Use nomes de host explícitos ou endereços IP em vez de curingas. Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não serão um risco à segurança se você controlar todo o domínio pai (ao contrário de `*.com`, o qual é vulnerável). Para saber mais, confira [RFC 7230: Seção 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Pré-registre os prefixos de URL no servidor.

   O *netsh.exe* é a ferramenta interna destinada a configurar o HTTP.sys. Com o *netsh.exe*, é possível reservar prefixos de URL e atribuir certificados X.509. A ferramenta exige privilégios de administrador.

   Use a ferramenta *netsh.exe* para registrar as URLs do aplicativo:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>` &ndash; A URL (Uniform Resource Locator) totalmente qualificada. Não use uma associação de curinga. Use um nome de host válido ou o endereço IP local. *A URL deve incluir uma barra à direita.*
   * `<USER>` &ndash; Especifica o nome de usuário ou do grupo de usuários.

   No exemplo a seguir, o endereço IP local do servidor é `10.0.0.4`:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Quando uma URL é registrada, a ferramenta responde com `URL reservation successfully added`.

   Para excluir uma URL registrada, use o comando `delete urlacl`:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Registre certificados X.509 no servidor.

   Use a ferramenta *netsh.exe* para registrar certificados do aplicativo:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>` &ndash; Especifica o endereço IP local para a associação. Não use uma associação de curinga. Use um endereço IP válido.
   * `<PORT>` &ndash; Especifica a porta da associação.
   * `<THUMBPRINT>` &ndash; A impressão digital do certificado X.509.
   * `<GUID>` &ndash; Um GUID gerado pelo desenvolvedor para representar o aplicativo para fins informativos.

   Para fins de referência, armazene o GUID no aplicativo como uma marca de pacote:

   * No Visual Studio:
     * Abra as propriedades do projeto do aplicativo, clicando com o botão direito do mouse no aplicativo no **Gerenciador de Soluções** e selecionando **Propriedades**.
     * Selecione a guia **Pacote**.
     * Insira o GUID que você criou no campo **Marcas**.
   * Quando não estiver usando o Visual Studio:
     * Abra o arquivo de projeto do aplicativo.
     * Adicione uma propriedade `<PackageTags>` a um `<PropertyGroup>` novo ou existente com o GUID que você criou:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   No exemplo a seguir:

   * O endereço IP local do servidor é `10.0.0.4`.
   * Um gerador GUID aleatório online fornece o valor `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Quando um certificado é registrado, a ferramenta responde com `SSL Certificate successfully added`.

   Para excluir um registro de certificado, use o comando `delete sslcert`:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Documentação de referência do *netsh.exe*:

   * [Comandos do Netsh para o protocolo HTTP](https://technet.microsoft.com/library/cc725882.aspx)
   * [Cadeias de caracteres de UrlPrefix](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Execute o aplicativo.

   Não é necessário ter privilégios de administrador para executar o aplicativo ao associar ao localhost usando HTTP (não HTTPS) com um número de porta maior do que 1024. Para outras configurações (por exemplo, usar um endereço IP local ou associação à porta 443), execute o aplicativo com privilégios de administrador.

   O aplicativo responde no endereço IP público do servidor. Neste exemplo, o servidor é acessado pela Internet como seu endereço IP público de `104.214.79.47`.

   Um certificado de desenvolvimento é usado neste exemplo. A página é carregada com segurança após ignorar o aviso de certificado não confiável do navegador.

   ![Janela do navegador mostrando a página de Índice do aplicativo carregada](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

Para aplicativos hospedados pelo HTTP.sys que interagem com solicitações da Internet ou de uma rede corporativa, podem ser necessárias configurações adicionais ao hospedar atrás de balanceadores de carga e de servidores proxy. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Recursos adicionais

* [Habilitar a autenticação do Windows com HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API do servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [Repositório aspnet/HttpSysServer do GitHub (código-fonte)](https://github.com/aspnet/HttpSysServer/)
* [O host](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
