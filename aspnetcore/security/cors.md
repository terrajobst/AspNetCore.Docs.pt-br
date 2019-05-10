---
title: Habilitar solicitações entre origens (CORS) no ASP.NET Core
author: rick-anderson
description: Saiba como CORS como padrão para permitir ou rejeitar solicitações entre origens em um aplicativo ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: 655d9be894c677f8adf0fecc2b465d5ae7af2b61
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897463"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Habilitar solicitações entre origens (CORS) no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este artigo mostra como habilitar o CORS em um aplicativo ASP.NET Core.

Segurança do navegador impede que uma página da web fazendo solicitações para um domínio diferente daquele que atendia a página da web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Às vezes, você talvez queira permitir que outros sites fazem solicitações entre origens em seu aplicativo. Para obter mais informações, consulte o [artigo Mozilla CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):

* É um W3C padrão que permite que um servidor relaxar a política de mesma origem.
* Está **não** um recurso de segurança, o CORS alivia a segurança. Uma API não é mais segura, permitindo que o CORS. Para obter mais informações, consulte [funciona como o CORS](#how-cors).
* Permite que um servidor explicitamente permitir algumas solicitações entre origens enquanto rejeita outras.
* É mais seguro e mais flexível do que técnicas anteriores, tais como [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Mesma origem

Duas URLs têm a mesma origem se eles têm esquemas idênticas, hosts e portas ([6454 RFC](https://tools.ietf.org/html/rfc6454)).

Essas duas URLs têm a mesma origem:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Essas URLs têm diferentes origens que as duas URLs anteriores:

* `https://example.net` &ndash; Domínio diferente
* `https://www.example.com/foo.html` &ndash; Subdomínio diferente
* `http://example.com/foo.html` &ndash; Esquema diferente
* `https://example.com:9000/foo.html` &ndash; Porta diferente

Internet Explorer não considera a porta ao comparar as origens.

## <a name="cors-with-named-policy-and-middleware"></a>CORS com middleware e nomeado de política

Middleware CORS manipula solicitações entre origens. O código a seguir habilita o CORS para todo o aplicativo com a origem especificada:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

O código anterior:

* Define o nome da política para "\_myAllowSpecificOrigins". O nome da política é arbitrário.
* Chama o <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> método de extensão, que habilita o CORS.
* Chamadas <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> com um [expressão lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). O lambda utiliza um <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> objeto. [Opções de configuração](#cors-policy-options), tais como `WithOrigins`, são descritos neste artigo.

O <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chamada de método adiciona serviços do CORS ao contêiner de serviço do aplicativo:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Para obter mais informações, consulte [opções de política CORS](#cpo) neste documento.

O <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> método pode encadear os métodos, conforme mostrado no código a seguir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

O seguinte código realçado se aplica a políticas CORS para todos os pontos de extremidade aplicativos por meio do Middleware do CORS:

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

Ver [habilitar CORS em páginas do Razor, controladores e métodos de ação](#ecors) para aplicar a política CORS no nível de página/controlador/ação.

Observação:

* `UseCors` deve ser chamado antes de `UseMvc`.
* A URL deve **não** contêm uma barra à direita (`/`). Se a URL termina com `/`, a comparação retorna `false` e nenhum cabeçalho é retornado.

Ver [CORS teste](#test) para obter instruções sobre como testar o código anterior.

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a>Habilitar o CORS com atributos

O [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) atributo fornece uma alternativa de aplicação CORS globalmente. O `[EnableCors]` atributo habilita o CORS para os pontos de extremidade selecionados, em vez de todos os pontos de extremidade.

Use `[EnableCors]` para especificar a política padrão e `[EnableCors("{Policy String}")]` para especificar uma política.

O `[EnableCors]` atributo pode ser aplicado a:

* Página do Razor `PageModel`
* Controlador
* Método de ação do controlador

Você pode aplicar políticas diferentes para o controlador/página-modelo/ação com o `[EnableCors]` atributo. Quando o `[EnableCors]` atributo é aplicado a um método de controladores/página-modelo/ação e CORS estiver habilitado no middleware, ambas as políticas são aplicadas. É recomendável combinar as políticas. Use o `[EnableCors]` atributo ou middleware, não ambos no mesmo aplicativo.

O código a seguir se aplica a uma política diferente a cada método:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

O código a seguir cria uma política de CORS padrão e uma política chamada `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Desabilitar o CORS

O [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) atributo desabilita o CORS para o controlador/página modelo/ação.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Opções de política de CORS

Esta seção descreve as várias opções que podem ser definidas em uma política CORS:

* [Defina as origens permitidas](#set-the-allowed-origins)
* [Defina os métodos HTTP permitidos](#set-the-allowed-http-methods)
* [Definir os cabeçalhos de solicitação permitido](#set-the-allowed-request-headers)
* [Definir os cabeçalhos de resposta exposto](#set-the-exposed-response-headers)
* [Credenciais nas solicitações entre origens](#credentials-in-cross-origin-requests)
* [Definir o tempo de expiração de simulação](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> é chamado no `Startup.ConfigureServices`. Para algumas opções, pode ser útil ler o [funciona como o CORS](#how-cors) seção pela primeira vez.

## <a name="set-the-allowed-origins"></a>Defina as origens permitidas

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Permite que as solicitações CORS de todas as origens com qualquer esquema (`http` ou `https`). `AllowAnyOrigin` não é seguro porque *de qualquer site* pode fazer solicitações entre origens para o aplicativo.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Especificando `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e podem resultar em falsificação de solicitação entre sites. O serviço CORS retorna uma resposta inválida do CORS, quando um aplicativo é configurado com os dois métodos.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Especificando `AllowAnyOrigin` e `AllowCredentials` é uma configuração insegura e podem resultar em falsificação de solicitação entre sites. Para um aplicativo seguro, especifique uma lista exata de origens, se o cliente deve autorizar a mesmo para acessar recursos do servidor.

::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**.
-->

`AllowAnyOrigin` solicitações de simulação afeta e o `Access-Control-Allow-Origin` cabeçalho. Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Conjuntos de <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> propriedade da política para ser uma função que permite origens corresponder a um domínio de curinga configurado ao avaliar se a origem é permitida.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Defina os métodos HTTP permitidos

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Permite que qualquer método HTTP:
* Solicitações de simulação afeta e o `Access-Control-Allow-Methods` cabeçalho. Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.

### <a name="set-the-allowed-request-headers"></a>Definir os cabeçalhos de solicitação permitido

Para permitir que os cabeçalhos específicos a serem enviados em uma solicitação CORS, chamado *criar cabeçalhos de solicitação*, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e especificar os cabeçalhos permitidos:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir que todos os cabeçalhos de solicitação do autor chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Essa configuração afeta as solicitações de simulação e o `Access-Control-Request-Headers` cabeçalho. Para obter mais informações, consulte o [solicitações de simulação](#preflight-requests) seção.

::: moniker range=">= aspnetcore-2.2"

Uma correspondência de política de Middleware do CORS para cabeçalhos específicos especificado por `WithHeaders` só é possível quando os cabeçalhos enviados `Access-Control-Request-Headers` coincidir exatamente com os cabeçalhos indicados na `WithHeaders`.

Por exemplo, considere um aplicativo configurado da seguinte maneira:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS recusar uma solicitação de simulação com o seguinte cabeçalho de solicitação, pois `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) não está listado no `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

O aplicativo retorna um *200 Okey* resposta, mas não envia de volta os cabeçalhos de CORS. Portanto, o navegador não tente a solicitação entre origens.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Middleware CORS sempre permite que os cabeçalhos de quatro no `Access-Control-Request-Headers` a ser enviado, independentemente dos valores configurados no CorsPolicy.Headers. Esta lista de cabeçalhos inclui:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Por exemplo, considere um aplicativo configurado da seguinte maneira:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Middleware CORS responde com êxito a uma solicitação de simulação com o seguinte cabeçalho de solicitação porque `Content-Language` é sempre na lista de permissões:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Definir os cabeçalhos de resposta exposto

Por padrão, o navegador não expõe todos os cabeçalhos de resposta para o aplicativo. Para obter mais informações, consulte [W3C Cross-Origin Resource Sharing (terminologia): Cabeçalho de resposta simples](https://www.w3.org/TR/cors/#simple-response-header).

Os cabeçalhos de resposta que estão disponíveis por padrão são:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

A especificação CORS chama esses cabeçalhos *cabeçalhos de resposta simples*. Para disponibilizar outros cabeçalhos para o aplicativo, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Credenciais nas solicitações entre origens

Credenciais exigem tratamento especial em uma solicitação CORS. Por padrão, o navegador não envia as credenciais com uma solicitação entre origens. As credenciais incluem cookies e esquemas de autenticação HTTP. Para enviar as credenciais com uma solicitação entre origens, o cliente deve definir `XMLHttpRequest.withCredentials` para `true`.

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

Usando o [buscar API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

O servidor deve permitir que as credenciais. Para permitir credenciais entre origens, chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

A resposta HTTP inclui um `Access-Control-Allow-Credentials` cabeçalho, que informa ao navegador que o servidor permite que as credenciais para uma solicitação entre origens.

Se o navegador envia as credenciais, mas a resposta não inclui um válido `Access-Control-Allow-Credentials` cabeçalho, o navegador não expõe a resposta para o aplicativo e a solicitação entre origens falhar.

Permitindo credenciais entre origens é um risco à segurança. Um site em outro domínio pode enviar credenciais do usuário conectado para o aplicativo em nome do usuário sem o conhecimento do usuário. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

A especificação CORS também declara que a configuração origens `"*"` (todas as origens) é inválida se o `Access-Control-Allow-Credentials` cabeçalho está presente.

### <a name="preflight-requests"></a>Solicitações de simulação

Para algumas solicitações CORS, o navegador envia uma solicitação adicional antes de fazer a solicitação real. Essa solicitação é chamada de um *solicitação de simulação*. O navegador pode ignorar a solicitação de simulação se as seguintes condições forem verdadeiras:

* O método de solicitação é GET, HEAD ou POST.
* O aplicativo não define cabeçalhos de solicitação diferente de `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, ou `Last-Event-ID`.
* O `Content-Type` cabeçalho, se definido, tem um dos seguintes valores:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

A regra em cabeçalhos de solicitação definido para a solicitação do cliente se aplica aos cabeçalhos que o aplicativo define chamando `setRequestHeader` sobre o `XMLHttpRequest` objeto. A especificação CORS chama esses cabeçalhos *criar cabeçalhos de solicitação*. A regra não se aplica aos cabeçalhos, o navegador pode definir, tais como `User-Agent`, `Host`, ou `Content-Length`.

Este é um exemplo de uma solicitação de simulação:

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

A solicitação de simulação usa o método HTTP OPTIONS. Ele inclui dois cabeçalhos especiais:

* `Access-Control-Request-Method`: O método HTTP que será usado para a solicitação real.
* `Access-Control-Request-Headers`: Uma lista de cabeçalhos de solicitação que o aplicativo define a solicitação real. Conforme mencionado anteriormente, isso não inclui os cabeçalhos que define o navegador, tais como `User-Agent`.

Uma solicitação de simulação de CORS pode incluir um `Access-Control-Request-Headers` cabeçalho, que indica ao servidor, os cabeçalhos que são enviados com a solicitação real.

Para permitir que os cabeçalhos específicos, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Para permitir que todos os cabeçalhos de solicitação do autor chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Navegadores não estão totalmente consistentes em como eles definir `Access-Control-Request-Headers`. Se você definir os cabeçalhos para algo diferente de `"*"` (ou use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), você deve incluir pelo menos `Accept`, `Content-Type`, e `Origin`, além de quaisquer cabeçalhos personalizados que você deseja dar suporte.

Este é um exemplo de resposta à solicitação de simulação (supondo que o servidor permite que a solicitação):

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

A resposta inclui um `Access-Control-Allow-Methods` cabeçalho que lista os métodos permitidos e, opcionalmente, um `Access-Control-Allow-Headers` cabeçalho, que lista os cabeçalhos permitidos. Se a solicitação de simulação for bem-sucedida, o navegador envia a solicitação real.

Se a solicitação de simulação for negada, o aplicativo retornar um *200 Okey* resposta, mas não envia de volta os cabeçalhos de CORS. Portanto, o navegador não tente a solicitação entre origens.

### <a name="set-the-preflight-expiration-time"></a>Definir o tempo de expiração de simulação

O `Access-Control-Max-Age` cabeçalho Especifica quanto tempo a resposta à solicitação de simulação pode ser armazenados em cache. Para definir esse cabeçalho, chame <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Como funciona o CORS

Esta seção descreve o que acontece em um [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) solicitação no nível das mensagens HTTP.

* O CORS é **não** um recurso de segurança. O CORS é um padrão W3C que permite que um servidor relaxar a política de mesma origem.
  * Por exemplo, um ator mal-intencionado pode usar [impedir Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) em relação a seu site e executar uma solicitação entre sites para o respectivo site CORS habilitado para roubar informações.
* Sua API não é mais segura, permitindo que o CORS.
  * Cabe ao cliente (navegador) para impor o CORS. O servidor executa a solicitação e retorna a resposta, ele é o cliente que retorna um erro e bloqueia a resposta. Por exemplo, qualquer uma das ferramentas a seguir exibirá a resposta do servidor:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient do .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Um navegador da web inserindo a URL na barra de endereços.
* É uma maneira para um servidor permitir que os navegadores para executar uma entre origens [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) ou [API buscar](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) solicitação caso contrário, seria proibida.
  * Navegadores (sem CORS) não podem fazer solicitações entre origens. Antes de CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) foi usado para evitar essa restrição. JSONP não usa XHR, ele usa o `<script>` marca receba a resposta. Scripts podem ser carregados entre origens.

O [especificação CORS](https://www.w3.org/TR/cors/) introduziu vários novos cabeçalhos HTTP que permitem que solicitações entre origens. Se um navegador oferece suporte a CORS, ele define esses cabeçalhos automaticamente para solicitações entre origens. Código JavaScript personalizado não é necessário para habilitar o CORS.

O exemplo a seguir é um exemplo de uma solicitação entre origens. O `Origin` cabeçalho fornece o domínio do site que está fazendo a solicitação:

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

Se o servidor permite que a solicitação, ele define o `Access-Control-Allow-Origin` cabeçalho na resposta. O valor desse cabeçalho também corresponde a `Origin` cabeçalho da solicitação ou é o valor de curinga `"*"`, o que significa que qualquer origem é permitida:

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

Se a resposta não inclui o `Access-Control-Allow-Origin` falha do cabeçalho, a solicitação entre origens. Especificamente, o navegador não permite a solicitação. Mesmo se o servidor retorna uma resposta bem-sucedida, o navegador não disponibiliza a resposta para o aplicativo cliente.

<a name="test"></a>

## <a name="test-cors"></a>Testar CORS

Para testar o CORS:

1. [Criar um projeto de API](xref:tutorials/first-web-api). Como alternativa, você pode [baixar o exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Habilite CORS usando uma das abordagens neste documento. Por exemplo:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` só deve ser usado para testar um aplicativo de exemplo semelhante para o [baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Crie um projeto de aplicativo web (páginas do Razor ou MVC). O exemplo usa as páginas do Razor. Você pode criar o aplicativo web na mesma solução que o projeto de API.
1. Adicione o seguinte código realçado para o *index. cshtml* arquivo:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. No código anterior, substitua `url: 'https://<web app>.azurewebsites.net/api/values/1',` com a URL para o aplicativo implantado.
1. Implante o projeto de API. Por exemplo, [implantar no Azure](xref:host-and-deploy/azure-apps/index).
1. Execute o aplicativo de páginas do Razor ou MVC da área de trabalho e clique no **teste** botão. Use as ferramentas de F12 para examinar mensagens de erro.
1. Remover a origem do localhost de `WithOrigins` e implantar o aplicativo. Como alternativa, execute o aplicativo cliente com uma porta diferente. Por exemplo, execute do Visual Studio.
1. Teste com o aplicativo cliente. Falhas CORS retornar um erro, mas a mensagem de erro não está disponível para o JavaScript. Use a guia console nas ferramentas F12 para ver o erro. Dependendo do navegador, você receberá um erro (no console de ferramentas de F12) semelhante ao seguinte:

   * Usando o Microsoft Edge:

     **SEC7120: [CORS] a origem `https://localhost:44375` não foi possível localizar `https://localhost:44375` no cabeçalho de resposta Access-Control-Allow-Origin para recursos entre origens em `https://webapi.azurewebsites.net/api/values/1`**

   * Usando o Chrome:

     **Acesso ao XMLHttpRequest em `https://webapi.azurewebsites.net/api/values/1` de origem `https://localhost:44375` foi bloqueada pela política CORS: Nenhum cabeçalho 'Access-Control-Allow-Origin' está presente no recurso solicitado.**

## <a name="additional-resources"></a>Recursos adicionais

* [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
