---
title: Middleware no ASP.NET Core de cache de resposta
author: guardrex
description: Saiba como configurar e usar o Middleware de cache de resposta no ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: performance/caching/middleware
ms.openlocfilehash: ddbd547ec0fdd09da1f3e3ce30f8fa35a34870c2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897363"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Middleware no ASP.NET Core de cache de resposta

Por [Luke Latham](https://github.com/guardrex) e [John Luo](https://github.com/JunTaoLuo)

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([como baixar](xref:index#how-to-download-a-sample)).

Este artigo explica como configurar o Middleware de cache de resposta em um aplicativo ASP.NET Core. O middleware determina quando as respostas são armazenáveis em cache, as respostas de repositórios e respostas de serve do cache. Para obter uma introdução ao cache de HTTP e o `ResponseCache` atributo, consulte [cache de resposta](xref:performance/caching/response).

## <a name="package"></a>Pacote

::: moniker range=">= aspnetcore-2.1"

Referência a [metapacote do Microsoft](xref:fundamentals/metapackage-app) ou adicionar uma referência de pacote para o [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) pacote.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Referência a [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) ou adicionar uma referência de pacote para o [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) pacote.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Adicionar uma referência de pacote para o [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) pacote.

::: moniker-end

## <a name="configuration"></a>Configuração

No `Startup.ConfigureServices`, adicione o middleware para a coleção de serviço.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

Configurar o aplicativo para usar o middleware com o `UseResponseCaching` método de extensão, que adiciona o middleware ao pipeline de processamento da solicitação. O aplicativo de exemplo adiciona uma [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2) cabeçalho à resposta que armazena em cache respostas armazenáveis em cache por até 10 segundos. O exemplo envia uma [ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4) cabeçalho para configurar o middleware para servir a uma resposta em cache somente se o [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) cabeçalho das solicitações subsequentes corresponde da solicitação original. No exemplo de código a seguir, [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) e [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames) exigir uma `using` instrução para o [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) namespace.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

Middleware de cache de resposta só armazena em cache as respostas do servidor que resultam em um código de status 200 (Okey). Outras respostas, incluindo [páginas de erro](xref:fundamentals/error-handling), são ignorados pelo middleware.

> [!WARNING]
> Respostas que contêm o conteúdo para clientes autenticados devem ser marcadas como não armazenável em cache para impedir que o middleware de armazenamento e que atende a essas respostas. Ver [condições para armazenar em cache](#conditions-for-caching) para obter detalhes sobre como o middleware determina se uma resposta é armazenável em cache.

## <a name="options"></a>Opções

O middleware oferece três opções para controlar o cache de resposta.

| Opção                | Descrição |
| --------------------- | ----------- |
| UseCaseSensitivePaths | Determina se as respostas são armazenadas em cache em caminhos diferencia maiusculas de minúsculas. O valor padrão é `false`. |
| MaximumBodySize       | O maior tamanho armazenáveis em cache para o corpo de resposta em bytes. O valor padrão é `64 * 1024 * 1024` (64 MB). |
| SizeLimit             | O limite de tamanho para o middleware de cache de resposta em bytes. O valor padrão é `100 * 1024 * 1024` (100 MB). |

O exemplo a seguir configura o middleware para:

* Respostas em cache menores ou iguais a 1.024 bytes.
* Store as respostas por caminhos diferencia maiusculas de minúsculas (por exemplo, `/page1` e `/Page1` são armazenadas separadamente).

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Ao usar controladores de API da Web do MVC ou modelos de página de páginas do Razor, o `ResponseCache` atributo especifica os parâmetros necessários para definir os cabeçalhos apropriados para o cache de resposta. O único parâmetro do `ResponseCache` atributo que estritamente requer o middleware é `VaryByQueryKeys`, que não corresponde a um cabeçalho HTTP real. Para obter mais informações, consulte [do atributo ResponseCache](xref:performance/caching/response#responsecache-attribute).

Quando não estiver usando o `ResponseCache` atributo, cache de resposta pode ser variado com o `VaryByQueryKeys` recurso. Use o `ResponseCachingFeature` diretamente do `IFeatureCollection` da `HttpContext`:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Usando um único valor igual a `*` em `VaryByQueryKeys` o cache de varia de acordo com todos os parâmetros de consulta de solicitação.

## <a name="http-headers-used-by-response-caching-middleware"></a>Cabeçalhos HTTP usados pelo Middleware de cache de resposta

Cache de resposta pelo middleware é configurado usando cabeçalhos HTTP.

| Cabeçalho | Detalhes |
| ------ | ------- |
| Autorização | A resposta não é armazenado em cache se o cabeçalho existe. |
| Cache-Control | O middleware considera somente o cache de respostas marcadas com o `public` diretiva de cache. Controlar o cache com os seguintes parâmetros:<ul><li>max-age</li><li>max-stale&#8224;</li><li>nova min</li><li>must-revalidate</li><li>no-cache</li><li>Nenhum repositório</li><li>somente se-armazenado em cache</li><li>particulares</li><li>públicos</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Se nenhum limite é especificado para `max-stale`, o middleware não toma nenhuma ação.<br>&#8225;`proxy-revalidate`tem o mesmo efeito que `must-revalidate`.<br><br>Para obter mais informações, consulte [RFC 7231: As diretivas de controle de Cache de solicitação](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| Pragma | Um `Pragma: no-cache` cabeçalho na solicitação produz o mesmo efeito que `Cache-Control: no-cache`. Esse cabeçalho é substituído por diretivas relevantes no `Cache-Control` cabeçalho, se presente. Considerado para compatibilidade com versões anteriores com HTTP 1.0. |
| Set-Cookie | A resposta não é armazenado em cache se o cabeçalho existe. Qualquer middleware no pipeline de processamento de solicitação que define um ou mais cookies impede que o Middleware de cache de resposta de cache a resposta (por exemplo, o [provedor de TempData baseado em cookie](xref:fundamentals/app-state#tempdata)).  |
| Variar | O `Vary` cabeçalho é usado para variar a resposta em cache por outro cabeçalho. Por exemplo, armazenar em cache respostas de codificação, incluindo o `Vary: Accept-Encoding` cabeçalho, que armazena em cache respostas para solicitações com cabeçalhos `Accept-Encoding: gzip` e `Accept-Encoding: text/plain` separadamente. Uma resposta com um valor de cabeçalho de `*` nunca é armazenada. |
| Expira | Uma resposta considerada obsoleta por esse cabeçalho não é armazenada ou recuperada, a menos que substituído por outro `Cache-Control` cabeçalhos. |
| If-None-Match | A resposta completa for atendida do cache se o valor não for `*` e o `ETag` da resposta não coincide com nenhum dos valores fornecidos. Caso contrário, uma resposta 304 (não modificado) é atendido. |
| If-Modified-Since | Se o `If-None-Match` cabeçalho não estiver presente, uma resposta completa for atendida do cache se a data de resposta em cache é mais recente do que o valor fornecido. Caso contrário, uma resposta 304 (não modificado) é atendido. |
| Date | Quando atendendo do cache, o `Date` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. |
| Tamanho do conteúdo | Quando atendendo do cache, o `Content-Length` cabeçalho é definido pelo middleware se ele não foi fornecido na resposta original. |
| Idade | O `Age` cabeçalho enviado na resposta original será ignorado. O middleware calcula um novo valor ao oferecer uma resposta em cache. |

## <a name="caching-respects-request-cache-control-directives"></a>Armazenamento em cache respeita as diretivas de solicitação de Cache-Control

O middleware respeita as regras do [especificação de cache do HTTP 1.1](https://tools.ietf.org/html/rfc7234#section-5.2). As regras exigem um cache para honrar válido `Cache-Control` cabeçalho enviado pelo cliente. Sob a especificação, um cliente pode fazer solicitações com um `no-cache` força o servidor gere uma nova resposta para cada solicitação e o valor do cabeçalho. Atualmente, não há nenhum controle do desenvolvedor sobre o comportamento de cache ao usar o middleware porque o middleware segue a especificação oficial do cache.

Para obter mais controle sobre o comportamento de cache, explore outros recursos de cache do ASP.NET Core. Confira os seguintes tópicos:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Solução de problemas

Se o comportamento de cache não é conforme o esperado, confirme que as respostas sejam armazenável em cache e capaz de sendo servido do cache. Examine os cabeçalhos de entrada da solicitação e cabeçalhos de saída da resposta. Habilitar [registro em log](xref:fundamentals/logging/index) para ajudar na depuração.

Ao testar e solucionar problemas de comportamento de cache, um navegador pode definir cabeçalhos de solicitação que afetam o cache de maneira indesejada. Por exemplo, um navegador pode definir a `Cache-Control` cabeçalho `no-cache` ou `max-age=0` durante a atualização de uma página. As ferramentas a seguir podem definir explicitamente os cabeçalhos de solicitação e são preferenciais para testes de armazenamento em cache:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Condições para armazenar em cache

* A solicitação deve resultar em uma resposta do servidor com um código de status 200 (Okey).
* O método de solicitação deve ser GET ou HEAD.
* No `Startup.Configure`, Middleware de cache de resposta deve ser colocado antes do middleware que exigem compactação. Para obter mais informações, consulte <xref:fundamentals/middleware/index>.
* O `Authorization` cabeçalho não deverá estar presente.
* `Cache-Control` parâmetros do cabeçalho devem ser válidos, e a resposta deve ser marcada `public` e não marcada `private`.
* O `Pragma: no-cache` cabeçalho não deverá estar presente se o `Cache-Control` cabeçalho não estiver presente, como o `Cache-Control` cabeçalho substitui o `Pragma` cabeçalho quando presentes.
* O `Set-Cookie` cabeçalho não deverá estar presente.
* `Vary` parâmetros do cabeçalho devem ser válido e não é igual a `*`.
* O `Content-Length` valor de cabeçalho (se definido) deve corresponder ao tamanho do corpo da resposta.
* O [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) não é usado.
* A resposta não deve ser obsoleta conforme especificado pelo `Expires` cabeçalho e o `max-age` e `s-maxage` diretivas de cache.
* Buffer de resposta deve ser bem-sucedida e o tamanho da resposta deve ser menor do que o configurado ou padrão `SizeLimit`.
* A resposta deve ser armazenáveis em cache de acordo com o [RFC 7234](https://tools.ietf.org/html/rfc7234) especificações. Por exemplo, o `no-store` diretiva não deve existir nos campos de cabeçalho de solicitação ou resposta. Consulte *seção 3: Armazena as respostas em Caches* dos [RFC 7234](https://tools.ietf.org/html/rfc7234) para obter detalhes.

> [!NOTE]
> O sistema Antifalsificação para gerar tokens de seguras para evitar a falsificação de solicitação entre sites (CSRF) attacks conjuntos a `Cache-Control` e `Pragma` cabeçalhos para `no-cache` para que as respostas não são armazenadas em cache. Para obter informações sobre como desabilitar tokens antifalsificação para elementos de formulário HTML, consulte [configuração antifalsificação do ASP.NET Core](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
