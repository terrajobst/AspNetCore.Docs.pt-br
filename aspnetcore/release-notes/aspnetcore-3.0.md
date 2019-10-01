---
title: O que há de novo no ASP.NET Core 3,0
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: aspnetcore-3.0
ms.openlocfilehash: a32307f6de01e03e548cbde2c38c9469db74b506
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688039"
---
# <a name="whats-new-in-aspnet-core-30"></a>O que há de novo no ASP.NET Core 3,0

Este artigo destaca as alterações mais significativas no ASP.NET Core 3,0 com links para a documentação relevante.

## <a name="blazor"></a>Blazor

O mais novo é uma estrutura em ASP.NET Core para a criação de interface do usuário da Web interativa do lado do cliente com o .NET:

* Crie interfaces do usuário interativas avançadas usando C# em vez de JavaScript.
* Compartilhe a lógica de aplicativo do lado do cliente e do servidor gravada no .NET.
* Renderize a interface do usuário, como HTML e CSS para suporte amplo de navegadores, incluindo navegadores móveis.

Cenários com suporte da estrutura de mais incrivelmente:

* Componentes de interface do usuário reutilizáveis (componentes do Razor)
* Roteamento do lado do cliente
* Layouts de componente
* Suporte para injeção de dependência
* Formulários e validação
* Criar bibliotecas de componentes com bibliotecas de classes do Razor
* Interoperabilidade do JavaScript

Para obter mais informações, consulte <xref:blazor/index>.

### <a name="blazor-server"></a>Servidor Blazor

Os componentes Blazor desvinculam a lógica de renderização do componente da forma como as atualizações da interface do usuário são aplicadas. O servidor mais incrivelmente dá suporte para hospedar componentes do Razor no servidor em um aplicativo ASP.NET Core. As atualizações da interface do usuário são tratadas por uma conexão SignalR. Há suporte para o servidor mais incrivelmente no ASP.NET Core 3,0.

### <a name="blazor-webassembly-preview"></a>Webassembly mais incrivelmente (visualização)

Os aplicativos mais fáceis também podem ser executados diretamente no navegador usando um tempo de execução .NET baseado em Webassembly. Webassembly de mais de-teste está em versão prévia e *sem* suporte no ASP.NET Core 3,0. O Webassembly mais incrivelmente terá suporte em uma versão futura do ASP.NET Core.

### <a name="razor-components"></a>Componentes do Razor

Os aplicativos mais elaborados são criados a partir de componentes. Os componentes são partes independentes da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário. Os componentes são classes normais do .NET que definem a lógica de renderização da interface do usuário e manipuladores de eventos do lado do cliente. Você pode criar aplicativos Web interativos sofisticados sem JavaScript.

Os componentes no mais claro normalmente são criados usando sintaxe Razor, uma mistura natural de HTML e C#. Os componentes do Razor são semelhantes às exibições Razor Pages e MVC, pois ambos usam o Razor. Ao contrário de páginas e exibições, que se baseiam em um modelo de solicitação-resposta, os componentes são usados especificamente para manipular a composição da interface do usuário.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* É uma estrutura popular de RPC de alto desempenho (chamada de procedimento remoto).
* Oferece uma abordagem de contrato conceituada para o desenvolvimento de API.
* O usa tecnologias modernas, como:

  * HTTP/2 para transporte.
  * Buffers de protocolo como linguagem de descrição de interface.
  * Formato de serialização binária.
* Fornece recursos como:

  * Autenticação
  * Fluxo bidirecional e controle de fluxo.
  * Cancelamento e tempos limite.

a funcionalidade gRPC no ASP.NET Core 3,0 inclui:

