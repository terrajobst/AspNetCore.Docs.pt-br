# <a name="aspnet-core-distributed-cache-sample"></a>Amostra de Cache distribuído do ASP.NET Core

Este exemplo ilustra o uso de um cache distribuído. Este exemplo demonstra o cenário descrito o [trabalhar com um cache distribuído no ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) tópico.

No ambiente de produção, o aplicativo de exemplo está configurado para usar um cache distribuído do SQL Server. Para reconfigurar o aplicativo para usar um cache Redis distribuído, altere a diretiva de pré-processador na parte superior do *Startup.cs* arquivo para usar o Redis (`#define Redis // SQLServer`). Para obter mais informações, consulte [diretivas de pré-processador no código de exemplo](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code).
