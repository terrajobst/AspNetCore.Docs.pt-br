---
title: Verificações de integridade no ASP.NET Core
author: guardrex
description: Saiba como configurar verificações de integridade para a infraestrutura do ASP.NET Core, como aplicativos e bancos de dados.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/12/2018
uid: host-and-deploy/health-checks
ms.openlocfilehash: cf2aea91221887dad5646604214f810493d4b175
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329140"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="34650-103">Verificações de integridade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34650-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="34650-104">Por [Luke Latham](https://github.com/guardrex) e [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="34650-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

<span data-ttu-id="34650-105">O ASP.NET Core oferece o Middleware de Verificação de Integridade e bibliotecas para relatar a integridade de componentes de infraestrutura do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-105">ASP.NET Core offers Health Check Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="34650-106">As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="34650-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="34650-107">Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:</span><span class="sxs-lookup"><span data-stu-id="34650-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="34650-108">As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="34650-109">Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner.</span><span class="sxs-lookup"><span data-stu-id="34650-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="34650-110">Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.</span><span class="sxs-lookup"><span data-stu-id="34650-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="34650-111">O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.</span><span class="sxs-lookup"><span data-stu-id="34650-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="34650-112">As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.</span><span class="sxs-lookup"><span data-stu-id="34650-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="34650-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34650-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="34650-114">O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="34650-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="34650-115">Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="34650-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="34650-116">Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="34650-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34650-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="34650-117">Prerequisites</span></span>

