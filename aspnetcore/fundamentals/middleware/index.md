---
title: Middleware do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre o middleware do ASP.NET Core e o pipeline de solicitação.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
---
# <a name="aspnet-core-middleware"></a>Middleware do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)

O middleware é um software montado em um pipeline de aplicativo para manipular solicitações e respostas. Cada componente:

* Escolhe se deseja passar a solicitação para o próximo componente no pipeline.
* Pode executar o trabalho antes e depois do próximo componente no pipeline.

Os delegados de solicitação são usados para criar o pipeline de solicitação. Os delegados de solicitação manipulam cada solicitação HTTP.

Os delegados de solicitação são configurados usando os métodos de extensão <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> e <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. Um delegado de solicitação individual pode ser especificado em linha como um método anônimo (chamado do middleware em linha) ou pode ser definido em uma classe reutilizável. Essas classes reutilizáveis e os métodos anônimos em linha são o *middleware*, também chamado de *componentes do middleware*. Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito do pipeline. Quando um middleware causa um curto-circuito, ele é chamado de *middleware terminal*, porque impede que outros middlewares processem a solicitação.

<xref:migration/http-modules> explica a diferença entre pipelines de solicitação no ASP.NET Core e no ASP.NET 4.x e fornece mais exemplos do middleware.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Criar um pipeline do middleware com o IApplicationBuilder

O pipeline de solicitação do ASP.NET Core consiste em uma sequência de delegados de solicitação, chamados um após o outro. O diagrama a seguir demonstra o conceito. O thread de execução segue as setas pretas.

![Padrão de processamento de solicitação mostrando a chegada de uma solicitação, processada por meio de três middlewares e a resposta que sai do aplicativo. Cada middleware executa sua lógica e transmite a solicitação para o próximo middleware na instrução next(). Depois que o terceiro middleware processa a solicitação, ela é transmitida por meio dos dois middlewares anteriores na ordem inversa para processamento adicional após suas instruções next(), antes de deixar o aplicativo como uma resposta ao cliente.](index/_static/request-delegate-pipeline.png)

Cada delegado pode executar operações antes e depois do próximo delegado. Os delegados de tratamento de exceção devem ser chamados no início do pipeline para que possam detectar exceções que ocorrem em etapas posteriores do pipeline.

O aplicativo ASP.NET Core mais simples possível define um delegado de solicitação única que controla todas as solicitações. Este caso não inclui um pipeline de solicitação real. Em vez disso, uma única função anônima é chamada em resposta a cada solicitação HTTP.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

O primeiro delegado <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> encerra o pipeline.

Encadeie vários delegados de solicitação junto com o <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>. O parâmetro `next` representa o próximo delegado no pipeline. Lembre-se de que você pode causar um curto-circuito no pipeline ao *não* chamar o parâmetro *next*. Normalmente, você pode executar ações antes e depois do próximo delegado, conforme o exemplo a seguir demonstra:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

Quando um delegado não transmite uma solicitação ao próximo delegado, considera-se que ele esteja *causando um curto-circuito do pipeline de solicitação*. O curto-circuito geralmente é desejável porque ele evita trabalho desnecessário. Por exemplo, o [Middleware de Arquivo Estático](xref:fundamentals/static-files) pode agir com um *middleware terminal*, processando uma solicitação para um arquivo estático e causar curto-circuito no restante do pipeline. O middleware adicionado ao pipeline antes do middleware que finaliza o processamento adicional ainda processa o código após as instruções `next.Invoke`. No entanto, confira o seguinte aviso sobre a tentativa de gravar em uma resposta que já foi enviada.

> [!WARNING]
> Não chame `next.Invoke` depois que a resposta tiver sido enviada ao cliente. Altera para <xref:Microsoft.AspNetCore.Http.HttpResponse> depois de a resposta ser iniciada e lança uma exceção. Por exemplo, mudanças como a configuração de cabeçalhos e o código de status lançam uma exceção. Gravar no corpo da resposta após a chamada `next`:
>
> * Pode causar uma violação do protocolo. Por exemplo, gravar mais do que o `Content-Length` indicado.
> * Pode corromper o formato do corpo. Por exemplo, gravar um rodapé HTML em um arquivo CSS.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> é uma dica útil para indicar se os cabeçalhos foram enviados ou o corpo foi gravado.

