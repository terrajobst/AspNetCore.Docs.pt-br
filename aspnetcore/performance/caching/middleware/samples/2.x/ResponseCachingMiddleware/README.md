# <a name="aspnet-core-response-caching-sample"></a>Exemplo de cache de resposta ASP.NET Core

Este exemplo ilustra o uso do [middleware de cache de resposta](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)ASP.NET Core.

O aplicativo responde com sua página de índice, incluindo um cabeçalho `Cache-Control` para configurar o comportamento de Caching. O aplicativo também definirá o cabeçalho `Vary` para configurar o cache para atender à resposta somente se o cabeçalho de `Accept-Encoding` das solicitações subsequentes corresponder ao da solicitação original.

Ao executar o exemplo, a página de índice é servida do cache quando armazenada e armazenada em cache por até 10 segundos.

Para testar o comportamento de Caching:

* Não use um navegador para testar o comportamento do cache. Os navegadores geralmente adicionam um cabeçalho de controle de cache ao recarregar, o que impede que o middleware forneça uma página armazenada em cache. Por exemplo, um cabeçalho de `Cache-Control` com um valor de `max-age=0`) pode ser adicionado pelo navegador.
* Use uma ferramenta de desenvolvedor que permita definir os cabeçalhos de solicitação explicitamente, como o <a href="https://www.telerik.com/fiddler">Fiddler</a> ou o <a href="https://www.getpostman.com/">postmaster</a>.