<span data-ttu-id="34650-118">As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="34650-119">Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado.</span><span class="sxs-lookup"><span data-stu-id="34650-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="34650-120">O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="34650-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="34650-121">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="34650-121">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="34650-122">O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="34650-122">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="34650-123">O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando o [BeatPulse](https://github.com/Xabaril/BeatPulse).</span><span class="sxs-lookup"><span data-stu-id="34650-123">The [database probe](#database-probe) scenario checks the health of a database connection using [BeatPulse](https://github.com/Xabaril/BeatPulse).</span></span> <span data-ttu-id="34650-124">O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="34650-124">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="34650-125">Para explorar os cenários de banco de dados, o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="34650-125">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="34650-126">Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="34650-126">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="34650-127">Tem as seguintes referências de pacote em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="34650-127">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="34650-128">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="34650-128">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="34650-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="34650-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="34650-130">O [BeatPulse](https://github.com/Xabaril/BeatPulse) não é mantido pela Microsoft nem tem o suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="34650-130">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="34650-131">Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="34650-131">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="34650-132">O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="34650-132">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="34650-133">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="34650-133">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="34650-134">Investigação de integridade básica</span><span class="sxs-lookup"><span data-stu-id="34650-134">Basic health probe</span></span>

<span data-ttu-id="34650-135">Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-135">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="34650-136">A configuração básica registra os serviços de verificação de integridade e chama o Middleware de Verificação de Integridade para responder a um ponto de extremidade de URL com uma resposta de integridade.</span><span class="sxs-lookup"><span data-stu-id="34650-136">The basic configuration registers health check services and calls the Health Check Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="34650-137">Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico.</span><span class="sxs-lookup"><span data-stu-id="34650-137">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="34650-138">O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade.</span><span class="sxs-lookup"><span data-stu-id="34650-138">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="34650-139">O gravador de resposta padrão grava o status (`HealthStatus`) como uma resposta de texto não criptografado no cliente, indicando um status `HealthStatus.Healthy`, `HealthStatus.Degraded` ou `HealthStatus.Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="34650-139">The default response writer writes the status (`HealthStatus`) as a plaintext response back to the client, indicating either a `HealthStatus.Healthy`, `HealthStatus.Degraded` or `HealthStatus.Unhealthy` status.</span></span>

<span data-ttu-id="34650-140">Registre os serviços de verificação de integridade com `AddHealthChecks` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34650-140">Register health check services with `AddHealthChecks` in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34650-141">Adicione o Middleware de Verificação de Integridade com `UseHealthChecks` ao pipeline de processamento de solicitação de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34650-141">Add Health Check Middleware with `UseHealthChecks` in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="34650-142">No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-142">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

<span data-ttu-id="34650-143">Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-143">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="34650-144">Exemplo do Docker</span><span class="sxs-lookup"><span data-stu-id="34650-144">Docker example</span></span>

<span data-ttu-id="34650-145">O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="34650-145">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="34650-146">Criar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-146">Create health checks</span></span>

<span data-ttu-id="34650-147">As verificações de integridade são criadas pela implementação da interface `IHealthCheck`.</span><span class="sxs-lookup"><span data-stu-id="34650-147">Health checks are created by implementing the `IHealthCheck` interface.</span></span> <span data-ttu-id="34650-148">O método `IHealthCheck.CheckHealthAsync` retorna um `Task<HealthCheckResult>` que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="34650-148">The `IHealthCheck.CheckHealthAsync` method returns a `Task<HealthCheckResult>` that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="34650-149">O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="34650-149">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="34650-150">`HealthCheckResult` também pode retornar pares chave-valor opcionais.</span><span class="sxs-lookup"><span data-stu-id="34650-150">`HealthCheckResult` can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="34650-151">Verificação de integridade de exemplo</span><span class="sxs-lookup"><span data-stu-id="34650-151">Example health check</span></span>

<span data-ttu-id="34650-152">A seguinte classe `ExampleHealthCheck` demonstra o layout de uma verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="34650-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check:</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public ExampleHealthCheck()
    {
        // Use dependency injection (DI) to supply any required services to the
        // health check.
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context, 
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Execute health check logic here. This example sets a dummy
        // variable to true.
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

### <a name="register-health-check-services"></a><span data-ttu-id="34650-153">Registrar os serviços de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-153">Register health check services</span></span>

<span data-ttu-id="34650-154">O tipo `ExampleHealthCheck` é adicionado aos serviços de verificação de integridade com `AddCheck`:</span><span class="sxs-lookup"><span data-stu-id="34650-154">The `ExampleHealthCheck` type is added to health check services with `AddCheck`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<span data-ttu-id="34650-155">A sobrecarga `AddCheck` mostrada no exemplo a seguir define o status de falha (`HealthStatus`) como relatório quando a verificação de integridade relata uma falha.</span><span class="sxs-lookup"><span data-stu-id="34650-155">The `AddCheck` overload shown in the following example sets the failure status (`HealthStatus`) to report when the health check reports a failure.</span></span> <span data-ttu-id="34650-156">Se o status de falha é definido como `null` (padrão), `HealthStatus.Unhealthy` é relatado.</span><span class="sxs-lookup"><span data-stu-id="34650-156">If the failure status is set to `null` (default), `HealthStatus.Unhealthy` is reported.</span></span> <span data-ttu-id="34650-157">Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.</span><span class="sxs-lookup"><span data-stu-id="34650-157">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="34650-158">*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="34650-158">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" });
```

<span data-ttu-id="34650-159">`AddCheck` também pode executar uma função lambda.</span><span class="sxs-lookup"><span data-stu-id="34650-159">`AddCheck` can also execute a lambda function.</span></span> <span data-ttu-id="34650-160">No seguinte exemplo, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:</span><span class="sxs-lookup"><span data-stu-id="34650-160">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () => 
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="34650-161">Usar o Middleware de Verificações de Integridade</span><span class="sxs-lookup"><span data-stu-id="34650-161">Use Health Checks Middleware</span></span>

<span data-ttu-id="34650-162">Em `Startup.Configure`, chame `UseHealthChecks` do pipeline de processamento com a URL de ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="34650-162">In `Startup.Configure`, call `UseHealthChecks` in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

<span data-ttu-id="34650-163">Se as verificações de integridade precisarem escutar uma porta específica, use uma sobrecarga igual a `UseHealthChecks` para definir a porta (descrito posteriormente na seção [Filtrar por porta](#filter-by-port)):</span><span class="sxs-lookup"><span data-stu-id="34650-163">If the health checks should listen on a specific port, use an overload of `UseHealthChecks` to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

<span data-ttu-id="34650-164">O Middleware de Verificações de Integridade é um *middleware de terminal* no pipeline de processamento de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-164">Health Checks Middleware is a *terminal middleware* in the app's request processing pipeline.</span></span> <span data-ttu-id="34650-165">O primeiro ponto de extremidade de verificação de integridade encontrado que seja uma correspondência exata da URL de solicitação é executado e causa curto-circuito no restante do pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="34650-165">The first health check endpoint encountered that's an exact match to the request URL executes and short-circuits the rest of the middleware pipeline.</span></span> <span data-ttu-id="34650-166">Quando ocorre o curto-circuito, nenhum middleware após a verificação de integridade correspondente é executado.</span><span class="sxs-lookup"><span data-stu-id="34650-166">When short-circuiting occurs, no middleware following the matched health check executes.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="34650-167">Opções de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-167">Health check options</span></span>

<span data-ttu-id="34650-168">`HealthCheckOptions` oferece uma oportunidade de personalizar o comportamento de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="34650-168">`HealthCheckOptions` provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="34650-169">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-169">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="34650-170">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="34650-170">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="34650-171">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="34650-171">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="34650-172">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="34650-172">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="34650-173">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-173">Filter health checks</span></span>

<span data-ttu-id="34650-174">Por padrão, o Middleware de Verificação de Integridade executa todas as verificações de integridade registradas.</span><span class="sxs-lookup"><span data-stu-id="34650-174">By default, Health Check Middleware runs all registered health checks.</span></span> <span data-ttu-id="34650-175">Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção `Predicate`.</span><span class="sxs-lookup"><span data-stu-id="34650-175">To run a subset of health checks, provide a function that returns a boolean to the `Predicate` option.</span></span> <span data-ttu-id="34650-176">No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade `Tag` da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="34650-176">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's `Tag` property matches `foo_tag` or `baz_tag`:</span></span>

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
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
        .AddCheck("Baz", () => 
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // Filter out the 'Bar' health check. Only Foo and Baz execute.
        Predicate = (check) => check.Tags.Contains("foo_tag") || 
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="34650-177">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="34650-177">Customize the HTTP status code</span></span>

<span data-ttu-id="34650-178">Use `ResultStatusCodes` para personalizar o mapeamento de status da integridade para códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="34650-178">Use `ResultStatusCodes` to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="34650-179">As atribuições `StatusCode` a seguir são os valores padrão usados pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="34650-179">The following `StatusCode` assignments are the default values used by the middleware.</span></span> <span data-ttu-id="34650-180">Altere os valores do código de status de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="34650-180">Change the status code values to meet your requirements.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The following StatusCodes are the default assignments for
        // the HealthStatus properties.
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
}
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="34650-181">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="34650-181">Suppress cache headers</span></span>

<span data-ttu-id="34650-182">`AllowCachingResponses` controla se o Middleware de Verificação de Integridade adiciona cabeçalhos HTTP a uma resposta de investigação para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="34650-182">`AllowCachingResponses` controls whether the Health Check Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="34650-183">Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="34650-183">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="34650-184">Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.</span><span class="sxs-lookup"><span data-stu-id="34650-184">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The default value is false.
        AllowCachingResponses = false
    });
}
```

### <a name="customize-output"></a><span data-ttu-id="34650-185">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="34650-185">Customize output</span></span>

<span data-ttu-id="34650-186">A opção `ResponseWriter` obtém ou define um representante usado para gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="34650-186">The `ResponseWriter` option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="34650-187">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres `HealthReport.Status`.</span><span class="sxs-lookup"><span data-stu-id="34650-187">The default delegate writes a minimal plaintext response with the string value of `HealthReport.Status`.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // WriteResponse is a delegate used to write the response.
        ResponseWriter = WriteResponse
    });
}