## <a name="order"></a>Pedido

A ordem em que os componentes do middleware são adicionados ao método `Startup.Configure` define a ordem em que os componentes de middleware são invocados nas solicitações e a ordem inversa para a resposta. A ordem é crítica para a segurança, o desempenho e a funcionalidade.

O método `Startup.Configure` a seguir adiciona componentes de middleware para cenários de aplicativo comuns:

::: moniker range=">= aspnetcore-2.0"

1. Exceção/tratamento de erro
1. Protocolo HTTP Strict Transport Security
1. Redirecionamento para HTTPS
1. Servidor de arquivos estático
1. Imposição de política de cookies
1. Autenticação
1. Session
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. Exceção/tratamento de erro
1. Arquivos estáticos
1. Autenticação
1. Session
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

No código de exemplo anterior, cada método de extensão de middleware é exposto em <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> por meio do namespace <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> é o primeiro componente de middleware adicionado ao pipeline. Portanto, o middleware de manipulador de exceção captura todas as exceções que ocorrem em chamadas posteriores.

O Middleware de Arquivo Estático é chamado no início do pipeline para que possa controlar as solicitações e causar o curto-circuito sem passar pelos componentes restantes. O Middleware de Arquivo Estático não fornece **nenhuma** verificação de autorização. Todos os arquivos atendidos, incluindo aqueles em *wwwroot*, estão disponíveis publicamente. Para conhecer uma abordagem para proteger arquivos estáticos, veja <xref:fundamentals/static-files>.

::: moniker range=">= aspnetcore-2.0"

Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Autenticação (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), que executa a autenticação. A autenticação causa curto-circuito em solicitações não autenticadas. Embora o middleware de autenticação autentique as solicitações, a autorização (e a rejeição) ocorre somente depois que o MVC seleciona uma Página Razor específica ou um controlador MVC e uma ação.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Se a solicitação não for controlada pelo Middleware de Arquivo Estático, ela será transmitida para o Middleware de Identidade (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), que executará a autenticação. A identidade não liga as solicitações não autenticadas em curto-circuito. Embora a identidade autentique as solicitações, a autorização (e a rejeição) ocorre somente depois que o MVC seleciona um controlador específico e uma ação.

::: moniker-end

O exemplo a seguir demonstra uma solicitação de middleware cujas solicitações de arquivos estáticos são manipuladas pelo Middleware de Arquivo Estático antes do Middleware de Compactação de Resposta. Arquivos estáticos não são compactados com este pedido de middleware. As respostas do MVC de <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> podem ser compactadas.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a>Use, Run e Map

Configure o pipeline de HTTP usando `Use`, `Run` e `Map`. O método `Use` pode ligar o pipeline em curto-circuito (ou seja, se ele não chamar um delegado de solicitação `next`). `Run` é uma convenção e alguns componentes de middleware podem expor os métodos `Run[Middleware]` que são executados no final do pipeline.

As extensões <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> são usadas como uma convenção de ramificação do pipeline. `Map*` ramifica o pipeline de solicitação com base na correspondência do caminho da solicitação em questão. Se o caminho da solicitação iniciar com o caminho especificado, o branch será executado.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.

| Solicitação             | Resposta                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Saudação do delegado diferente de Map. |
| localhost:1234/map1 | Teste de Map 1                   |
| localhost:1234/map2 | Teste de Map 2                   |
| localhost:1234/map3 | Saudação do delegado diferente de Map. |

Quando `Map` é usado, os segmentos de caminho correspondentes são removidos do `HttpRequest.Path` e anexados em `HttpRequest.PathBase` para cada solicitação.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) ramifica o pipeline de solicitação com base no resultado do predicado em questão. Qualquer predicado do tipo `Func<HttpContext, bool>` pode ser usado para mapear as solicitações para um novo branch do pipeline. No exemplo a seguir, um predicado é usado para detectar a presença de uma variável de cadeia de caracteres de consulta `branch`:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

A tabela a seguir mostra as solicitações e as respostas de `http://localhost:1234` usando o código anterior.

| Solicitação                       | Resposta                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Saudação do delegado diferente de Map. |
| localhost:1234/?branch=master | Branch usado = mestre         |

`Map` é compatível com aninhamento, por exemplo:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

