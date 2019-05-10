# <a name="aspnet-core-response-caching-sample"></a>Exemplo de cache de resposta do ASP.NET Core

Este exemplo ilustra o uso do ASP.NET Core [Middleware de cache de resposta](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

O aplicativo responde com a página de índice, incluindo um `Cache-Control` cabeçalho para configurar o comportamento do cache. O aplicativo também define o `Vary` cabeçalho para configurar o cache para servir a resposta somente se o `Accept-Encoding` cabeçalho das solicitações subsequentes corresponde da solicitação original.

Ao executar o exemplo, a página de índice for atendida do cache quando armazenados e armazenado em cache por até 10 segundos.