private static Task WriteResponse(HttpContext httpContext, 
    HealthReport result)
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

## <a name="database-probe"></a><span data-ttu-id="34650-188">Investigação de banco de dados</span><span class="sxs-lookup"><span data-stu-id="34650-188">Database probe</span></span>

<span data-ttu-id="34650-189">Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="34650-189">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="34650-190">O aplicativo de exemplo usa o [BeatPulse](https://github.com/Xabaril/BeatPulse), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="34650-190">The sample app uses [BeatPulse](https://github.com/Xabaril/BeatPulse), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="34650-191">O BeatPulse executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.</span><span class="sxs-lookup"><span data-stu-id="34650-191">BeatPulse executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="34650-192">Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente.</span><span class="sxs-lookup"><span data-stu-id="34650-192">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="34650-193">A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="34650-193">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="34650-194">Na maioria dos casos, a execução de uma consulta de teste não é necessária.</span><span class="sxs-lookup"><span data-stu-id="34650-194">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="34650-195">É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="34650-195">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="34650-196">Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="34650-196">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="34650-197">Para usar a biblioteca do BeatPulse, inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="34650-197">In order to use the BeatPulse library, include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="34650-198">Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="34650-198">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="34650-199">O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="34650-199">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="34650-200">Registre os serviços de verificação de integridade com `AddHealthChecks` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34650-200">Register health check services with `AddHealthChecks` in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34650-201">O aplicativo de exemplo chama o método `AddSqlServer` do BeatPulse com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-201">The sample app calls BeatPulse's `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="34650-202">Chame o Middleware de Verificação de Integridade do pipeline de processamento de aplicativo em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="34650-202">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="34650-203">Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-203">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="34650-204">O [BeatPulse](https://github.com/Xabaril/BeatPulse) não é mantido pela Microsoft nem tem o suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="34650-204">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="34650-205">Investigação de DbContext do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="34650-205">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="34650-206">A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="34650-206">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="34650-207">A verificação `DbContext` é compatível em aplicativos que:</span><span class="sxs-lookup"><span data-stu-id="34650-207">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="34650-208">Usam o [EF (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="34650-208">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="34650-209">Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="34650-209">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="34650-210">`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="34650-210">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="34650-211">O `DbContext` é fornecido como o `TContext` para o método.</span><span class="sxs-lookup"><span data-stu-id="34650-211">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="34650-212">Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.</span><span class="sxs-lookup"><span data-stu-id="34650-212">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="34650-213">Por padrão:</span><span class="sxs-lookup"><span data-stu-id="34650-213">By default:</span></span>

* <span data-ttu-id="34650-214">o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="34650-214">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="34650-215">Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="34650-215">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="34650-216">O nome da verificação de integridade é o nome do tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="34650-216">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="34650-217">No aplicativo de exemplo, `AppDbContext` é fornecido para `AddDbContextCheck` e registrado como um serviço em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34650-217">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="34650-218">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="34650-218">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="34650-219">No aplicativo de exemplo, `UseHealthChecks` adiciona o Middleware de Verificação de Integridade em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34650-219">In the sample app, `UseHealthChecks` adds the Health Check Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="34650-220">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="34650-220">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="34650-221">Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="34650-221">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="34650-222">Se o banco de dados existir, exclua-o.</span><span class="sxs-lookup"><span data-stu-id="34650-222">If the database exists, delete it.</span></span>

<span data-ttu-id="34650-223">Execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-223">Execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario dbcontext
```

<span data-ttu-id="34650-224">Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador.</span><span class="sxs-lookup"><span data-stu-id="34650-224">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="34650-225">O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="34650-225">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="34650-226">Dispare o aplicativo de exemplo para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="34650-226">Trigger the sample app to create the database.</span></span> <span data-ttu-id="34650-227">Faça uma solicitação para `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="34650-227">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="34650-228">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="34650-228">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="34650-229">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="34650-229">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="34650-230">O banco de dados e o contexto existem e, portanto, o aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="34650-230">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="34650-231">Dispare o aplicativo de exemplo para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="34650-231">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="34650-232">Faça uma solicitação para `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="34650-232">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="34650-233">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="34650-233">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="34650-234">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="34650-234">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="34650-235">O aplicativo fornece uma resposta não íntegra:</span><span class="sxs-lookup"><span data-stu-id="34650-235">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="34650-236">Investigações de preparação e atividade separadas</span><span class="sxs-lookup"><span data-stu-id="34650-236">Separate readiness and liveness probes</span></span>

<span data-ttu-id="34650-237">Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="34650-237">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="34650-238">O aplicativo está funcionando, mas ainda não está pronto para receber solicitações.</span><span class="sxs-lookup"><span data-stu-id="34650-238">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="34650-239">Esse estado é a *preparação* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-239">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="34650-240">O aplicativo está funcionando e respondendo a solicitações.</span><span class="sxs-lookup"><span data-stu-id="34650-240">The app is functioning and responding to requests.</span></span> <span data-ttu-id="34650-241">Esse estado é a *atividade* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="34650-241">This state is the app's *liveness*.</span></span>

<span data-ttu-id="34650-242">A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="34650-242">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="34650-243">Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="34650-243">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="34650-244">Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="34650-244">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="34650-245">O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="34650-245">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="34650-246">A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-246">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=5)]

<span data-ttu-id="34650-247">A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="34650-247">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="34650-248">Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:</span><span class="sxs-lookup"><span data-stu-id="34650-248">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="34650-249">A verificação de integridade é registrada em `AddCheck` no `Startup.ConfigureServices` juntamente com o serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="34650-249">The health check is registered with `AddCheck` in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="34650-250">Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-250">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="34650-251">Chame o Middleware de Verificação de Integridade no pipeline de processamento de aplicativo em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34650-251">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="34650-252">No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em `/health/ready` para a verificação de preparação e em `/health/live` para a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="34650-252">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="34650-253">A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.</span><span class="sxs-lookup"><span data-stu-id="34650-253">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="34650-254">A verificação de atividade filtra a `StartupHostedServiceHealthCheck` retornando `false` no `HealthCheckOptions.Predicate` (para obter mais informações, confira [Filtrar verificações de integridade](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="34650-254">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the `HealthCheckOptions.Predicate` (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

<span data-ttu-id="34650-255">Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-255">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario liveness
```

<span data-ttu-id="34650-256">Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="34650-256">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="34650-257">A verificação de integridade relata `Unhealthy` para os primeiros 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="34650-257">The health check reports `Unhealthy` for the first 15 seconds.</span></span> <span data-ttu-id="34650-258">Após 15 segundos, o ponto de extremidade relata `Healthy`, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="34650-258">After 15 seconds, the endpoint reports `Healthy`, which reflects the completion of the long-running task by the hosted service.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="34650-259">Exemplo do Kubernetes</span><span class="sxs-lookup"><span data-stu-id="34650-259">Kubernetes example</span></span>

<span data-ttu-id="34650-260">O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="34650-260">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="34650-261">No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="34650-261">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="34650-262">O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="34650-262">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="34650-263">Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="34650-263">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="34650-264">O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="34650-264">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="34650-265">Investigação baseada em métrica com um gravador de resposta personalizada</span><span class="sxs-lookup"><span data-stu-id="34650-265">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="34650-266">O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.</span><span class="sxs-lookup"><span data-stu-id="34650-266">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="34650-267">`MemoryHealthCheck` relata um estado degradado se o aplicativo usa mais de determinado limite de memória (1 GB no aplicativo de exemplo).</span><span class="sxs-lookup"><span data-stu-id="34650-267">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="34650-268">O `HealthCheckResult` inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-268">The `HealthCheckResult` includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="34650-269">Registre os serviços de verificação de integridade com `AddHealthChecks` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34650-269">Register health check services with `AddHealthChecks` in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34650-270">Em vez de permitir a verificação de integridade passando-a para `AddCheck`, a `MemoryHealthCheck` é registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="34650-270">Instead of enabling the health check by passing it to `AddCheck`, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="34650-271">Todos os serviços registrados da `IHealthCheck` estão disponíveis para os serviços de verificação de integridade e middleware.</span><span class="sxs-lookup"><span data-stu-id="34650-271">All `IHealthCheck` registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="34650-272">Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.</span><span class="sxs-lookup"><span data-stu-id="34650-272">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="34650-273">*CustomWriterStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="34650-273">*CustomWriterStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="34650-274">Chame o Middleware de Verificação de Integridade no pipeline de processamento de aplicativo em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34650-274">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="34650-275">Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:</span><span class="sxs-lookup"><span data-stu-id="34650-275">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

<span data-ttu-id="34650-276">O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="34650-276">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="34650-277">Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-277">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="34650-278">O [BeatPulse](https://github.com/Xabaril/BeatPulse) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de atividade de valor máximo e armazenamento em disco.</span><span class="sxs-lookup"><span data-stu-id="34650-278">[BeatPulse](https://github.com/Xabaril/BeatPulse) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="34650-279">O [BeatPulse](https://github.com/Xabaril/BeatPulse) não é mantido pela Microsoft nem tem o suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="34650-279">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="34650-280">Filtrar por porta</span><span class="sxs-lookup"><span data-stu-id="34650-280">Filter by port</span></span>

<span data-ttu-id="34650-281">A chamada a `UseHealthChecks` com uma porta restringe as solicitações de verificação de integridade à porta especificada.</span><span class="sxs-lookup"><span data-stu-id="34650-281">Calling `UseHealthChecks` with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="34650-282">Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="34650-282">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="34650-283">O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="34650-283">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="34650-284">A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="34650-284">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="34650-285">Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="34650-285">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="34650-286">Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.</span><span class="sxs-lookup"><span data-stu-id="34650-286">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="34650-287">O arquivo *launchSettings.json* a seguir não está incluído nos arquivos de projeto do aplicativo de exemplo e precisa ser criado manualmente.</span><span class="sxs-lookup"><span data-stu-id="34650-287">The following *launchSettings.json* file isn't included in the sample app's project files and must be created manually.</span></span>

<span data-ttu-id="34650-288">*Properties/launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="34650-288">*Properties/launchSettings.json*:</span></span>

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

<span data-ttu-id="34650-289">Registre os serviços de verificação de integridade com `AddHealthChecks` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="34650-289">Register health check services with `AddHealthChecks` in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34650-290">A chamada a `UseHealthChecks` especifica a porta de gerenciamento (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="34650-290">The call to `UseHealthChecks` specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> <span data-ttu-id="34650-291">Evite a criação do arquivo *launchSettings.json* no aplicativo de exemplo definindo as URLs e a porta de gerenciamento explicitamente no código.</span><span class="sxs-lookup"><span data-stu-id="34650-291">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="34650-292">Em *Program.cs*, em que o `WebHostBuilder` é criado, adicione uma chamada a `UseUrls` e forneça o ponto de extremidade da resposta normal do aplicativo e o ponto de extremidade da porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="34650-292">In *Program.cs* where the `WebHostBuilder` is created, add a call to `UseUrls` and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="34650-293">Em *ManagementPortStartup.cs*, em que `UseHealthChecks` é chamado, especifique a porta de gerenciamento explicitamente.</span><span class="sxs-lookup"><span data-stu-id="34650-293">In *ManagementPortStartup.cs* where `UseHealthChecks` is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="34650-294">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="34650-294">*Program.cs*:</span></span>
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
> <span data-ttu-id="34650-295">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="34650-295">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="34650-296">Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="34650-296">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="34650-297">Distribuir uma biblioteca de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="34650-297">Distribute a health check library</span></span>

<span data-ttu-id="34650-298">Para distribuir uma verificação de integridade como uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="34650-298">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="34650-299">Escreva uma verificação de integridade que implementa a interface `IHealthCheck` como uma classe autônoma.</span><span class="sxs-lookup"><span data-stu-id="34650-299">Write a health check that implements the `IHealthCheck` interface as a standalone class.</span></span> <span data-ttu-id="34650-300">A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="34650-300">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

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
                   // Health check logic
                   //
                   // data1 and data2 are used in the method to
                   // run the probe's health check logic.

                   // Assume that it's possible for this health check
                   // to throw an AccessViolationException.

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

1. <span data-ttu-id="34650-301">Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="34650-301">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="34650-302">No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="34650-302">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="34650-303">A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="34650-303">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="34650-304">Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="34650-304">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="34650-305">No seguinte exemplo, o chamador especifica itens opcionais:</span><span class="sxs-lookup"><span data-stu-id="34650-305">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="34650-306">nome da verificação de integridade (`name`).</span><span class="sxs-lookup"><span data-stu-id="34650-306">health check name (`name`).</span></span> <span data-ttu-id="34650-307">Se `null`, `example_health_check` é usado.</span><span class="sxs-lookup"><span data-stu-id="34650-307">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="34650-308">ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).</span><span class="sxs-lookup"><span data-stu-id="34650-308">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="34650-309">ponto de dados de inteiro para a verificação de integridade (`data2`).</span><span class="sxs-lookup"><span data-stu-id="34650-309">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="34650-310">Se `null`, `1` é usado.</span><span class="sxs-lookup"><span data-stu-id="34650-310">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="34650-311">status de falha (`HealthStatus`).</span><span class="sxs-lookup"><span data-stu-id="34650-311">failure status (`HealthStatus`).</span></span> <span data-ttu-id="34650-312">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="34650-312">The default is `null`.</span></span> <span data-ttu-id="34650-313">Se `null`, `HealthStatus.Unhealthy` é relatado para um status de falha.</span><span class="sxs-lookup"><span data-stu-id="34650-313">If `null`, `HealthStatus.Unhealthy` is reported for a failure status.</span></span>
   * <span data-ttu-id="34650-314">marcas (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="34650-314">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string NAME = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder, 
           string name = default, 
           string data1, 
           int data2 = 1, 
           HealthStatus? failureStatus = default, 
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? NAME,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="34650-315">Publicador de Verificação de Integridade</span><span class="sxs-lookup"><span data-stu-id="34650-315">Health Check Publisher</span></span>

<span data-ttu-id="34650-316">Quando um `IHealthCheckPublisher` é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado.</span><span class="sxs-lookup"><span data-stu-id="34650-316">When an `IHealthCheckPublisher` is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="34650-317">Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.</span><span class="sxs-lookup"><span data-stu-id="34650-317">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="34650-318">A interface `IHealthCheckPublisher` tem um único método:</span><span class="sxs-lookup"><span data-stu-id="34650-318">The `IHealthCheckPublisher` interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

> [!NOTE]
> <span data-ttu-id="34650-319">O [BeatPulse](https://github.com/Xabaril/BeatPulse) inclui publicadores para vários sistemas, incluindo o [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="34650-319">[BeatPulse](https://github.com/Xabaril/BeatPulse) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="34650-320">O [BeatPulse](https://github.com/Xabaril/BeatPulse) não é mantido pela Microsoft nem tem o suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="34650-320">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>
