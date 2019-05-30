---
title: Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga
author: guardrex
description: Saiba mais sobre a configuração para aplicativos hospedados por trás de servidores proxy e balanceadores de carga, o que muitas vezes oculta informações de solicitação importantes.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/24/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 2423b5bed760ad879d1c47c5e64b0f815b50397e
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251391"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Configure o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga

Por [Luke Latham](https://github.com/guardrex) e [Chris Ross](https://github.com/Tratcher)

Na configuração recomendada para o ASP.NET Core, o aplicativo é hospedado usando IIS/Módulo do ASP.NET Core, Nginx ou Apache. Servidores proxy, balanceadores de carga e outros dispositivos de rede geralmente ocultam informações sobre a solicitação antes de ela alcançar o aplicativo:

* Quando solicitações HTTPS são passadas por proxy por HTTP, o esquema original (HTTPS) é perdido e deve ser encaminhado em um cabeçalho.
* Devido a um aplicativo receber uma solicitação do proxy e não de sua origem verdadeira na Internet ou rede corporativa, o endereço IP do cliente originador também deve ser encaminhado em um cabeçalho.

Essas informações podem ser importantes no processamento de solicitações, por exemplo, em redirecionamentos, autenticação, geração de link, avaliação de política e localização geográfica do cliente.

## <a name="forwarded-headers"></a>Cabeçalhos encaminhados

Por convenção, os proxies encaminham informações em cabeçalhos HTTP.

| Cabeçalho | Descrição |
| ------ | ----------- |
| X-Forwarded-For | Contém informações sobre o cliente que iniciou a solicitação e os proxies subsequentes em uma cadeia de proxies. Esse parâmetro pode conter endereços IP (e, opcionalmente, os números de porta). Em uma cadeia de servidores proxy, o primeiro parâmetro indica o cliente em que a solicitação foi feita pela primeira vez. Depois, vêm os identificadores de proxy subsequentes. O último proxy na cadeia não está na lista de parâmetros. O endereço IP do último proxy (e opcionalmente um número da porta) estão disponíveis como o endereço IP remoto na camada de transporte. |
| X-Forwarded-Proto | O valor do esquema de origem (HTTP/HTTPS). O valor também pode ser uma lista de esquemas se a solicitação percorreu vários proxies. |
| X-Forwarded-Host | O valor original do campo de cabeçalho do host. Normalmente, os proxies não modificam o cabeçalho do host. Veja [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) para obter informações sobre uma vulnerabilidade de elevação de privilégios que afeta os sistemas em que o proxy não valida ou restringe cabeçalhos de Host a valores válidos conhecidos. |

O middleware de cabeçalhos encaminhados, do pacote [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), lê esses cabeçalhos e preenche os campos associados em <xref:Microsoft.AspNetCore.Http.HttpContext>.

As atualizações de middleware:

* [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-For`. Configurações adicionais influenciam o modo como o middleware define `RemoteIpAddress`. Para obter detalhes, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Proto`.
* [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Definido usando o valor do cabeçalho `X-Forwarded-Host`.

As [configurações padrão](#forwarded-headers-middleware-options) de middleware de cabeçalhos encaminhados podem ser definidas. As configurações padrão são:

* Há apenas *um proxy* entre o aplicativo e a origem das solicitações.
* Somente os endereços de loopback são configurados para proxies conhecidos e redes conhecidas.
* Os cabeçalhos encaminhados são nomeados `X-Forwarded-For` e `X-Forwarded-Proto`.

Nem todos os dispositivos de rede adicionam os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` sem configuração adicional. Consulte as diretrizes do fabricante do dispositivo se as solicitações de proxies não contiverem esses cabeçalhos quando atingirem o aplicativo. Se o dispositivo usar nomes de cabeçalho diferente de `X-Forwarded-For` e `X-Forwarded-Proto`, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo dispositivo. Para obter mais informações, consulte [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) (Opções de middleware de cabeçalhos encaminhados) e [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names) (Configuração de um proxy que usa diferentes nomes de cabeçalho).

## <a name="iisiis-express-and-aspnet-core-module"></a>O IIS/IIS Express e o Módulo do ASP.NET Core

O Middleware de Cabeçalhos Encaminhados é habilitado por padrão pelo [Middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando o aplicativo é hospedado [fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model) atrás do IIS e do Módulo do ASP.NET Core. O middleware de cabeçalhos encaminhados é ativado para ser executado primeiro no pipeline de middleware, com uma configuração restrita específica para o Módulo do ASP.NET Core devido a questões de confiança com cabeçalhos encaminhados (por exemplo, [falsificação de IP](https://www.iplocation.net/ip-spoofing)). O middleware está configurado para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` e é restrito a um proxy de localhost único. Se configuração adicional for necessária, veja as [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Outros cenários de servidor proxy e balanceador de carga

Além de usar a [Integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) quando hospedar [fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model), o Middleware de cabeçalhos encaminhados não é habilitado por padrão. O middleware de cabeçalhos encaminhados deve ser habilitado para um aplicativo para processar cabeçalhos encaminhados com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>. Após a habilitação do middleware, se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado para o middleware, o [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) padrão será [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Configure o middleware com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`. Invocar o método <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> no `Startup.Configure` antes de chamar outro middleware:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> Se nenhum <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> for especificado no `Startup.ConfigureServices` ou diretamente no método de extensão com <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, os cabeçalhos padrão para encaminhar serão [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). A propriedade <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> deve ser configurada com os cabeçalhos para encaminhar.

## <a name="nginx-configuration"></a>Configuração de Nginx

Para encaminhar os cabeçalhos `X-Forwarded-For` e `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-nginx#configure-nginx>. Confira mais informações em [NGINX: como usar o cabeçalho Encaminhado](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Configuração do Apache

`X-Forwarded-For` é adicionado automaticamente (veja [mod_proxy do módulo do Apache: cabeçalhos de solicitação de proxy reverso](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Para obter informações sobre como encaminhar o cabeçalho `X-Forwarded-Proto`, consulte <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Opções de middleware de cabeçalhos encaminhados

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> controla o comportamento do middleware de cabeçalhos encaminhados. O seguinte exemplo altera os valores padrão:

* Limite o número de entradas nos cabeçalhos encaminhados a `2`.
* Adicione um endereço de proxy conhecido de `127.0.10.1`.
* Altere o nome do cabeçalho encaminhado do padrão `X-Forwarded-For` para `X-Forwarded-For-My-Custom-Header-Name`.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Opção | Descrição |
| ------ | ----------- |
| AllowedHosts | Restringe os hosts com o cabeçalho `X-Forwarded-Host` para os valores fornecidos.<ul><li>Os valores são comparados usando ordinal-ignore-case.</li><li>Os número de porta devem ser excluídos.</li><li>Se a lista estiver vazia, todos os hosts serão permitidos.</li><li>Um curinga de nível superior `*` permite todos os hosts não vazios.</li><li>Curingas de subdomínio são permitidos, mas não correspondem ao domínio raiz. Por exemplo, `*.contoso.com` corresponde o subdomínio `foo.contoso.com`, mas não ao domínio raiz `contoso.com`.</li><li>Nomes do host Unicode são permitidos, mas são convertidos em [Punycode](https://tools.ietf.org/html/rfc3492) para correspondência.</li><li>[Endereços IPv6](https://tools.ietf.org/html/rfc4291) devem incluir colchetes delimitadores e estar no [formato convencional](https://tools.ietf.org/html/rfc4291#section-2.2) (por exemplo, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). Endereços IPv6 não têm caso especial para verificar se há igualdade lógica entre formatos diferentes, e nenhuma canonicalização é executada.</li><li>Falha ao restringir os hosts permitidos pode permitir que um atacante falsifique links gerados pelo serviço.</li></ul>O valor padrão é um `IList<string>` vazio. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-For`, mas usa algum outro cabeçalho para encaminhar as informações.<br><br>O padrão é `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifica quais encaminhadores devem ser processados. Veja o [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) para a lista de campos aplicáveis. Os valores típicos atribuídos a essa propriedade são 'ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto'.<br><br>O valor padrão é [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Host`, mas usa algum outro cabeçalho para encaminhar as informações.<br><br>O padrão é `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Esta opção é usada quando o proxy/encaminhador não usa o cabeçalho `X-Forwarded-Proto`, mas usa algum outro cabeçalho para encaminhar as informações.<br><br>O padrão é `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limita o número de entradas nos cabeçalhos que são processados. Defina para `null` para desabilitar o limite, mas isso só deve ser feito se `KnownProxies` ou `KnownNetworks` estão configurados.<br><br>O padrão é 1. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Intervalos de endereços de redes conhecidas dos quais aceitar cabeçalhos encaminhados. Forneça os intervalos de IP usando notação de CIDR (Roteamento entre Domínios sem Classificação).<br><br>O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`). Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>O padrão é uma `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> que contém uma única entrada para `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Endereços de proxies conhecidos dos quais aceitar cabeçalhos encaminhados. Use `KnownProxies` especificar correspondências exatas de endereço IP.<br><br>O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1`). Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Para saber mais, veja a seção [Configuração de endereços IPv4 representados como endereços IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).<br><br>O padrão é uma `IList`\<<xref:System.Net.IPAddress>> que contém uma única entrada para `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>O padrão é `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>O padrão é `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use o cabeçalho especificado por essa propriedade, em vez de um especificado por [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>O padrão é `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Exigem o número de valores de cabeçalho a serem sincronizados entre os [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) sendo processados.<br><br>O padrão no ASP.NET Core 1.x é `true`. O padrão no ASP.NET Core 2.0 ou posterior é `false`. |

## <a name="scenarios-and-use-cases"></a>Cenários e casos de uso

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Quando não é possível adicionar cabeçalhos encaminhados e todas as solicitações são seguras

Em alguns casos, pode não ser possível adicionar cabeçalhos encaminhados para as solicitações passadas por proxy ao aplicativo. Se o proxy está impondo que todas as solicitações externas públicas sejam HTTPS, o esquema pode ser definido manualmente em `Startup.Configure` antes de usar qualquer tipo de middleware:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Esse código pode ser desabilitado com uma variável de ambiente ou outra definição de configuração em um ambiente de preparo ou de desenvolvimento.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Lidar com o caminho base e proxies que alteram o caminho da solicitação

Alguns proxies passam o caminho intacto, mas com um caminho base de aplicativo que deve ser removido para que o roteamento funcione corretamente. O middleware de [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) divide o caminho em [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) e o caminho base do aplicativo em [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Se `/foo` é o caminho base do aplicativo para um caminho de proxy passado como `/foo/api/1`, o middleware define `Request.PathBase` para `/foo` e `Request.Path` para `/api/1` com o seguinte comando:

```csharp
app.UsePathBase("/foo");
```

O caminho original e o caminho base são reaplicados quando o middleware é chamado novamente na ordem inversa. Para obter mais informações sobre o processamento de ordem de middleware, consulte <xref:fundamentals/middleware/index>.

Se o proxy cortar o caminho (por exemplo, encaminhando `/foo/api/1` para `/api/1`), corrija redirecionamentos e links definindo a propriedade [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) da solicitação:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Se o proxy estiver adicionando dados de caminho, descarte a parte do caminho para corrigir os redirecionamentos e links usando <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> e atribuindo à propriedade <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Configuração de um proxy que usa diferentes nomes de cabeçalho

Se o proxy não usar cabeçalhos nomeados `X-Forwarded-For` e `X-Forwarded-Proto` para encaminhar a porta/endereço do proxy e as informações de origem do esquema, defina as opções <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> e <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> para corresponder aos nomes de cabeçalho usados pelo proxy:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Configuração de endereços IPv4 representados como endereços IPv6

O servidor usa soquetes de modo duplo e os endereços IPv4 são fornecidos em um formato IPv6 (por exemplo, `10.0.0.1` no formato IPv4 representado no formato IPv6 como `::ffff:10.0.0.1` ou `::ffff:a00:1`). Confira [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Para determinar se este formato é obrigatório, veja [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

No exemplo a seguir, um endereço de rede que fornece cabeçalhos encaminhados é adicionado à lista `KnownNetworks` no formato IPv6.

Endereço IPv4: `10.11.12.1/8`

Conversão em endereço IPv6: `::ffff:10.11.12.1`  
Comprimento do prefixo convertido: 104

Você pode também fornecer o endereço no formato hexadecimal (`10.11.12.1`, representado no formato IPv6 como `::ffff:0a0b:0c01`). Quando converter um endereço IPv4 em IPv6, adicione 96 ao comprimento do prefixo CIDR (`8` no exemplo) para levar em conta o prefixo IPv6 adicional `::ffff:` (8 + 96 = 104). 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="troubleshoot"></a>Solução de problemas

Quando os cabeçalhos não são encaminhados conforme o esperado, habilite [registro em log](xref:fundamentals/logging/index). Se os logs não fornecerem informações suficientes para solucionar o problema, enumere os cabeçalhos de solicitação recebidos pelo servidor. Use middleware embutido para gravar cabeçalhos de solicitação para uma resposta do aplicativo ou para log dos cabeçalhos. 

Para gravar os cabeçalhos de resposta do aplicativo, coloque o seguinte middleware terminal embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`:

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

Você pode gravar em logs em vez de no corpo da resposta. A gravação em logs permite que o site funcione normalmente durante a depuração.

Para gravar em logs em vez de no corpo da resposta:

* Injete `ILogger<Startup>` na classe `Startup` conforme descrito em [Criar logs na inicialização](xref:fundamentals/logging/index#create-logs-in-startup).
* Coloque o seguinte middleware embutido imediatamente após a chamada para <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> em `Startup.Configure`.

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

Quando processado, os valores `X-Forwarded-{For|Proto|Host}` são movidos para `X-Original-{For|Proto|Host}`. Se há vários valores em um determinado cabeçalho, observe que o middleware de cabeçalhos encaminhados processa cabeçalhos na ordem inversa, da direita para esquerda. O `ForwardLimit` padrão é 1 (um), portanto, apenas o valor mais à direita dos cabeçalhos será processado, a menos que o valor de `ForwardLimit` aumente.

O IP de remoto original da solicitação precisa corresponder a uma entrada nas listas `KnownProxies` ou `KnownNetworks` antes dos cabeçalhos encaminhados serem processados. Isso limita a falsificação de cabeçalho por não aceitar encaminhadores de proxies não confiáveis. Quando um proxy desconhecido é detectado, o registro em log indica o endereço dele:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

No exemplo anterior, 10.0.0.100 é um servidor proxy. Se o servidor for um proxy confiável, adicione o endereço IP do servidor a `KnownProxies` (ou adicione uma rede confiável a `KnownNetworks`) em `Startup.ConfigureServices`. Para obter mais informações, consulte a seção [Opções de middleware de cabeçalhos encaminhados](#forwarded-headers-middleware-options).

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Permitir que somente proxies e redes confiáveis encaminhem os cabeçalhos. Caso contrário, podem ocorrer ataques de [falsificação de IP](https://www.iplocation.net/ip-spoofing).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:host-and-deploy/web-farm>
* [Consultoria de segurança da Microsoft CVE-2018-0787: vulnerabilidade de elevação de privilégio do ASP.NET Core](https://github.com/aspnet/Announcements/issues/295)
