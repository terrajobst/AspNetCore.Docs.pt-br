---
title: Verificações de integridade no ASP.NET Core
author: guardrex
description: Saiba como configurar verificações de integridade para a infraestrutura do ASP.NET Core, como aplicativos e bancos de dados.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/13/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 4a4606a58178018f0d71d467d4c8b6c9982c09dc
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115999"
---
# <a name="health-checks-in-aspnet-core"></a>Verificações de integridade no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) e [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

O ASP.NET Core oferece middleware de verificações de integridade e bibliotecas para relatar a integridade dos componentes de infraestrutura de aplicativo.

As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP. Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:

* As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo. Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner. Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.
* O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.
* As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico. Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando. Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.

## <a name="prerequisites"></a>Prerequisites

As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo. Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado. O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.

O pacote [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) é referenciado implicitamente para aplicativos ASP.NET Core. Para executar verificações de integridade usando Entity Framework Core, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .

O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários. O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core. Para explorar os cenários de banco de dados, o aplicativo de exemplo:

* Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.
* Tem as seguintes referências de pacote em seu arquivo de projeto:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento. O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento. Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).

## <a name="basic-health-probe"></a>Investigação de integridade básica

Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.

A configuração básica registra serviços de verificação de integridade e chama o middleware de verificações de integridade para responder em um ponto de extremidade de URL com uma resposta de integridade. Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico. O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade. O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. Crie um ponto de extremidade de verificação de integridade chamando `MapHealthChecks` em `Startup.Configure`.

No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemplo do Docker

O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Criar verificações de integridade

As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`. O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.

A classe de `ExampleHealthCheck` a seguir demonstra o layout de uma verificação de integridade. A lógica de verificações de integridade é colocada no método `CheckHealthAsync`. O exemplo a seguir define uma variável fictícia, `healthCheckResultHealthy`, para `true`. Se o valor de `healthCheckResultHealthy` for definido como `false`, o [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status não íntegro será retornado.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Registrar os serviços de verificação de integridade

O tipo de `ExampleHealthCheck` é adicionado aos serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha. Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado. Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.

*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda. No seguinte exemplo, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Chame <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> para passar argumentos para uma implementação de verificação de integridade. No exemplo a seguir, `TestHealthCheckWithArgs` aceita um inteiro e uma cadeia de caracteres para uso quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> é chamado:

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs` é registrado chamando `AddTypeActivatedCheck` com o inteiro e a cadeia de caracteres passada para a implementação:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Usar roteamento de verificações de integridade

No `Startup.Configure`, chame `MapHealthChecks` no construtor de ponto de extremidade com a URL do ponto de extremidade ou o caminho relativo:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Exigir host

Chame `RequireHost` para especificar um ou mais hosts permitidos para o ponto de extremidade de verificação de integridade. Os hosts devem ser Unicode em vez de Punycode e podem incluir uma porta. Se uma coleção não for fornecida, qualquer host será aceito.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).

### <a name="require-authorization"></a>Exigir autorização

Chame `RequireAuthorization` para executar o middleware de autorização no ponto de extremidade de solicitação de verificação de integridade. Uma sobrecarga de `RequireAuthorization` aceita uma ou mais políticas de autorização. Se uma política não for fornecida, a política de autorização padrão será usada.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Habilitar o CORS (Solicitações Entre Origens)

Embora a execução manual de verificações de integridade de um navegador não seja um cenário de uso comum, o middleware CORS pode ser habilitado chamando `RequireCors` nos pontos de extremidade de verificações de integridade. Uma sobrecarga de `RequireCors` aceita um delegado de construtor de política CORS (`CorsPolicyBuilder`) ou um nome de política. Se uma política não for fornecida, a política CORS padrão será usada. Para obter mais informações, consulte <xref:security/cors>.

## <a name="health-check-options"></a>Opções de verificação de integridade

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:

