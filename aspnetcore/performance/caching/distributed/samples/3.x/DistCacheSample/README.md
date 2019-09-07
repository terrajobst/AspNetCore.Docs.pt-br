# <a name="aspnet-core-distributed-cache-sample"></a>ASP.NET Core amostra de cache distribuído

Este exemplo ilustra o uso de um cache distribuído. Este exemplo demonstra o cenário descrito no tópico [trabalhar com um cache distribuído no ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) .

No ambiente de produção, o aplicativo de exemplo é configurado para usar um cache SQL Server distribuído. Para reconfigurar o aplicativo para usar um cache Redis distribuído, altere a diretiva de pré-processador na parte superior do arquivo *Startup.cs* para usar Redis (`#define Redis // SQLServer`). Para obter mais informações, consulte [diretivas de pré-processador no código de exemplo](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code).
