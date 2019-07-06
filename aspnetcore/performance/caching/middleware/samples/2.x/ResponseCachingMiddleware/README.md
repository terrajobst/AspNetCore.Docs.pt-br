# <a name="aspnet-core-response-caching-sample"></a>Exemplo de cache de resposta do ASP.NET Core

Este exemplo ilustra o uso do ASP.NET Core [Middleware de cache de resposta](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

O aplicativo responde com a página de índice, incluindo um `Cache-Control` cabeçalho para configurar o comportamento do cache. O aplicativo também define o `Vary` cabeçalho para configurar o cache para servir a resposta somente se o `Accept-Encoding` cabeçalho das solicitações subsequentes corresponde da solicitação original.

Ao executar o exemplo, a página de índice for atendida do cache quando armazenados e armazenado em cache por até 10 segundos.

Para testar o comportamento de cache:

* Não use um navegador para testar o comportamento do cache. Navegadores normalmente adicionam um cabeçalho de controle de cache ao recarregar que impedem que o middleware que atende a uma página em cache. Por exemplo, uma `Cache-Control` cabeçalho com um valor de `max-age=0`) podem ser adicionados pelo navegador.
* Usar uma ferramenta de desenvolvedor que permite configurar os cabeçalhos de solicitação explicitamente, como <a href="https://www.telerik.com/fiddler">Fiddler</a> ou <a href="https://www.getpostman.com/">Postman</a>.