* [Filtrar verificações de integridade](#filter-health-checks)
* [Personalizar o código de status HTTP](#customize-the-http-status-code)
* [Suprimir os cabeçalhos de cache](#suppress-cache-headers)
* [Personalizar a saída](#customize-output)

### <a name="filter-health-checks"></a>Filtrar verificações de integridade

Por padrão, o middleware de verificações de integridade executa todas as verificações de integridade registradas. Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:

No `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

No `Startup.Configure`, o `Predicate` filtra a verificação de integridade da ' barra '. Somente foo e Baz são executados.:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>Personalizar o código de status HTTP

Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP. As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware. Altere os valores do código de status de acordo com suas necessidades.

No `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Suprimir os cabeçalhos de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controla se o middleware de verificações de integridade adiciona cabeçalhos HTTP a uma resposta de investigação para evitar o cache de resposta. Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta. Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.

No `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Personalizar a saída

A opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtém ou define um representante usado para gravar a resposta.

No `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). O seguinte delegado personalizado, `WriteResponse`, gera uma resposta JSON personalizada:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

O sistema de verificações de integridade não fornece suporte interno para formatos de retorno JSON complexos porque o formato é específico à sua escolha de sistema de monitoramento. Fique à vontade para personalizar o `JObject` no exemplo anterior, conforme necessário, para atender às suas necessidades.

## <a name="database-probe"></a>Investigação de banco de dados

Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.

O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server. O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.

> [!WARNING]
> Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente. A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho. Na maioria dos casos, a execução de uma consulta de teste não é necessária. É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados. Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.

Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo. O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Investigação de DbContext do Entity Framework Core

A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core. A verificação `DbContext` é compatível em aplicativos que:

* Usam o [EF (Entity Framework) Core](/ef/core/).
* Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`. O `DbContext` é fornecido como o `TContext` para o método. Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.

Por padrão:

* o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core. Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.
* O nome da verificação de integridade é o nome do tipo `TContext`.

No aplicativo de exemplo, `AppDbContext` é fornecida para `AddDbContextCheck` e registrada como um serviço no `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server. Se o banco de dados existir, exclua-o.

Execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario dbcontext
```

Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador. O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:

```
Unhealthy
```

Dispare o aplicativo de exemplo para criar o banco de dados. Faça uma solicitação para `/createdatabase`. O aplicativo responde:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Faça uma solicitação ao ponto de extremidade `/health`. O banco de dados e o contexto existem e, portanto, o aplicativo responde:

```
Healthy
```

Dispare o aplicativo de exemplo para excluir o banco de dados. Faça uma solicitação para `/deletedatabase`. O aplicativo responde:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Faça uma solicitação ao ponto de extremidade `/health`. O aplicativo fornece uma resposta não íntegra:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Investigações de preparação e atividade separadas

Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:

* O aplicativo está funcionando, mas ainda não está pronto para receber solicitações. Esse estado é a *preparação* do aplicativo.
* O aplicativo está funcionando e respondendo a solicitações. Esse estado é a *atividade* do aplicativo.

A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis. Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações. Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.

O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services). A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado. Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`. No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em:

* `/health/ready` para a verificação de preparação. A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.
* `/health/live` para a verificação de tempo de vida. A verificação de vida filtra a `StartupHostedServiceHealthCheck` retornando `false` no [predicado HealthCheckOptions.](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, consulte [Filtrar verificações de integridade](#filter-health-checks))

No código de exemplo a seguir:

* A verificação de preparação usa todas as verificações registradas com a marca ' Ready '.
* O `Predicate` exclui todas as verificações e retorna um 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario liveness
```

Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos. A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos. Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.

Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos. Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemplo do Kubernetes

O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/). No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente. O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado. Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.

O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Investigação baseada em métrica com um gravador de resposta personalizada

O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.

`MemoryHealthCheck` relata um estado degradado se o aplicativo usa mais de determinado limite de memória (1 GB no aplicativo de exemplo). O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço. Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware. Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.

No aplicativo de exemplo (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`. Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="filter-by-port"></a>Filtrar por porta

Chame `RequireHost` em `MapHealthChecks` com um padrão de URL que especifica uma porta para restringir as solicitações de verificação de integridade à porta especificada. Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.

O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider). A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente. Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.

Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.

O arquivo de *Propriedades/launchSettings. JSON* a seguir no aplicativo de exemplo não está incluído nos arquivos de projeto do aplicativo de exemplo e deve ser criado manualmente:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. Crie um ponto de extremidade de verificação de integridade chamando `MapHealthChecks` em `Startup.Configure`.

No aplicativo de exemplo, uma chamada para `RequireHost` no ponto de extremidade em `Startup.Configure` especifica a porta de gerenciamento da configuração:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Os pontos de extremidade são criados no aplicativo de exemplo no `Startup.Configure`. No código de exemplo a seguir:

* A verificação de preparação usa todas as verificações registradas com a marca ' Ready '.
* O `Predicate` exclui todas as verificações e retorna um 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Você pode evitar criar o arquivo *launchSettings. JSON* no aplicativo de exemplo definindo a porta de gerenciamento explicitamente no código. Em *Program.cs* em que o <xref:Microsoft.Extensions.Hosting.HostBuilder> é criado, adicione uma chamada para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> e forneça o ponto de extremidade da porta de gerenciamento do aplicativo. Em `Configure` de *ManagementPortStartup.cs*, especifique a porta de gerenciamento com `RequireHost`:
>
> *Program.cs*:
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuir uma biblioteca de verificação de integridade

Para distribuir uma verificação de integridade como uma biblioteca:

1. Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma. A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.

   Na lógica de verificações de integridade de `CheckHealthAsync`:

   * `data1` e `data2` são usados no método para executar a lógica de verificação de integridade da investigação.
   * `AccessViolationException` é manipulado.

   Quando ocorre uma <xref:System.AccessViolationException>, a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é retornada com a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> para permitir que os usuários configurem o status de falha de verificações de integridade.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`. No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade. Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão. No seguinte exemplo, o chamador especifica itens opcionais:

   * nome da verificação de integridade (`name`). Se `null`, `example_health_check` é usado.
   * ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).
   * ponto de dados de inteiro para a verificação de integridade (`data2`). Se `null`, `1` é usado.
   * status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). O padrão é `null`. Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.
   * marcas (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Publicador de Verificação de Integridade

Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado. Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.

A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; O atraso inicial aplicado após o aplicativo começar a executar instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes. O valor padrão é cinco segundos.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; O período da execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O valor padrão é 30 segundos.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> for `null` (padrão), o serviço publicador de verificação de integridade executará todas as verificações de integridade registradas. Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações. O predicado é avaliado a cada período.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; O tempo limite para a execução de verificações de integridade em todas as instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite. O valor padrão é 30 segundos.

No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O status da verificação de integridade é registrado para cada verificação em um nível de log de:

* Informações (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) se o status das verificações de integridade for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Erro (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) se o status for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos. Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restringir verificações de integridade com MapWhen

Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.

No exemplo a seguir, `MapWhen` ramifica o pipeline de solicitação para ativar o middleware de verificações de integridade se uma solicitação GET for recebida para o ponto de extremidade de `api/HealthCheck`:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O ASP.NET Core oferece middleware de verificações de integridade e bibliotecas para relatar a integridade dos componentes de infraestrutura de aplicativo.

As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP. Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:

* As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo. Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner. Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.
* O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.
* As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))

O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico. Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando. Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.

## <a name="prerequisites"></a>Prerequisites

As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo. Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado. O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.

Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).

O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários. O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core. Para explorar os cenários de banco de dados, o aplicativo de exemplo:

* Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.
* Tem as seguintes referências de pacote em seu arquivo de projeto:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento. O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento. Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).

## <a name="basic-health-probe"></a>Investigação de integridade básica

Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.

A configuração básica registra serviços de verificação de integridade e chama o middleware de verificações de integridade para responder em um ponto de extremidade de URL com uma resposta de integridade. Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico. O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade. O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. Adicione um ponto de extremidade para o middleware de verificações de integridade com <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> no pipeline de processamento de solicitação de `Startup.Configure`.

No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Exemplo do Docker

O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Criar verificações de integridade

As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`. O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.

### <a name="example-health-check"></a>Verificação de integridade de exemplo

A classe de `ExampleHealthCheck` a seguir demonstra o layout de uma verificação de integridade. A lógica de verificações de integridade é colocada no método `CheckHealthAsync`. O exemplo a seguir define uma variável fictícia, `healthCheckResultHealthy`, para `true`. Se o valor de `healthCheckResultHealthy` for definido como `false`, o [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status não íntegro será retornado.

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Registrar os serviços de verificação de integridade

O tipo de `ExampleHealthCheck` é adicionado aos serviços de verificação de integridade em `Startup.ConfigureServices` com <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha. Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado. Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.

*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda. No exemplo a seguir `Startup.ConfigureServices`, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Usar o Middleware de Verificações de Integridade

Em `Startup.Configure`, chame <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> do pipeline de processamento com a URL de ponto de extremidade ou o caminho relativo:

```csharp
app.UseHealthChecks("/health");
```

Se as verificações de integridade precisarem escutar uma porta específica, use uma sobrecarga igual a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> para definir a porta (descrito posteriormente na seção [Filtrar por porta](#filter-by-port)):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Opções de verificação de integridade

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:

* [Filtrar verificações de integridade](#filter-health-checks)
* [Personalizar o código de status HTTP](#customize-the-http-status-code)
* [Suprimir os cabeçalhos de cache](#suppress-cache-headers)
* [Personalizar a saída](#customize-output)

### <a name="filter-health-checks"></a>Filtrar verificações de integridade

Por padrão, o middleware de verificações de integridade executa todas as verificações de integridade registradas. Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>. No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Personalizar o código de status HTTP

Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP. As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware. Altere os valores do código de status de acordo com suas necessidades.

No `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Suprimir os cabeçalhos de cache

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controla se o middleware de verificações de integridade adiciona cabeçalhos HTTP a uma resposta de investigação para evitar o cache de resposta. Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta. Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.

No `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Personalizar a saída

A opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtém ou define um representante usado para gravar a resposta. O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

No `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). O seguinte delegado personalizado, `WriteResponse`, gera uma resposta JSON personalizada:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

O sistema de verificações de integridade não fornece suporte interno para formatos de retorno JSON complexos porque o formato é específico à sua escolha de sistema de monitoramento. Fique à vontade para personalizar o `JObject` no exemplo anterior, conforme necessário, para atender às suas necessidades.

## <a name="database-probe"></a>Investigação de banco de dados

Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.

O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server. O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.

> [!WARNING]
> Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente. A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho. Na maioria dos casos, a execução de uma consulta de teste não é necessária. É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados. Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.

Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo. O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

O middleware de verificações de integridade de chamada no pipeline de processamento de aplicativo no `Startup.Configure`:

```csharp
app.UseHealthChecks("/health");
```

Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Investigação de DbContext do Entity Framework Core

A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core. A verificação `DbContext` é compatível em aplicativos que:

* Usam o [EF (Entity Framework) Core](/ef/core/).
* Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`. O `DbContext` é fornecido como o `TContext` para o método. Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.

Por padrão:

* o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core. Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.
* O nome da verificação de integridade é o nome do tipo `TContext`.

No aplicativo de exemplo, `AppDbContext` é fornecida para `AddDbContextCheck` e registrada como um serviço no `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

No aplicativo de exemplo, `UseHealthChecks` adiciona o middleware de verificações de integridade em `Startup.Configure`.

```csharp
app.UseHealthChecks("/health");
```

Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server. Se o banco de dados existir, exclua-o.

Execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario dbcontext
```

Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador. O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:

```
Unhealthy
```

Dispare o aplicativo de exemplo para criar o banco de dados. Faça uma solicitação para `/createdatabase`. O aplicativo responde:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Faça uma solicitação ao ponto de extremidade `/health`. O banco de dados e o contexto existem e, portanto, o aplicativo responde:

```
Healthy
```

Dispare o aplicativo de exemplo para excluir o banco de dados. Faça uma solicitação para `/deletedatabase`. O aplicativo responde:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Faça uma solicitação ao ponto de extremidade `/health`. O aplicativo fornece uma resposta não íntegra:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Investigações de preparação e atividade separadas

Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:

* O aplicativo está funcionando, mas ainda não está pronto para receber solicitações. Esse estado é a *preparação* do aplicativo.
* O aplicativo está funcionando e respondendo a solicitações. Esse estado é a *atividade* do aplicativo.

A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis. Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações. Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.

O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services). A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado. Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Chame o middleware de verificações de integridade no pipeline de processamento de aplicativos no `Startup.Configure`. No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em `/health/ready` para a verificação de preparação e em `/health/live` para a verificação de atividade. A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`. A verificação de atividade filtra a `StartupHostedServiceHealthCheck` retornando `false` no [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, confira [Filtrar verificações de integridade](#filter-health-checks)):

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario liveness
```

Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos. A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos. Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.

Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos. Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).

### <a name="kubernetes-example"></a>Exemplo do Kubernetes

O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/). No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente. O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado. Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.

O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Investigação baseada em métrica com um gravador de resposta personalizada

O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.

`MemoryHealthCheck` relatará um status não íntegro se o aplicativo usar mais do que um determinado limite de memória (1 GB no aplicativo de exemplo). O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço. Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware. Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.

No aplicativo de exemplo (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Chame o middleware de verificações de integridade no pipeline de processamento de aplicativos no `Startup.Configure`. Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="filter-by-port"></a>Filtrar por porta

A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> com uma porta restringe as solicitações de verificação de integridade à porta especificada. Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.

O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider). A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente. Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.

Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.

O arquivo de *Propriedades/launchSettings. JSON* a seguir no aplicativo de exemplo não está incluído nos arquivos de projeto do aplicativo de exemplo e deve ser criado manualmente:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`. A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> especifica a porta de gerenciamento (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Evite a criação do arquivo *launchSettings.json* no aplicativo de exemplo definindo as URLs e a porta de gerenciamento explicitamente no código. Em *Program.cs*, em que o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> é criado, adicione uma chamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> e forneça o ponto de extremidade da resposta normal do aplicativo e o ponto de extremidade da porta de gerenciamento. Em *ManagementPortStartup.cs*, em que <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> é chamado, especifique a porta de gerenciamento explicitamente.
>
> *Program.cs*:
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuir uma biblioteca de verificação de integridade

Para distribuir uma verificação de integridade como uma biblioteca:

1. Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma. A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.

   Na lógica de verificações de integridade de `CheckHealthAsync`:

   * `data1` e `data2` são usados no método para executar a lógica de verificação de integridade da investigação.
   * `AccessViolationException` é manipulado.

   Quando ocorre uma <xref:System.AccessViolationException>, a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é retornada com a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> para permitir que os usuários configurem o status de falha de verificações de integridade.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`. No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade. Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão. No seguinte exemplo, o chamador especifica itens opcionais:

   * nome da verificação de integridade (`name`). Se `null`, `example_health_check` é usado.
   * ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).
   * ponto de dados de inteiro para a verificação de integridade (`data2`). Se `null`, `1` é usado.
   * status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). O padrão é `null`. Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.
   * marcas (`IEnumerable<string>`).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Publicador de Verificação de Integridade

Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado. Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.

A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; O atraso inicial aplicado após o aplicativo começar a executar instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes. O valor padrão é cinco segundos.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; O período da execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O valor padrão é 30 segundos.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> for `null` (padrão), o serviço publicador de verificação de integridade executará todas as verificações de integridade registradas. Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações. O predicado é avaliado a cada período.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; O tempo limite para a execução de verificações de integridade em todas as instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite. O valor padrão é 30 segundos.

> [!WARNING]
> Na versão para ASP.NET Core 2.2, a configuração de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> não é respeitada pela implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>; ela define o valor de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Esse problema foi resolvido no ASP.NET Core 3,0.

No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. O status da verificação de integridade é registrado para cada verificação:

* Informações (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) se o status das verificações de integridade for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Erro (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) se o status for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos. Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> A solução alternativa a seguir permite a adição de uma instância <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ao contêiner de serviço quando um ou mais serviços hospedados já tiverem sido adicionados ao aplicativo. Essa solução alternativa não será necessária no ASP.NET Core 3,0.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Restringir verificações de integridade com MapWhen

Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.

No exemplo a seguir, `MapWhen` ramifica o pipeline de solicitação para ativar o middleware de verificações de integridade se uma solicitação GET for recebida para o ponto de extremidade de `api/HealthCheck`:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
