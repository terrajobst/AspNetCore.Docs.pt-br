## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC não tem suporte no serviço Azure App

> [!WARNING]
> No momento, não há suporte para o [ASP.NET Core gRPC](xref:grpc/index) no serviço Azure app ou no IIS. A implementação HTTP/2 de http. sys não dá suporte a cabeçalhos de direita de resposta HTTP dos quais o gRPC se baseia. Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/AspNetCore/issues/9020).