* [Grpc. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; uma estrutura de ASP.NET Core para hospedar serviços Grpc. o gRPC em ASP.NET Core integra-se com recursos de ASP.NET Core padrão, como registro em log, injeção de dependência (DI), autenticação e autorização.
* [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; um cliente Grpc para .NET Core que se baseia no conhecido `HttpClient`.
* Integração de cliente do [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; Grpc com `HttpClientFactory`.

Para obter mais informações, consulte <xref:grpc/index>.

## <a name="signalr"></a>SignalR

Consulte [atualizar o código do sinalizador](xref:migration/22-to-30#signalr) para obter instruções de migração. O signalr agora `System.Text.Json` usa para serializar/desserializar mensagens JSON. Consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) para obter instruções para restaurar `Newtonsoft.Json`o serializador baseado em.

Nos clientes JavaScript e .NET para Signalr, foi adicionado suporte para reconexão automática. Por padrão, o cliente tenta se reconectar imediatamente e tentar novamente após 2, 10 e 30 segundos, se necessário. Se o cliente reconectar-se com êxito, ele receberá uma nova ID de conexão. A reconexão automática é opcional:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Os intervalos de reconexão podem ser especificados passando uma matriz de durações baseadas em milissegundos:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Uma implementação personalizada pode ser passada para controle total dos intervalos de reconexão.

Se a reconexão falhar após o último intervalo de reconexão:

* O cliente considera que a conexão está offline.
* O cliente para de tentar se reconectar.

Durante as tentativas de reconexão, atualize a interface de usuário do aplicativo para notificar o usuário de que a reconexão está sendo tentada.

Para fornecer comentários da interface do usuário quando a conexão é interrompida, a API de cliente do Signalr foi expandida para incluir os seguintes manipuladores de eventos:

* `onreconnecting`:  Dá aos desenvolvedores uma oportunidade de desabilitar a interface do usuário ou de permitir que os usuários saibam que o aplicativo está offline.
* `onreconnected`: Dá aos desenvolvedores uma oportunidade de atualizar a interface do usuário quando a conexão é restabelecida.

O código a seguir `onreconnecting` usa para atualizar a interface do usuário ao tentar se conectar:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

O código a seguir `onreconnected` usa para atualizar a interface do usuário na conexão:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

O signalr 3,0 e posterior fornece um recurso personalizado para manipuladores de autorização quando um método de Hub requer autorização. O recurso é uma instância do `HubInvocationContext`. O `HubInvocationContext` inclui:

* `HubCallerContext`
* Nome do método de Hub que está sendo invocado.
* Argumentos para o método de Hub.

Considere o exemplo a seguir de um aplicativo de sala de chat que permite a entrada de várias organizações por meio de Azure Active Directory. Qualquer pessoa com um conta Microsoft pode entrar no chat, mas somente os membros da organização proprietária podem proibir os usuários ou exibir os históricos de chat dos usuários. O aplicativo pode restringir determinada funcionalidade de usuários específicos.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

No código anterior, `DomainRestrictedRequirement` serve como um personalizado. `IAuthorizationRequirement` Como o `HubInvocationContext` parâmetro de recurso está sendo passado, a lógica interna pode:

* Inspecione o contexto no qual o Hub está sendo chamado.
* Tome decisões sobre como permitir que o usuário execute métodos de Hub individuais.

Os métodos de Hub individuais podem ser decorados com o nome da política que o código verifica em tempo de execução. À medida que os clientes tentam chamar métodos de `DomainRestrictedRequirement` Hub individuais, o manipulador é executado e controla o acesso aos métodos. Com base na maneira como `DomainRestrictedRequirement` os controles são acessados:

* Todos os usuários conectados podem chamar o `SendMessage` método.
* Somente os usuários que fizeram logon com um `@jabbr.net` endereço de email podem exibir os históricos dos usuários.
* O `bob42@jabbr.net` só pode proibir usuários da sala de chat.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

A criação `DomainRestricted` da política pode envolver:

* Em *Startup.cs*, adicionando a nova política.
* Forneça o requisito `DomainRestrictedRequirement` personalizado como um parâmetro.
* Registrando `DomainRestricted` com o middleware de autorização.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Os hubs de sinalização usam [Roteamento de ponto de extremidade](xref:fundamentals/routing). A conexão do Hub do signalr foi feita explicitamente anteriormente:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Na versão anterior, os desenvolvedores precisavam conectar controladores, páginas Razor e hubs em uma variedade de locais diferentes. A conexão explícita resulta em uma série de segmentos de roteamento quase idênticos:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Os hubs 3,0 do signalr podem ser roteados por meio do roteamento de ponto de extremidade. Com o roteamento de ponto de extremidade, normalmente todo o `UseRouting`roteamento pode ser configurado em:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core Signaler 3,0 adicionado:

Streaming de cliente para servidor. Com o streaming de cliente para servidor, os métodos do lado do servidor podem ter instâncias de `IAsyncEnumerable<T>` um `ChannelReader<T>`ou. No exemplo a C# seguir, o `UploadStream` método no Hub receberá um fluxo de cadeias de caracteres do cliente:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Os aplicativos cliente .net podem passar uma `IAsyncEnumerable<T>` instância `ChannelReader<T>` ou `UploadStream` como o `stream` argumento do método de Hub acima.

Depois que `for` o loop for concluído e a função local sair, a conclusão do fluxo será enviada:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Os aplicativos cliente JavaScript usam o `Subject` signalr (ou um [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) para `stream` `UploadStream` o argumento do método de Hub acima.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

O código JavaScript pode usar o `subject.next` método para lidar com cadeias de caracteres à medida que elas são capturadas e prontas para serem enviadas ao servidor.

```javascript
subject.next("example");
subject.complete();
```

Usando um código como os dois trechos anteriores, as experiências de streaming em tempo real podem ser criadas.

### <a name="new-json-serialization"></a>Nova serialização JSON

O ASP.NET Core 3,0 agora <xref:System.Text.Json> usa por padrão para serialização JSON:

* Lê e grava JSON de forma assíncrona.
* É otimizado para texto UTF-8.
* Geralmente um desempenho maior `Newtonsoft.Json`do que o.

Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [Adicionar suporte ao formato JSON baseado em Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Novas diretivas do Razor

A lista a seguir contém novas diretivas do Razor:

* [@attribute](xref:mvc/views/razor#attribute)&ndash; A`@attribute` diretiva aplica o atributo fornecido à classe da página ou exibição gerada. Por exemplo: `@attribute [Authorize]`.
* [@implements](xref:mvc/views/razor#implements)&ndash; A`@implements` diretiva implementa uma interface para a classe gerada. Por exemplo: `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>O IdentityServer4 dá suporte à autenticação e autorização para APIs Web e SPAs

[IdentityServer4](https://identityserver.io) é uma estrutura de OpenID Connect e OAuth 2,0 para ASP.NET Core 3,0. O IdentityServer4 habilita os seguintes recursos de segurança:

* AaaS (autenticação como serviço)
* Logon único/logoff (SSO) em vários tipos de aplicativos
* Controle de acesso para APIs
* Gateway de Federação

Para obter mais informações, consulte [Bem-vindo ao IdentityServer4](http://docs.identityserver.io/en/latest/index.html).

## <a name="certificate-and-kerberos-authentication"></a>Autenticação de certificado e Kerberos

A autenticação de certificado requer:

* Configurando o servidor para aceitar certificados.
* Adicionar o middleware de autenticação no `Startup.Configure`.
* Adicionando o serviço de autenticação de `Startup.ConfigureServices`certificado no.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

As opções de autenticação de certificado incluem a capacidade de:

* Aceite certificados autoassinados.
* Verificar revogação de certificado.
* Verifique se o certificado proffered tem os sinalizadores de uso corretos.

Uma entidade de usuário padrão é construída a partir das propriedades do certificado. A entidade de usuário contém um evento que permite complementar ou substituir a entidade de segurança. Para obter mais informações, consulte <xref:security/authentication/certauth>.

A [autenticação do Windows](/windows-server/security/windows-authentication/windows-authentication-overview) foi estendida para Linux e MacOS. Em versões anteriores, a autenticação do Windows era limitada ao [IIS](xref:host-and-deploy/iis/index) e à [https](xref:fundamentals/servers/httpsys). No ASP.NET Core 3,0, o [Kestrel](xref:fundamentals/servers/kestrel) tem a capacidade de usar Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)e [NTLM no Windows](/windows-server/security/kerberos/ntlm-overview), Linux e MacOS para hosts ingressados no domínio do Windows. O suporte do Kestrel desses esquemas de autenticação é fornecido pelo pacote [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) . Assim como acontece com os outros serviços de autenticação, configure o aplicativo de autenticação de todo e configure o serviço:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Requisitos do host:

* Os hosts do Windows devem ter SPNs ( [nomes da entidade de serviço](/windows/win32/ad/service-principal-names) ) adicionados à conta de usuário que hospeda o aplicativo.
* Os computadores Linux e macOS devem ser ingressados no domínio.
  * Os SPNs devem ser criados para o processo da Web.
  * Os [arquivos keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) devem ser gerados e configurados no computador host.

Para obter mais informações, consulte <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Alterações de modelo

Os modelos de interface do usuário da Web (Razor Pages, MVC com controlador e exibições) têm o seguinte removido:

* A interface do usuário de consentimento do cookie não está mais incluída. Para habilitar o recurso de consentimento de cookie em um aplicativo gerado pelo modelo ASP.NET Core <xref:security/gdpr>3,0, consulte.
* Os scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar CDNs. Para obter mais informações, consulte [scripts e ativos estáticos relacionados agora são referenciados como arquivos locais em vez de usar o CDNs com base no ambiente atual (ASPNET/AspNetCore. Docs #14350)](https://github.com/aspnet/AspNetCore.Docs/issues/14350).

O modelo angular atualizado para usar o angular 8.

O modelo de biblioteca de classes Razor (RCL) usa como padrão o desenvolvimento de componentes Razor por padrão. Uma nova opção de modelo no Visual Studio fornece suporte de modelo para páginas e exibições. Ao criar um RCL a partir do modelo em um shell de comando, `-support-pages-and-views` passe a`dotnet new razorclasslib -support-pages-and-views`opção ().

## <a name="generic-host"></a>Host Genérico

Os modelos ASP.NET Core 3,0 usam <xref:fundamentals/host/generic-host>. Versões anteriores usadas <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. O uso do host genérico do .NET<xref:Microsoft.Extensions.Hosting.HostBuilder>Core () fornece uma melhor integração de aplicativos ASP.NET Core com outros cenários de servidor que não são específicos da Web. Para obter mais informações, consulte [HostBuilder substitui WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Configuração do host

Antes do lançamento do ASP.NET Core 3,0, as variáveis de ambiente prefixadas com `ASPNETCORE_` foram carregadas para a configuração de host do host da Web. No 3,0, `AddEnvironmentVariables` é usado para carregar variáveis de ambiente prefixadas com `DOTNET_` para a `CreateDefaultBuilder`configuração de host com.

### <a name="changes-to-startup-contructor-injection"></a>Alterações na injeção de construtor de inicialização

O host genérico só dá suporte aos seguintes tipos `Startup` para injeção de construtor:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Todos os serviços ainda podem ser injetados diretamente como argumentos para `Startup.Configure` o método. Para obter mais informações, consulte [injeção de construtor de inicialização de restrict de host genérico (ASPNET/comunicados #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* A configuração do Kestrel foi atualizada para a migração para o host genérico. No 3,0, o Kestrel é configurado no construtor de hosts da `ConfigureWebHostDefaults`Web fornecido pelo.
* Os adaptadores de conexão foram removidos do Kestrel e substituídos pelo middleware de conexão, que é semelhante ao middleware HTTP no pipeline de ASP.NET Core, mas para conexões de nível inferior.
* A camada de transporte Kestrel foi exposta como uma interface pública no `Connections.Abstractions`.
* A ambiguidade entre cabeçalhos e trailers foi resolvida movendo os cabeçalhos à direita para uma nova coleção.
* APIs de e/s síncronas, `HttpRequest.Body.Read`como, são uma fonte comum de privação de thread que leva a falhas de aplicativo. No 3,0, `AllowSynchronousIO` é desabilitado por padrão.

Para obter mais informações, consulte <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 habilitado por padrão

O HTTP/2 é habilitado por padrão em Kestrel para pontos de extremidade HTTPS. O suporte a HTTP/2 para IIS ou HTTP. sys é habilitado quando há suporte para o sistema operacional.

## <a name="eventcounters-on-request"></a>EventCounters na solicitação

A hospedagem EventSource, `Microsoft.AspNetCore.Hosting`, emite os seguintes novos <xref:System.Diagnostics.Tracing.EventCounter> tipos relacionados a solicitações de entrada:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Roteamento de ponto de extremidade

O roteamento de ponto de extremidade, que permite que as estruturas (por exemplo, MVC) funcionem bem com o middleware, seja aprimorado:

* A ordem do middleware e dos pontos de extremidade é configurável no pipeline de processamento de solicitação `Startup.Configure`do.
* Os pontos de extremidade e o middleware compõem-se bem com outras tecnologias baseadas em ASP.NET Core, como verificações de integridade.
* Os pontos de extremidade podem implementar uma política, como CORS ou autorização, tanto no middleware quanto no MVC.
* Filtros e atributos podem ser colocados em métodos em controladores.

Para obter mais informações, consulte <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Verificações de integridade

As verificações de integridade usam o roteamento de ponto de extremidade com o host genérico. No `Startup.Configure`, chame `MapHealthChecks` no construtor de ponto de extremidade com a URL do ponto de extremidade ou o caminho relativo:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Os pontos de extremidade de verificações de integridade podem:

* Especifique um ou mais hosts/portas permitidos.
* Exigir autorização.
* Exigir CORS.

Para obter mais informações, confira os seguintes artigos:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Pipes em HttpContext

Agora é possível ler o corpo da solicitação e gravar o corpo da resposta usando a <xref:System.IO.Pipelines> API. O <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`a propriedade fornece <xref:System.IO.Pipelines.PipeReader> um que pode ser usado para ler o corpo da solicitação. O <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`a propriedade fornece <xref:System.IO.Pipelines.PipeWriter> um que pode ser usado para gravar o corpo da resposta. `HttpRequest.BodyReader`é um análogo do `HttpRequest.Body` fluxo. `HttpResponse.BodyWriter`é um análogo do `HttpResponse.Body` fluxo.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Relatório de erros aprimorado no IIS

Os erros de inicialização ao hospedar aplicativos ASP.NET Core no IIS agora produzem dados de diagnóstico mais avançados. Esses erros são relatados para o log de eventos do Windows com rastreamentos de pilha onde for aplicável. Além disso, todos os avisos, erros e exceções sem tratamento são registrados no log de eventos do Windows.

## <a name="worker-service-and-worker-sdk"></a>Serviço de trabalho e SDK de trabalho

O .NET Core 3,0 apresenta o novo modelo de aplicativo do serviço de trabalho. Este modelo fornece um ponto de partida para escrever serviços de longa execução no .NET Core.

Para obter mais informações, consulte:

* [Trabalhos do .NET Core como serviços do Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Melhorias de middleware de cabeçalhos encaminhados

Em versões anteriores do ASP.NET Core, chamando <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> e <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> eram problemáticas quando implantamos em um Linux do Azure ou atrás de qualquer proxy reverso que não seja o IIS. A correção para versões anteriores está documentada em [encaminhar o esquema para proxies inversos do Linux e não IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Esse cenário é corrigido no ASP.NET Core 3,0. O host habilita o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) quando a `ASPNETCORE_FORWARDEDHEADERS_ENABLED` variável de ambiente é definida `true`como. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`é definido como `true` em nossas imagens de contêiner.

## <a name="performance-improvements"></a>Melhorias de desempenho

O ASP.NET Core 3,0 inclui muitas melhorias que reduzem o uso de memória e melhoram a taxa de transferência:

* Redução no uso de memória ao usar o contêiner de injeção de dependência interna para serviços com escopo.
* Redução nas alocações em toda a estrutura, incluindo cenários de middleware e roteamento.
* Redução no uso de memória para conexões WebSocket.
* Melhorias de taxa de transferência e redução de memória para conexões HTTPS.
* Novo serializador JSON otimizado e totalmente assíncrono.
* Redução no uso de memória e melhorias de taxa de transferência na análise de formulário.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>O ASP.NET Core 3,0 só é executado no .NET Core 3,0

A partir de ASP.NET Core 3,0, .NET Framework não é mais uma estrutura de destino com suporte. Os projetos destinados a .NET Framework podem continuar de uma maneira totalmente compatível usando a [versão LTS do .NET Core 2,1](https://www.microsoft.com/net/download/dotnet-core/2.1). A maioria dos pacotes relacionados ao ASP.NET Core 2.1. x terá suporte indefinidamente, além do período de LTS de 3 anos para o .NET Core 2,1.

Para obter informações de migração, consulte [portar seu código do .NET Framework para o .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Usar a estrutura compartilhada ASP.NET Core

A estrutura compartilhada ASP.NET Core 3,0, contida no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), não requer mais um elemento explícito `<PackageReference />` no arquivo de projeto. A estrutura compartilhada é automaticamente referenciada ao usar `Microsoft.NET.Sdk.Web` o SDK no arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Assemblies removidos da estrutura compartilhada do ASP.NET Core

Os assemblies mais notáveis removidos da estrutura compartilhada ASP.NET Core 3,0 são:

* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net). Para adicionar o Json.NET ao ASP.NET Core 3,0, consulte [Adicionar suporte ao formato JSON baseado em Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3,0 apresenta `System.Text.Json` para leitura e gravação de JSON. Para obter mais informações, consulte [nova SERIALIZAÇÃO JSON](#new-json-serialization) neste documento.
* [Entity Framework Core](/ef/core/)

Para obter uma lista completa de assemblies removidos da estrutura compartilhada, consulte [assemblies sendo removidos do Microsoft. AspNetCore. App 3,0](https://github.com/aspnet/AspNetCore/issues/3755). Para obter mais informações sobre a motivação para essa alteração, consulte [alterações recentes em Microsoft. AspNetCore. app em 3,0](https://github.com/aspnet/Announcements/issues/325) e [uma primeira análise sobre as alterações que chegam no ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
