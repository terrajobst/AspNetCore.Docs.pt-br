# <a name="blazor-client-side-sample-app"></a>Aplicativo de exemplo mais alto (lado do cliente)

Este exemplo ilustra o uso de cenários de mais claros descritos na documentação mais simples.

## <a name="call-web-api-example"></a>Exemplo de API Web de chamada

O exemplo de API da Web requer uma API Web em execução com base no aplicativo <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">de exemplo para o tutorial: Crie uma API Web com o tópico</a> ASP.NET Core MVC. O aplicativo de exemplo faz solicitações para a API da `https://localhost:10000/api/todo`Web em. Se um endereço da API Web diferente for usado, atualize `ServiceEndpoint` o valor constante no bloco do `@functions` componente Razor.</p>

O aplicativo de exemplo faz uma solicitação de <a href="https://docs.microsoft.com/aspnet/core/security/cors">compartilhamento de recursos entre origens (CORS)</a> de `http://localhost:5000` ou `https://localhost:5001` para a API da Web. As credenciais (cookies/cabeçalhos de autorização) são permitidas. Adicione a seguinte configuração de middleware CORS ao método da `Startup.Configure` API Web antes de chamar: `UseMvc`</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajuste os domínios e as portas `WithOrigins` do conforme necessário para o aplicativo mais incrivelmente.

A API da Web é configurada para CORS para permitir cookies/cabeçalhos de autorização e solicitações do código do cliente, mas a API da Web criada pelo tutorial não autoriza, de fato, solicitações. Consulte os <a href="https://docs.microsoft.com/aspnet/core/security/">artigos ASP.NET Core segurança e identidade</a> para obter diretrizes de implementação.
