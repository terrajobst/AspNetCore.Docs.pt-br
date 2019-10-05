# <a name="blazor-webassembly-sample-app"></a>Aplicativo de exemplo Webassembly mais incrivelmente

Este exemplo ilustra o uso de cenários de mais claros descritos na documentação mais simples.

## <a name="call-web-api-example"></a>Exemplo de API Web de chamada

O exemplo de API Web requer uma API Web em execução com base no aplicativo de exemplo para o tópico <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">criar uma API Web com ASP.NET Core</a> , que, por padrão, usa a mesma porta HTTPS (5001) que o aplicativo de exemplo mais incrivelmente. Para usar ambos os aplicativos no mesmo computador ao mesmo tempo, altere a porta da API Web (por exemplo, use a porta 10000). O aplicativo de exemplo faz solicitações para a API da Web em `https://localhost:10000/api/TodoItems`. Se um endereço da API Web diferente for usado, atualize o valor constante `ServiceEndpoint` no bloco `@code` do componente Razor.</p>

O aplicativo de exemplo faz uma solicitação de <a href="https://docs.microsoft.com/aspnet/core/security/cors">compartilhamento de recursos entre origens (CORS)</a> de `http://localhost:5000` ou `https://localhost:5001` para a API da Web. As credenciais (cookies/cabeçalhos de autorização) são permitidas. Adicione a seguinte configuração de middleware CORS ao método `Startup.Configure` da API Web:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajuste os domínios e as portas de `WithOrigins` conforme necessário para o aplicativo mais Altova.

A API da Web é configurada para CORS para permitir cookies/cabeçalhos de autorização e solicitações do código do cliente, mas a API da Web criada pelo tutorial não autoriza, de fato, solicitações. Consulte os <a href="https://docs.microsoft.com/aspnet/core/security/">artigos ASP.NET Core segurança e identidade</a> para obter diretrizes de implementação.