`Map` também pode ser correspondido com vários segmentos de uma vez:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Middleware interno

O ASP.NET Core é fornecido com os seguintes componentes de middleware. A coluna *Ordem* fornece observações sobre o posicionamento do middleware no pipeline de processamento da solicitação e sob quais condições o middleware podem encerrar o processamento da solicitação. Quando um middleware causa um curto-circuito na solicitação ao processar o pipeline e impede outros middleware downstream de processar uma solicitação, ele é chamado de *middleware terminal*. Para saber mais sobre curto-circuito, confira a seção [Criar um pipeline de middleware com o IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Descrição | Pedido |
| ---------- | ----------- | ----- |
| [Autenticação](xref:security/authentication/identity) | Fornece suporte à autenticação. | Antes de `HttpContext.User` ser necessário. Terminal para retornos de chamada OAuth. |
| [Política de cookies](xref:security/gdpr) | Acompanha o consentimento dos usuários para o armazenamento de informações pessoais e impõe padrões mínimos para campos de cookie, tais como `secure` e `SameSite`. | Antes do middleware que emite cookies. Exemplos: autenticação, sessão e MVC (TempData). |
| [CORS](xref:security/cors) | Configura o Compartilhamento de Recursos entre Origens. | Antes de componentes que usam o CORS. |
| [Tratamento de Exceção](xref:fundamentals/error-handling) | Trata exceções. | Antes dos componentes que geram erros. |
| [Cabeçalhos encaminhados](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Encaminha cabeçalhos como proxy para a solicitação atual. | Antes dos componentes que consomem os campos atualizados. Exemplos: esquema, host, IP do cliente e método. |
| [Verificações de integridade](xref:host-and-deploy/health-checks) | Verifica a integridade de um aplicativo ASP.NET Core e suas dependências, como a verificação da disponibilidade do banco de dados. | Terminal, se uma solicitação corresponde a um ponto de extremidade da verificação de integridade. |
| [Substituição do Método HTTP](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Permite que uma solicitação de entrada POST substitua o método. | Antes dos componentes que consomem o método atualizado. |
| [Redirecionamento de HTTPS](xref:security/enforcing-ssl#require-https) | Redirecione todas as solicitações HTTP para HTTPS (ASP.NET Core 2.1 ou posterior). | Antes dos componentes que consomem a URL. |
| [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware de aprimoramento de segurança que adiciona um cabeçalho de resposta especial (ASP.NET Core 2.1 ou posterior). | Antes das respostas serem enviadas e depois dos componentes que modificam solicitações. Exemplos: cabeçalhos encaminhados, regravação de URL. |
| [MVC](xref:mvc/overview) | Processa as solicitações de Razor Pages/MVC (ASP.NET Core 2.0 ou posterior). | Terminal, se uma solicitação corresponder a uma rota. |
| [OWIN](xref:fundamentals/owin) | Interoperabilidade com aplicativos baseados em OWIN, em servidores e em middleware. | Terminal, se o middleware OWIN processa totalmente a solicitação. |
| [Cache de resposta](xref:performance/caching/middleware) | Fornece suporte para as respostas em cache. | Antes dos componentes que exigem armazenamento em cache. |
| [Compactação de resposta](xref:performance/response-compression) | Fornece suporte para a compactação de respostas. | Antes dos componentes que exigem compactação. |
| [Localização de Solicitação](xref:fundamentals/localization) | Fornece suporte à localização. | Antes dos componentes de localização importantes. |
| [Roteamento](xref:fundamentals/routing) | Define e restringe as rotas de solicitação. | Terminal de rotas correspondentes. |
| [Sessão](xref:fundamentals/app-state) | Fornece suporte para gerenciar sessões de usuário. | Antes de componentes que exigem a sessão. |
| [Arquivos estáticos](xref:fundamentals/static-files) | Fornece suporte para servir arquivos estáticos e pesquisa no diretório. | Terminal, se uma solicitação corresponde a um arquivo. |
| [Regravação de URL](xref:fundamentals/url-rewriting) | Fornece suporte para regravar URLs e redirecionar solicitações. | Antes dos componentes que consomem a URL. |
| [WebSockets](xref:fundamentals/websockets) | Habilita o protocolo WebSockets. | Antes dos componentes que são necessários para aceitar solicitações de WebSocket. |

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
