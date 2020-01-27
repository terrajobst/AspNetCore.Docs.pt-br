---
title: Habilitar as solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como o CORS como um padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 57098be73164c71d1b0d1fe2f3aee7ec41a32346
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727317"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilitar as solicitações entre origens (CORS) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, talvez você queira permitir que outros sites façam solicitações entre origens para seu aplicativo. Para obter mais informações, consulte o [artigo de CORS do Mozilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

CORS ( [compartilhamento de recursos entre origens](https://www.w3.org/TR/cors/) ):

* É um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
* **Não** é um recurso de segurança, o CORS libera a segurança. Uma API não é mais segura, permitindo CORS. Para obter mais informações, consulte [como o CORS funciona](#how-cors).
* Permite que um servidor permita explicitamente algumas solicitações entre origens enquanto rejeita outras.
* É mais seguro e flexível do que as técnicas anteriores, como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Duas URLs têm a mesma origem se tiverem esquemas, hosts e portas idênticos ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Essas duas URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Essas URLs têm origens diferentes das duas URLs anteriores:

* `https://example.net` &ndash; domínio diferente
* `https://www.example.com/foo.html` &ndash; subdomínio diferente
* `http://example.com/foo.html` &ndash; esquema diferente
* `https://example.com:9000/foo.html` &ndash; porta diferente

O Internet Explorer não considera a porta ao comparar origens.

## <a name="cors-with-named-policy-and-middleware"></a>CORS com política nomeada e middleware

O middleware CORS lida com solicitações entre origens. O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

O código anterior:

* Define o nome da política como "\_myAllowSpecificOrigins". O nome da política é arbitrário.
* Chama o método de extensão <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>, que habilita o CORS.
* Chama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com uma [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). O lambda usa um objeto <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>. [Opções de configuração](#cors-policy-options), como `WithOrigins`, são descritas posteriormente neste artigo.

A chamada do método <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> adiciona serviços CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [Opções de política de CORS](#cpo) neste documento.

O método <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> pode encadear métodos, conforme mostrado no código a seguir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Observação: a URL **não** deve conter uma barra à direita (`/`). Se a URL for encerrada com `/`, a comparação retornará `false` e nenhum cabeçalho será retornado.

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>Aplicar políticas CORS a todos os pontos de extremidade

O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> Com o roteamento de ponto de extremidade, o middleware CORS deve ser configurado para ser executado entre as chamadas para `UseRouting` e `UseEndpoints`. A configuração incorreta fará com que o middleware pare de funcionar corretamente.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
O código a seguir aplica políticas CORS a todos os pontos de extremidade de aplicativos por meio do middleware CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Observação: `UseCors` deve ser chamado antes de `UseMvc`.

::: moniker-end

Consulte [habilitar CORS em Razor pages, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.

Consulte [testar CORS](#test) para obter instruções sobre como testar o código anterior.

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>Habilitar CORS com roteamento de ponto de extremidade

Com o roteamento de ponto de extremidade, o CORS pode ser habilitado em uma base por ponto de extremidade usando o conjunto de `RequireCors` de métodos de extensão.

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

Da mesma forma, o CORS também pode ser habilitado para todos os controladores:

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>Habilitar CORS com atributos

O atributo [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornece uma alternativa para aplicar o CORS globalmente. O atributo `[EnableCors]` habilita CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.

Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.

O atributo `[EnableCors]` pode ser aplicado a:

* `PageModel` de página Razor
* Controlador
* Método de ação do controlador

Você pode aplicar políticas diferentes ao controlador/página-modelo/ação com o atributo `[EnableCors]`. Quando o atributo `[EnableCors]` é aplicado a um método de controladores/página-modelo/ação, e o CORS é habilitado no middleware, ambas as políticas são aplicadas. Recomendamos a combinação de políticas. Use o atributo `[EnableCors]` ou o middleware, não ambos no mesmo aplicativo.

O código a seguir aplica uma política diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria uma política padrão CORS e uma política chamada `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Desabilitar CORS

O atributo [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) DESABILITA o CORS para o controlador/página-modelo/ação.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política de CORS

Esta seção descreve as várias opções que podem ser definidas em uma política CORS:

* [Definir as origens permitidas](#set-the-allowed-origins)
* [Definir os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Definir os cabeçalhos de solicitação permitidos](#set-the-allowed-request-headers)
* [Definir os cabeçalhos de resposta expostos](#set-the-exposed-response-headers)
* [Credenciais em solicitações entre origens](#credentials-in-cross-origin-requests)
* [Definir o tempo de expiração de simulação](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado no `Startup.ConfigureServices`. Para algumas opções, pode ser útil ler a seção [como o CORS funciona](#how-cors) primeiro.

## <a name="set-the-allowed-origins"></a>Definir as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; permite solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`). `AllowAnyOrigin` não é seguro porque *qualquer site* pode fazer solicitações entre origens para o aplicativo.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. O serviço CORS retorna uma resposta CORS inválida quando um aplicativo é configurado com ambos os métodos.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Especificar `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e pode resultar em falsificação de solicitação entre sites. Para um aplicativo seguro, especifique uma lista exata de origens se o cliente precisar se autorizar para acessar os recursos do servidor.

::: moniker-end

`AllowAnyOrigin` afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Origin`. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; define a propriedade <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> da política como uma função que permite que as origens correspondam a um domínio curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Definir os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite qualquer método HTTP:
* Afeta as solicitações de simulação e o cabeçalho de `Access-Control-Allow-Methods`. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Definir os cabeçalhos de solicitação permitidos

Para permitir que cabeçalhos específicos sejam enviados em uma solicitação CORS, chamada de *cabeçalhos de solicitação de autor*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especifique os cabeçalhos permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Essa configuração afeta as solicitações de simulação e o cabeçalho `Access-Control-Request-Headers`. Para obter mais informações, consulte a seção [solicitações de simulação](#preflight-requests) .

::: moniker range=">= aspnetcore-2.2"

Uma política de middleware CORS correspondente a cabeçalhos específicos especificados por `WithHeaders` só é possível quando os cabeçalhos enviados em `Access-Control-Request-Headers` correspondem exatamente aos cabeçalhos indicados no `WithHeaders`.

Por exemplo, considere um aplicativo configurado da seguinte maneira:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

O middleware CORS recusa uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` ([headernames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado em `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

O aplicativo retorna uma resposta de *200 OK* , mas não envia os cabeçalhos CORS de volta. Portanto, o navegador não tenta a solicitação entre origens.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

O middleware CORS sempre permite que quatro cabeçalhos na `Access-Control-Request-Headers` sejam enviados, independentemente dos valores configurados em CorsPolicy. Headers. Essa lista de cabeçalhos inclui:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por exemplo, considere um aplicativo configurado da seguinte maneira:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

O middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` sempre está na lista de permissões:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Definir os cabeçalhos de resposta expostos

Por padrão, o navegador não expõe todos os cabeçalhos de resposta ao aplicativo. Para obter mais informações, consulte [compartilhamento de recursos entre origens do W3C (terminologia): cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta que estão disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*. Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciais em solicitações entre origens

As credenciais exigem tratamento especial em uma solicitação CORS. Por padrão, o navegador não envia credenciais com uma solicitação entre origens. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` como `true`.

Usando `XMLHttpRequest` diretamente:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Usando o jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Usando a [API de busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir as credenciais. Para permitir credenciais entre origens, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

A resposta HTTP inclui um cabeçalho `Access-Control-Allow-Credentials`, que informa ao navegador que o servidor permite credenciais para uma solicitação entre origens.

Se o navegador enviar credenciais, mas a resposta não incluir um cabeçalho de `Access-Control-Allow-Credentials` válido, o navegador não exporá a resposta ao aplicativo e a solicitação entre origens falhará.

Permitir credenciais entre origens é um risco de segurança. Um site em outro domínio pode enviar as credenciais de um usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação CORS também indica que a definição de origens para `"*"` (todas as origens) é inválida se o cabeçalho `Access-Control-Allow-Credentials` estiver presente.

### <a name="preflight-requests"></a>Solicitações de simulação

Para algumas solicitações de CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real. Essa solicitação é chamada de uma *solicitação de simulação*. O navegador poderá ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos de solicitação diferentes de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`ou `Last-Event-ID`.
* O cabeçalho `Content-Type`, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra nos cabeçalhos de solicitação definidos para a solicitação do cliente aplica-se aos cabeçalhos que o aplicativo define ao chamar `setRequestHeader` no objeto `XMLHttpRequest`. A especificação CORS chama esses cabeçalhos de *solicitação de autor*de cabeçalho. A regra não se aplica aos cabeçalhos que o navegador pode definir, como `User-Agent`, `Host`ou `Content-Length`.

Veja a seguir um exemplo de uma solicitação de simulação:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

A solicitação de simulação usa o método de opções HTTP. Ele inclui dois cabeçalhos especiais:

* `Access-Control-Request-Method`: o método HTTP que será usado para a solicitação real.
* `Access-Control-Request-Headers`: uma lista de cabeçalhos de solicitação que o aplicativo define na solicitação real. Como mencionado anteriormente, isso não inclui cabeçalhos que o navegador define, como `User-Agent`.

Uma solicitação de simulação de CORS pode incluir um cabeçalho `Access-Control-Request-Headers`, que indica ao servidor os cabeçalhos que são enviados com a solicitação real.

Para permitir cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir todos os cabeçalhos de solicitação de autor, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Os navegadores não são totalmente consistentes em como eles definem `Access-Control-Request-Headers`. Se você definir cabeçalhos para algo diferente de `"*"` (ou usar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), deverá incluir pelo menos `Accept`, `Content-Type`e `Origin`, além de todos os cabeçalhos personalizados aos quais você deseja dar suporte.

Veja a seguir um exemplo de resposta para a solicitação de simulação (supondo que o servidor permita a solicitação):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

A resposta inclui um cabeçalho `Access-Control-Allow-Methods` que lista os métodos permitidos e, opcionalmente, um cabeçalho `Access-Control-Allow-Headers`, que lista os cabeçalhos permitidos. Se a solicitação de simulação for realizada com sucesso, o navegador enviará a solicitação real.

Se a solicitação de simulação for negada, o aplicativo retornará uma resposta *200 OK* , mas não enviará os cabeçalhos CORS de volta. Portanto, o navegador não tenta a solicitação entre origens.

### <a name="set-the-preflight-expiration-time"></a>Definir o tempo de expiração de simulação

O cabeçalho `Access-Control-Max-Age` especifica por quanto tempo a resposta à solicitação de simulação pode ser armazenada em cache. Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como o CORS funciona

Esta seção descreve o que acontece em uma solicitação de [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) no nível das mensagens http.

* O CORS **não** é um recurso de segurança. O CORS é um padrão W3C que permite que um servidor Relaxe a política de mesma origem.
  * Por exemplo, um ator mal-intencionado poderia usar a [prevenção de scripts entre sites (XSS)](xref:security/cross-site-scripting) em seu site e executar uma solicitação entre sites para o site habilitado para CORS para roubar informações.
* Sua API não é mais segura, permitindo CORS.
  * Cabe ao cliente (navegador) impor o CORS. O servidor executa a solicitação e retorna a resposta, é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das seguintes ferramentas exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da Web digitando a URL na barra de endereços.
* É uma maneira de um servidor permitir que os navegadores executem uma solicitação de API de [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou de [busca](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) de várias origens que, de outra forma, seriam proibidas.
  * Os navegadores (sem CORS) não podem fazer solicitações entre origens. Antes do CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para burlar essa restrição. JSONP não usa XHR, ele usa a marca `<script>` para receber a resposta. Os scripts podem ser carregados entre origens.

A [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que habilitam solicitações entre origens. Se um navegador oferecer suporte a CORS, ele definirá esses cabeçalhos automaticamente para solicitações entre origens. O código JavaScript personalizado não é necessário para habilitar o CORS.

Veja a seguir um exemplo de uma solicitação entre origens. O cabeçalho `Origin` fornece o domínio do site que está fazendo a solicitação. O cabeçalho de `Origin` é necessário e deve ser diferente do host.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Se o servidor permitir a solicitação, ele definirá o cabeçalho `Access-Control-Allow-Origin` na resposta. O valor desse cabeçalho corresponde ao cabeçalho de `Origin` da solicitação ou é o valor curinga `"*"`, o que significa que qualquer origem é permitida:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Se a resposta não incluir o cabeçalho `Access-Control-Allow-Origin`, a solicitação entre origens falhará. Especificamente, o navegador não permite a solicitação. Mesmo se o servidor retornar uma resposta bem-sucedida, o navegador não tornará a resposta disponível para o aplicativo cliente.

<a name="test"></a>

## <a name="test-cors"></a>Testar CORS

Para testar o CORS:

1. [Criar um projeto de API](xref:tutorials/first-web-api). Como alternativa, você pode [baixar o exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite o CORS usando uma das abordagens deste documento. Por exemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante ao [código de exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Criar um projeto de aplicativo Web (Razor Pages ou MVC). O exemplo usa Razor Pages. Você pode criar o aplicativo Web na mesma solução que o projeto de API.
1. Adicione o seguinte código realçado ao arquivo *index. cshtml* :

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` pela URL para o aplicativo implantado.
1. Implante o projeto de API. Por exemplo, [implante no Azure](xref:host-and-deploy/azure-apps/index).
1. Execute o Razor Pages ou o aplicativo MVC na área de trabalho e clique no botão **testar** . Use as ferramentas F12 para examinar mensagens de erro.
1. Remova a origem do localhost de `WithOrigins` e implante o aplicativo. Como alternativa, execute o aplicativo cliente com uma porta diferente. Por exemplo, execute do Visual Studio.
1. Teste com o aplicativo cliente. As falhas de CORS retornam um erro, mas a mensagem de erro não está disponível para JavaScript. Use a guia Console nas Ferramentas F12 para ver o erro. Dependendo do navegador, você receberá um erro (no console de ferramentas F12) semelhante ao seguinte:

   * Usando o Microsoft Edge:

     **SEC7120: [CORS] a origem `https://localhost:44375` não encontrou `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para recursos entre origens em `https://webapi.azurewebsites.net/api/values/1`**

   * Usando o Chrome:

     **O acesso a XMLHttpRequest em `https://webapi.azurewebsites.net/api/values/1` da origem `https://localhost:44375` foi bloqueado pela política CORS: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado.**
     
Os pontos de extremidade habilitados para CORS podem ser testados com uma ferramenta, como o [Fiddler](https://www.telerik.com/fiddler) ou o [postmaster](https://www.getpostman.com/). Ao usar uma ferramenta, a origem da solicitação especificada pelo cabeçalho de `Origin` deve ser diferente do host que está recebendo a solicitação. Se a solicitação não for de *origem cruzada* com base no valor do cabeçalho de `Origin`:

* Não há necessidade de middleware de CORS para processar a solicitação.
* Cabeçalhos CORS não são retornados na resposta.

## <a name="cors-in-iis"></a>CORS no IIS

Ao implantar no IIS, o CORS precisará ser executado antes da autenticação do Windows se o servidor não estiver configurado para permitir acesso anônimo. Para dar suporte a esse cenário, o [módulo CORS do IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) precisa ser instalado e configurado para o aplicativo.

## <a name="additional-resources"></a>Recursos adicionais

* [CORS (compartilhamento de recursos entre origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Introdução ao módulo CORS do IIS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
