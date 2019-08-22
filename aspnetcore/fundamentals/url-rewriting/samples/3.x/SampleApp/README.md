# <a name="aspnet-core-url-rewriting-sample"></a>Amostra de Reconfiguração de URL do ASP.NET Core

Esta amostra ilustra o uso do Middleware de Reconfiguração de URL do ASP.NET Core. O aplicativo demonstra as opções de redirecionamento e de reconfiguração de URL.

Durante a execução da amostra, as respostas que não são de arquivo retornam a URL reconfigurada ou redirecionada quando uma das regras é aplicada a uma URL de solicitação. Para os exemplos de XML e arquivo de texto, o Middleware de Arquivo Estático fornece o arquivo depois que a URL de solicitação é reconfigurada pelo middleware.

## <a name="examples-in-this-sample"></a>Exemplos desta amostra

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Código de status de êxito: 302 (Encontrado)
  - Exemplo (redirecionamento): **/redirect-rule/{capture_group}** para **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Código de status de êxito: 200 (OK)
  - Exemplo (reconfiguração): **/rewrite-rule/{capture_group_1}/{capture_group_2}** para **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Código de status de êxito: 302 (Encontrado)
  - Exemplo (redirecionamento): **/apache-mod-rules-redirect/{capture_group}** para **/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Código de status de êxito: 200 (OK)
  - Exemplo (reconfiguração): **/iis-rules-rewrite/{capture_group}** para **/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Código de status de êxito: 301 (Movido Permanentemente)
  - Exemplo (redirecionamento): **/file.xml** para **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Código de status de êxito: 200 (OK)
  - Exemplo (reconfiguração): **/some_file.txt** para **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Código de status de êxito: 301 (Movido Permanentemente)
  - Exemplo (redirecionamento): **/image.png** para **/png-images/image.png**
  - Exemplo (redirecionamento): **/image.jpg** para **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Usar um PhysicalFileProvider

Também obtenha um `IFileProvider` criando um `PhysicalFileProvider` para passar para os métodos `AddApacheModRewrite()` e `AddIISUrlRewrite()`:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Proteger extensões de redirecionamento

Esta amostra inclui a configuração `WebHostBuilder` para o aplicativo usar URLs (`https://localhost:5001`, `https://localhost`) e um certificado de teste (*testCert.pfx*) para ajudá-lo a explorar os métodos de redirecionamento seguros. Se o servidor já tiver a porta 443 atribuída ou em uso, o exemplo `https://localhost` não funcionará, remova o `ListenOptions` para a porta 443 no método `CreateWebHostBuilder` do arquivo *Program.cs* ou desassocie a porta 443 no servidor para que o Kestrel possa usar a porta.

| Método                           | Código de status |    Porta    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | nulo (465) |
| `.AddRedirectToHttps()`          |     302     | nulo (465) |
| `.AddRedirectToHttps(301)`       |     301     | nulo (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
