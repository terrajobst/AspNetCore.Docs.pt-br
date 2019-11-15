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
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="142c9-103">Verificações de integridade no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="142c9-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="142c9-104">Por [Luke Latham](https://github.com/guardrex) e [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="142c9-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="142c9-105">O ASP.NET Core oferece middleware de verificações de integridade e bibliotecas para relatar a integridade dos componentes de infraestrutura de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="142c9-106">As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="142c9-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="142c9-107">Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:</span><span class="sxs-lookup"><span data-stu-id="142c9-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="142c9-108">As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="142c9-109">Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner.</span><span class="sxs-lookup"><span data-stu-id="142c9-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="142c9-110">Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.</span><span class="sxs-lookup"><span data-stu-id="142c9-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="142c9-111">O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="142c9-112">As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.</span><span class="sxs-lookup"><span data-stu-id="142c9-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="142c9-113">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="142c9-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="142c9-114">O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="142c9-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="142c9-115">Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="142c9-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="142c9-116">Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="142c9-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="142c9-117">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="142c9-117">Prerequisites</span></span>

<span data-ttu-id="142c9-118">As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="142c9-119">Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="142c9-120">O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="142c9-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="142c9-121">O pacote [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) é referenciado implicitamente para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="142c9-122">Para executar verificações de integridade usando Entity Framework Core, adicione uma referência de pacote ao pacote [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="142c9-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="142c9-123">O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="142c9-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="142c9-124">O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="142c9-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="142c9-125">O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="142c9-126">Para explorar os cenários de banco de dados, o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="142c9-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="142c9-127">Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="142c9-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="142c9-128">Tem as seguintes referências de pacote em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="142c9-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="142c9-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="142c9-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="142c9-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="142c9-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="142c9-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="142c9-132">Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="142c9-133">O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="142c9-134">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="142c9-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="142c9-135">Investigação de integridade básica</span><span class="sxs-lookup"><span data-stu-id="142c9-135">Basic health probe</span></span>

<span data-ttu-id="142c9-136">Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="142c9-137">A configuração básica registra serviços de verificação de integridade e chama o middleware de verificações de integridade para responder em um ponto de extremidade de URL com uma resposta de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="142c9-138">Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico.</span><span class="sxs-lookup"><span data-stu-id="142c9-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="142c9-139">O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="142c9-140">O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="142c9-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="142c9-141">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-142">Crie um ponto de extremidade de verificação de integridade chamando `MapHealthChecks` em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="142c9-143">No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="142c9-144">Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="142c9-145">Exemplo do Docker</span><span class="sxs-lookup"><span data-stu-id="142c9-145">Docker example</span></span>

<span data-ttu-id="142c9-146">O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="142c9-147">Criar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-147">Create health checks</span></span>

<span data-ttu-id="142c9-148">As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="142c9-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="142c9-149">O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="142c9-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="142c9-150">O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="142c9-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="142c9-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.</span><span class="sxs-lookup"><span data-stu-id="142c9-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="142c9-152">A classe de `ExampleHealthCheck` a seguir demonstra o layout de uma verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="142c9-153">A lógica de verificações de integridade é colocada no método `CheckHealthAsync`.</span><span class="sxs-lookup"><span data-stu-id="142c9-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="142c9-154">O exemplo a seguir define uma variável fictícia, `healthCheckResultHealthy`, para `true`.</span><span class="sxs-lookup"><span data-stu-id="142c9-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="142c9-155">Se o valor de `healthCheckResultHealthy` for definido como `false`, o [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status não íntegro será retornado.</span><span class="sxs-lookup"><span data-stu-id="142c9-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="142c9-156">Registrar os serviços de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-156">Register health check services</span></span>

<span data-ttu-id="142c9-157">O tipo de `ExampleHealthCheck` é adicionado aos serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="142c9-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="142c9-158">A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha.</span><span class="sxs-lookup"><span data-stu-id="142c9-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="142c9-159">Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado.</span><span class="sxs-lookup"><span data-stu-id="142c9-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="142c9-160">Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.</span><span class="sxs-lookup"><span data-stu-id="142c9-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="142c9-161">*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="142c9-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="142c9-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda.</span><span class="sxs-lookup"><span data-stu-id="142c9-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="142c9-163">No seguinte exemplo, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:</span><span class="sxs-lookup"><span data-stu-id="142c9-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="142c9-164">Chame <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> para passar argumentos para uma implementação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arugments to a health check implementation.</span></span> <span data-ttu-id="142c9-165">No exemplo a seguir, `TestHealthCheckWithArgs` aceita um inteiro e uma cadeia de caracteres para uso quando <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="142c9-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="142c9-166">`TestHealthCheckWithArgs` é registrado chamando `AddTypeActivatedCheck` com o inteiro e a cadeia de caracteres passada para a implementação:</span><span class="sxs-lookup"><span data-stu-id="142c9-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="142c9-167">Usar roteamento de verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-167">Use Health Checks Routing</span></span>

<span data-ttu-id="142c9-168">No `Startup.Configure`, chame `MapHealthChecks` no construtor de ponto de extremidade com a URL do ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="142c9-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="142c9-169">Exigir host</span><span class="sxs-lookup"><span data-stu-id="142c9-169">Require host</span></span>

<span data-ttu-id="142c9-170">Chame `RequireHost` para especificar um ou mais hosts permitidos para o ponto de extremidade de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="142c9-171">Os hosts devem ser Unicode em vez de Punycode e podem incluir uma porta.</span><span class="sxs-lookup"><span data-stu-id="142c9-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="142c9-172">Se uma coleção não for fornecida, qualquer host será aceito.</span><span class="sxs-lookup"><span data-stu-id="142c9-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="142c9-173">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="142c9-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="142c9-174">Exigir autorização</span><span class="sxs-lookup"><span data-stu-id="142c9-174">Require authorization</span></span>

<span data-ttu-id="142c9-175">Chame `RequireAuthorization` para executar o middleware de autorização no ponto de extremidade de solicitação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="142c9-176">Uma sobrecarga de `RequireAuthorization` aceita uma ou mais políticas de autorização.</span><span class="sxs-lookup"><span data-stu-id="142c9-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="142c9-177">Se uma política não for fornecida, a política de autorização padrão será usada.</span><span class="sxs-lookup"><span data-stu-id="142c9-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="142c9-178">Habilitar o CORS (Solicitações Entre Origens)</span><span class="sxs-lookup"><span data-stu-id="142c9-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="142c9-179">Embora a execução manual de verificações de integridade de um navegador não seja um cenário de uso comum, o middleware CORS pode ser habilitado chamando `RequireCors` nos pontos de extremidade de verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="142c9-180">Uma sobrecarga de `RequireCors` aceita um delegado de construtor de política CORS (`CorsPolicyBuilder`) ou um nome de política.</span><span class="sxs-lookup"><span data-stu-id="142c9-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="142c9-181">Se uma política não for fornecida, a política CORS padrão será usada.</span><span class="sxs-lookup"><span data-stu-id="142c9-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="142c9-182">Para obter mais informações, consulte <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="142c9-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="142c9-183">Opções de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-183">Health check options</span></span>

<span data-ttu-id="142c9-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="142c9-185">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="142c9-186">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="142c9-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="142c9-187">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="142c9-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="142c9-188">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="142c9-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="142c9-189">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-189">Filter health checks</span></span>

<span data-ttu-id="142c9-190">Por padrão, o middleware de verificações de integridade executa todas as verificações de integridade registradas.</span><span class="sxs-lookup"><span data-stu-id="142c9-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="142c9-191">Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="142c9-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="142c9-192">No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="142c9-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="142c9-193">No `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="142c9-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="142c9-194">No `Startup.Configure`, o `Predicate` filtra a verificação de integridade da ' barra '.</span><span class="sxs-lookup"><span data-stu-id="142c9-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="142c9-195">Somente foo e Baz são executados.:</span><span class="sxs-lookup"><span data-stu-id="142c9-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="142c9-196">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="142c9-196">Customize the HTTP status code</span></span>

<span data-ttu-id="142c9-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="142c9-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="142c9-198">As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="142c9-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="142c9-199">Altere os valores do código de status de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="142c9-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="142c9-200">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="142c9-201">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="142c9-201">Suppress cache headers</span></span>

<span data-ttu-id="142c9-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controla se o middleware de verificações de integridade adiciona cabeçalhos HTTP a uma resposta de investigação para evitar o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="142c9-203">Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="142c9-204">Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="142c9-205">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="142c9-206">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="142c9-206">Customize output</span></span>

<span data-ttu-id="142c9-207">A opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtém ou define um representante usado para gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-207">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="142c9-208">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-208">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="142c9-209">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="142c9-209">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="142c9-210">O seguinte delegado personalizado, `WriteResponse`, gera uma resposta JSON personalizada:</span><span class="sxs-lookup"><span data-stu-id="142c9-210">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="142c9-211">O sistema de verificações de integridade não fornece suporte interno para formatos de retorno JSON complexos porque o formato é específico à sua escolha de sistema de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="142c9-211">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="142c9-212">Fique à vontade para personalizar o `JObject` no exemplo anterior, conforme necessário, para atender às suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="142c9-212">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="142c9-213">Investigação de banco de dados</span><span class="sxs-lookup"><span data-stu-id="142c9-213">Database probe</span></span>

<span data-ttu-id="142c9-214">Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="142c9-214">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="142c9-215">O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="142c9-215">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="142c9-216">O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.</span><span class="sxs-lookup"><span data-stu-id="142c9-216">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="142c9-217">Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente.</span><span class="sxs-lookup"><span data-stu-id="142c9-217">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="142c9-218">A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="142c9-218">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="142c9-219">Na maioria dos casos, a execução de uma consulta de teste não é necessária.</span><span class="sxs-lookup"><span data-stu-id="142c9-219">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="142c9-220">É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-220">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="142c9-221">Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="142c9-221">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="142c9-222">Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="142c9-222">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="142c9-223">Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="142c9-223">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="142c9-224">O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="142c9-224">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="142c9-225">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-225">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-226">O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-226">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-227">Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-227">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="142c9-228">Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-228">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="142c9-229">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-229">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="142c9-230">Investigação de DbContext do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="142c9-230">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="142c9-231">A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-231">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="142c9-232">A verificação `DbContext` é compatível em aplicativos que:</span><span class="sxs-lookup"><span data-stu-id="142c9-232">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="142c9-233">Usam o [EF (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="142c9-233">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="142c9-234">Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="142c9-234">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="142c9-235">`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="142c9-235">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="142c9-236">O `DbContext` é fornecido como o `TContext` para o método.</span><span class="sxs-lookup"><span data-stu-id="142c9-236">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="142c9-237">Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.</span><span class="sxs-lookup"><span data-stu-id="142c9-237">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="142c9-238">Por padrão:</span><span class="sxs-lookup"><span data-stu-id="142c9-238">By default:</span></span>

* <span data-ttu-id="142c9-239">o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-239">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="142c9-240">Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="142c9-240">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="142c9-241">O nome da verificação de integridade é o nome do tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="142c9-241">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="142c9-242">No aplicativo de exemplo, `AppDbContext` é fornecida para `AddDbContextCheck` e registrada como um serviço no `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-242">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-243">Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-243">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="142c9-244">Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="142c9-244">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="142c9-245">Se o banco de dados existir, exclua-o.</span><span class="sxs-lookup"><span data-stu-id="142c9-245">If the database exists, delete it.</span></span>

<span data-ttu-id="142c9-246">Execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-246">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="142c9-247">Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador.</span><span class="sxs-lookup"><span data-stu-id="142c9-247">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="142c9-248">O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="142c9-248">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="142c9-249">Dispare o aplicativo de exemplo para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-249">Trigger the sample app to create the database.</span></span> <span data-ttu-id="142c9-250">Faça uma solicitação para `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="142c9-250">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="142c9-251">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-251">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="142c9-252">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="142c9-252">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="142c9-253">O banco de dados e o contexto existem e, portanto, o aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-253">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="142c9-254">Dispare o aplicativo de exemplo para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-254">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="142c9-255">Faça uma solicitação para `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="142c9-255">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="142c9-256">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-256">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="142c9-257">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="142c9-257">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="142c9-258">O aplicativo fornece uma resposta não íntegra:</span><span class="sxs-lookup"><span data-stu-id="142c9-258">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="142c9-259">Investigações de preparação e atividade separadas</span><span class="sxs-lookup"><span data-stu-id="142c9-259">Separate readiness and liveness probes</span></span>

<span data-ttu-id="142c9-260">Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="142c9-260">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="142c9-261">O aplicativo está funcionando, mas ainda não está pronto para receber solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-261">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="142c9-262">Esse estado é a *preparação* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-262">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="142c9-263">O aplicativo está funcionando e respondendo a solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-263">The app is functioning and responding to requests.</span></span> <span data-ttu-id="142c9-264">Esse estado é a *atividade* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-264">This state is the app's *liveness*.</span></span>

<span data-ttu-id="142c9-265">A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="142c9-265">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="142c9-266">Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-266">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="142c9-267">Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="142c9-267">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="142c9-268">O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="142c9-268">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="142c9-269">A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-269">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="142c9-270">A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="142c9-270">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="142c9-271">Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:</span><span class="sxs-lookup"><span data-stu-id="142c9-271">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="142c9-272">A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="142c9-272">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="142c9-273">Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-273">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-274">Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-274">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="142c9-275">No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em:</span><span class="sxs-lookup"><span data-stu-id="142c9-275">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="142c9-276">`/health/ready` para a verificação de preparação.</span><span class="sxs-lookup"><span data-stu-id="142c9-276">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="142c9-277">A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.</span><span class="sxs-lookup"><span data-stu-id="142c9-277">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="142c9-278">`/health/live` para a verificação de tempo de vida.</span><span class="sxs-lookup"><span data-stu-id="142c9-278">`/health/live` for the liveness check.</span></span> <span data-ttu-id="142c9-279">A verificação de vida filtra a `StartupHostedServiceHealthCheck` retornando `false` no [predicado HealthCheckOptions.](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, consulte [Filtrar verificações de integridade](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="142c9-279">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="142c9-280">No código de exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="142c9-280">In the following example code:</span></span>

* <span data-ttu-id="142c9-281">A verificação de preparação usa todas as verificações registradas com a marca ' Ready '.</span><span class="sxs-lookup"><span data-stu-id="142c9-281">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="142c9-282">O `Predicate` exclui todas as verificações e retorna um 200-OK.</span><span class="sxs-lookup"><span data-stu-id="142c9-282">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="142c9-283">Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-283">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="142c9-284">Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-284">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="142c9-285">A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-285">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="142c9-286">Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="142c9-286">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="142c9-287">Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-287">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="142c9-288">Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="142c9-288">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="142c9-289">Exemplo do Kubernetes</span><span class="sxs-lookup"><span data-stu-id="142c9-289">Kubernetes example</span></span>

<span data-ttu-id="142c9-290">O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="142c9-290">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="142c9-291">No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="142c9-291">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="142c9-292">O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="142c9-292">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="142c9-293">Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="142c9-293">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="142c9-294">O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="142c9-294">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="142c9-295">Investigação baseada em métrica com um gravador de resposta personalizada</span><span class="sxs-lookup"><span data-stu-id="142c9-295">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="142c9-296">O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.</span><span class="sxs-lookup"><span data-stu-id="142c9-296">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="142c9-297">`MemoryHealthCheck` relata um estado degradado se o aplicativo usa mais de determinado limite de memória (1 GB no aplicativo de exemplo).</span><span class="sxs-lookup"><span data-stu-id="142c9-297">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="142c9-298">O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-298">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="142c9-299">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-299">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-300">Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="142c9-300">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="142c9-301">Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware.</span><span class="sxs-lookup"><span data-stu-id="142c9-301">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="142c9-302">Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.</span><span class="sxs-lookup"><span data-stu-id="142c9-302">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="142c9-303">No aplicativo de exemplo (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-303">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="142c9-304">Um ponto de extremidade de verificação de integridade é criado chamando `MapHealthChecks` em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-304">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="142c9-305">Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:</span><span class="sxs-lookup"><span data-stu-id="142c9-305">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="142c9-306">O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-306">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="142c9-307">Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-307">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="142c9-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.</span><span class="sxs-lookup"><span data-stu-id="142c9-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="142c9-309">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-309">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="142c9-310">Filtrar por porta</span><span class="sxs-lookup"><span data-stu-id="142c9-310">Filter by port</span></span>

<span data-ttu-id="142c9-311">Chame `RequireHost` em `MapHealthChecks` com um padrão de URL que especifica uma porta para restringir as solicitações de verificação de integridade à porta especificada.</span><span class="sxs-lookup"><span data-stu-id="142c9-311">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="142c9-312">Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="142c9-312">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="142c9-313">O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="142c9-313">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="142c9-314">A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="142c9-314">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="142c9-315">Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-315">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="142c9-316">Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.</span><span class="sxs-lookup"><span data-stu-id="142c9-316">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="142c9-317">O arquivo de *Propriedades/launchSettings. JSON* a seguir no aplicativo de exemplo não está incluído nos arquivos de projeto do aplicativo de exemplo e deve ser criado manualmente:</span><span class="sxs-lookup"><span data-stu-id="142c9-317">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="142c9-318">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-318">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-319">Crie um ponto de extremidade de verificação de integridade chamando `MapHealthChecks` em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-319">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="142c9-320">No aplicativo de exemplo, uma chamada para `RequireHost` no ponto de extremidade em `Startup.Configure` especifica a porta de gerenciamento da configuração:</span><span class="sxs-lookup"><span data-stu-id="142c9-320">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="142c9-321">Os pontos de extremidade são criados no aplicativo de exemplo no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-321">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="142c9-322">No código de exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="142c9-322">In the following example code:</span></span>

* <span data-ttu-id="142c9-323">A verificação de preparação usa todas as verificações registradas com a marca ' Ready '.</span><span class="sxs-lookup"><span data-stu-id="142c9-323">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="142c9-324">O `Predicate` exclui todas as verificações e retorna um 200-OK.</span><span class="sxs-lookup"><span data-stu-id="142c9-324">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="142c9-325">Você pode evitar criar o arquivo *launchSettings. JSON* no aplicativo de exemplo definindo a porta de gerenciamento explicitamente no código.</span><span class="sxs-lookup"><span data-stu-id="142c9-325">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="142c9-326">Em *Program.cs* em que o <xref:Microsoft.Extensions.Hosting.HostBuilder> é criado, adicione uma chamada para <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> e forneça o ponto de extremidade da porta de gerenciamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-326">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="142c9-327">Em `Configure` de *ManagementPortStartup.cs*, especifique a porta de gerenciamento com `RequireHost`:</span><span class="sxs-lookup"><span data-stu-id="142c9-327">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="142c9-328">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="142c9-328">*Program.cs*:</span></span>
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
> <span data-ttu-id="142c9-329">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="142c9-329">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="142c9-330">Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-330">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="142c9-331">Distribuir uma biblioteca de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-331">Distribute a health check library</span></span>

<span data-ttu-id="142c9-332">Para distribuir uma verificação de integridade como uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="142c9-332">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="142c9-333">Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma.</span><span class="sxs-lookup"><span data-stu-id="142c9-333">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="142c9-334">A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="142c9-334">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="142c9-335">Na lógica de verificações de integridade de `CheckHealthAsync`:</span><span class="sxs-lookup"><span data-stu-id="142c9-335">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="142c9-336">`data1` e `data2` são usados no método para executar a lógica de verificação de integridade da investigação.</span><span class="sxs-lookup"><span data-stu-id="142c9-336">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="142c9-337">`AccessViolationException` é manipulado.</span><span class="sxs-lookup"><span data-stu-id="142c9-337">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="142c9-338">Quando ocorre uma <xref:System.AccessViolationException>, a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é retornada com a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> para permitir que os usuários configurem o status de falha de verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-338">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="142c9-339">Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-339">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="142c9-340">No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-340">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="142c9-341">A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-341">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="142c9-342">Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="142c9-342">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="142c9-343">No seguinte exemplo, o chamador especifica itens opcionais:</span><span class="sxs-lookup"><span data-stu-id="142c9-343">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="142c9-344">nome da verificação de integridade (`name`).</span><span class="sxs-lookup"><span data-stu-id="142c9-344">health check name (`name`).</span></span> <span data-ttu-id="142c9-345">Se `null`, `example_health_check` é usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-345">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="142c9-346">ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).</span><span class="sxs-lookup"><span data-stu-id="142c9-346">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="142c9-347">ponto de dados de inteiro para a verificação de integridade (`data2`).</span><span class="sxs-lookup"><span data-stu-id="142c9-347">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="142c9-348">Se `null`, `1` é usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-348">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="142c9-349">status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="142c9-349">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="142c9-350">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="142c9-350">The default is `null`.</span></span> <span data-ttu-id="142c9-351">Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.</span><span class="sxs-lookup"><span data-stu-id="142c9-351">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="142c9-352">marcas (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="142c9-352">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="142c9-353">Publicador de Verificação de Integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-353">Health Check Publisher</span></span>

<span data-ttu-id="142c9-354">Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado.</span><span class="sxs-lookup"><span data-stu-id="142c9-354">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="142c9-355">Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-355">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="142c9-356">A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:</span><span class="sxs-lookup"><span data-stu-id="142c9-356">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="142c9-357"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:</span><span class="sxs-lookup"><span data-stu-id="142c9-357"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="142c9-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; O atraso inicial aplicado após o aplicativo começar a executar instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="142c9-359">O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes.</span><span class="sxs-lookup"><span data-stu-id="142c9-359">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="142c9-360">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-360">The default value is five seconds.</span></span>
* <span data-ttu-id="142c9-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; O período da execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="142c9-362">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-362">The default value is 30 seconds.</span></span>
* <span data-ttu-id="142c9-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> for `null` (padrão), o serviço publicador de verificação de integridade executará todas as verificações de integridade registradas.</span><span class="sxs-lookup"><span data-stu-id="142c9-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="142c9-364">Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações.</span><span class="sxs-lookup"><span data-stu-id="142c9-364">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="142c9-365">O predicado é avaliado a cada período.</span><span class="sxs-lookup"><span data-stu-id="142c9-365">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="142c9-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; O tempo limite para a execução de verificações de integridade em todas as instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="142c9-367">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite.</span><span class="sxs-lookup"><span data-stu-id="142c9-367">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="142c9-368">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-368">The default value is 30 seconds.</span></span>

<span data-ttu-id="142c9-369">No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-369">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="142c9-370">O status da verificação de integridade é registrado para cada verificação em um nível de log de:</span><span class="sxs-lookup"><span data-stu-id="142c9-370">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="142c9-371">Informações (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) se o status das verificações de integridade for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span><span class="sxs-lookup"><span data-stu-id="142c9-371">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="142c9-372">Erro (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) se o status for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="142c9-372">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="142c9-373">No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-373">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="142c9-374">Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="142c9-374">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="142c9-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="142c9-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="142c9-376">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-376">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="142c9-377">Restringir verificações de integridade com MapWhen</span><span class="sxs-lookup"><span data-stu-id="142c9-377">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="142c9-378">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-378">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="142c9-379">No exemplo a seguir, `MapWhen` ramifica o pipeline de solicitação para ativar o middleware de verificações de integridade se uma solicitação GET for recebida para o ponto de extremidade de `api/HealthCheck`:</span><span class="sxs-lookup"><span data-stu-id="142c9-379">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="142c9-380">Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="142c9-380">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="142c9-381">O ASP.NET Core oferece middleware de verificações de integridade e bibliotecas para relatar a integridade dos componentes de infraestrutura de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-381">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="142c9-382">As verificações de integridade são expostas por um aplicativo como pontos de extremidade HTTP.</span><span class="sxs-lookup"><span data-stu-id="142c9-382">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="142c9-383">Os pontos de extremidade de verificação de integridade podem ser configurados para uma variedade de cenários de monitoramento em tempo real:</span><span class="sxs-lookup"><span data-stu-id="142c9-383">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="142c9-384">As investigações de integridade podem ser usadas por orquestradores de contêineres e balanceadores de carga para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-384">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="142c9-385">Por exemplo, um orquestrador de contêineres pode responder a uma verificação de integridade com falha interrompendo uma implantação sem interrupção ou reiniciando um contêiner.</span><span class="sxs-lookup"><span data-stu-id="142c9-385">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="142c9-386">Um balanceador de carga pode reagir a um aplicativo não íntegro encaminhando o tráfego para fora da instância com falha para uma instância íntegra.</span><span class="sxs-lookup"><span data-stu-id="142c9-386">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="142c9-387">O uso de memória, disco e outros recursos de servidor físico pode ser monitorado quanto ao status de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-387">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="142c9-388">As verificações de integridade podem testar as dependências do aplicativo, como bancos de dados e pontos de extremidade de serviço externo, para confirmar a disponibilidade e o funcionamento normal.</span><span class="sxs-lookup"><span data-stu-id="142c9-388">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="142c9-389">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="142c9-389">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="142c9-390">O aplicativo de exemplo inclui exemplos dos cenários descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="142c9-390">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="142c9-391">Para executar o aplicativo de exemplo para determinado cenário, use o comando [dotnet run](/dotnet/core/tools/dotnet-run) na pasta do projeto em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="142c9-391">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="142c9-392">Confira o arquivo *README.md* do aplicativo de exemplo e as descrições de cenários neste tópico para obter detalhes sobre como usar o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="142c9-392">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="142c9-393">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="142c9-393">Prerequisites</span></span>

<span data-ttu-id="142c9-394">As verificações de integridade são normalmente usadas com um orquestrador de contêineres ou um serviço de monitoramento externo para verificar o status de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-394">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="142c9-395">Antes de adicionar verificações de integridade a um aplicativo, decida qual sistema de monitoramento será usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-395">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="142c9-396">O sistema de monitoramento determina quais tipos de verificações de integridade serão criados e como configurar seus pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="142c9-396">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="142c9-397">Referencie o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ou adicione uma referência de pacote ao pacote [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="142c9-397">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="142c9-398">O aplicativo de exemplo fornece um código de inicialização para demonstrar verificações de integridade para vários cenários.</span><span class="sxs-lookup"><span data-stu-id="142c9-398">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="142c9-399">O cenário [investigação de banco de dados](#database-probe) verifica a integridade de uma conexão de banco de dados usando [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="142c9-399">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="142c9-400">O cenário [investigação de DbContext](#entity-framework-core-dbcontext-probe) verifica um banco de dados usando um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-400">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="142c9-401">Para explorar os cenários de banco de dados, o aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="142c9-401">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="142c9-402">Cria um banco de dados e fornece sua cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="142c9-402">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="142c9-403">Tem as seguintes referências de pacote em seu arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="142c9-403">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="142c9-404">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="142c9-404">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="142c9-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="142c9-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="142c9-406">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-406">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="142c9-407">Outro cenário de verificação de integridade demonstra como filtrar verificações de integridade para uma porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-407">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="142c9-408">O aplicativo de exemplo exige a criação de um arquivo *Properties/launchSettings.json* que inclui a URL de gerenciamento e a porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-408">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="142c9-409">Para obter mais informações, confira a seção [Filtrar por porta](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="142c9-409">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="142c9-410">Investigação de integridade básica</span><span class="sxs-lookup"><span data-stu-id="142c9-410">Basic health probe</span></span>

<span data-ttu-id="142c9-411">Para muitos aplicativos, uma configuração básica de investigação de integridade que relata a disponibilidade do aplicativo para processar solicitações (*atividade*) é suficiente para descobrir o status do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-411">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="142c9-412">A configuração básica registra serviços de verificação de integridade e chama o middleware de verificações de integridade para responder em um ponto de extremidade de URL com uma resposta de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-412">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="142c9-413">Por padrão, nenhuma verificação de integridade específica é registrada para testar qualquer dependência ou subsistema específico.</span><span class="sxs-lookup"><span data-stu-id="142c9-413">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="142c9-414">O aplicativo é considerado íntegro se consegue responder na URL do ponto de extremidade de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-414">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="142c9-415">O gravador de resposta padrão grava o status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como uma resposta de texto não criptografado no cliente, indicando um status [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) ou [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus).</span><span class="sxs-lookup"><span data-stu-id="142c9-415">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="142c9-416">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-416">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-417">Adicione um ponto de extremidade para o middleware de verificações de integridade com <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> no pipeline de processamento de solicitação de `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-417">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="142c9-418">No aplicativo de exemplo, o ponto de extremidade de verificação de integridade é criado em `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-418">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="142c9-419">Para executar o cenário de configuração básica usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-419">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="142c9-420">Exemplo do Docker</span><span class="sxs-lookup"><span data-stu-id="142c9-420">Docker example</span></span>

<span data-ttu-id="142c9-421">O [Docker](xref:host-and-deploy/docker/index) oferece uma diretiva `HEALTHCHECK` interna que pode ser usada para verificar o status de um aplicativo que usa a configuração básica de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-421">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="142c9-422">Criar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-422">Create health checks</span></span>

<span data-ttu-id="142c9-423">As verificações de integridade são criadas pela implementação da interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="142c9-423">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="142c9-424">O método <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> retorna um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> que indica a integridade como `Healthy`, `Degraded` ou `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="142c9-424">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="142c9-425">O resultado é gravado como uma resposta de texto sem formatação com um código de status configurável (a configuração é descrita na seção [Opções de verificação de integridade](#health-check-options)).</span><span class="sxs-lookup"><span data-stu-id="142c9-425">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="142c9-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> também pode retornar pares chave-valor opcionais.</span><span class="sxs-lookup"><span data-stu-id="142c9-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="142c9-427">Verificação de integridade de exemplo</span><span class="sxs-lookup"><span data-stu-id="142c9-427">Example health check</span></span>

<span data-ttu-id="142c9-428">A classe de `ExampleHealthCheck` a seguir demonstra o layout de uma verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-428">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="142c9-429">A lógica de verificações de integridade é colocada no método `CheckHealthAsync`.</span><span class="sxs-lookup"><span data-stu-id="142c9-429">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="142c9-430">O exemplo a seguir define uma variável fictícia, `healthCheckResultHealthy`, para `true`.</span><span class="sxs-lookup"><span data-stu-id="142c9-430">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="142c9-431">Se o valor de `healthCheckResultHealthy` for definido como `false`, o [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status não íntegro será retornado.</span><span class="sxs-lookup"><span data-stu-id="142c9-431">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="142c9-432">Registrar os serviços de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-432">Register health check services</span></span>

<span data-ttu-id="142c9-433">O tipo de `ExampleHealthCheck` é adicionado aos serviços de verificação de integridade em `Startup.ConfigureServices` com <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span><span class="sxs-lookup"><span data-stu-id="142c9-433">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="142c9-434">A sobrecarga <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> mostrada no exemplo a seguir define o status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) como relatório quando a verificação de integridade relata uma falha.</span><span class="sxs-lookup"><span data-stu-id="142c9-434">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="142c9-435">Se o status de falha for definido como `null` (padrão), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) será relatado.</span><span class="sxs-lookup"><span data-stu-id="142c9-435">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="142c9-436">Essa sobrecarga é um cenário útil para autores de biblioteca, em que o status de falha indicado pela biblioteca é imposto pelo aplicativo quando uma falha de verificação de integridade ocorre se a implementação da verificação de integridade respeita a configuração.</span><span class="sxs-lookup"><span data-stu-id="142c9-436">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="142c9-437">*Marcas* podem ser usadas para filtrar as verificações de integridade (descritas posteriormente na seção [Filtrar verificações de integridade](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="142c9-437">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="142c9-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> também pode executar uma função lambda.</span><span class="sxs-lookup"><span data-stu-id="142c9-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="142c9-439">No exemplo a seguir `Startup.ConfigureServices`, o nome da verificação de integridade é especificado como `Example` e a verificação sempre retorna um estado íntegro:</span><span class="sxs-lookup"><span data-stu-id="142c9-439">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="142c9-440">Usar o Middleware de Verificações de Integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-440">Use Health Checks Middleware</span></span>

<span data-ttu-id="142c9-441">Em `Startup.Configure`, chame <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> do pipeline de processamento com a URL de ponto de extremidade ou o caminho relativo:</span><span class="sxs-lookup"><span data-stu-id="142c9-441">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="142c9-442">Se as verificações de integridade precisarem escutar uma porta específica, use uma sobrecarga igual a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> para definir a porta (descrito posteriormente na seção [Filtrar por porta](#filter-by-port)):</span><span class="sxs-lookup"><span data-stu-id="142c9-442">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="142c9-443">Opções de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-443">Health check options</span></span>

<span data-ttu-id="142c9-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> oferece uma oportunidade de personalizar o comportamento de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="142c9-445">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-445">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="142c9-446">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="142c9-446">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="142c9-447">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="142c9-447">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="142c9-448">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="142c9-448">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="142c9-449">Filtrar verificações de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-449">Filter health checks</span></span>

<span data-ttu-id="142c9-450">Por padrão, o middleware de verificações de integridade executa todas as verificações de integridade registradas.</span><span class="sxs-lookup"><span data-stu-id="142c9-450">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="142c9-451">Para executar um subconjunto das verificações de integridade, forneça uma função que retorne um booliano para a opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate>.</span><span class="sxs-lookup"><span data-stu-id="142c9-451">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="142c9-452">No seguinte exemplo, a verificação de integridade `Bar` é filtrada por sua marca (`bar_tag`) na instrução condicional da função, em que `true` só é retornado se a propriedade <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> da verificação de integridade corresponde a `foo_tag` ou `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="142c9-452">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="142c9-453">Personalizar o código de status HTTP</span><span class="sxs-lookup"><span data-stu-id="142c9-453">Customize the HTTP status code</span></span>

<span data-ttu-id="142c9-454">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> para personalizar o mapeamento de status da integridade para códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="142c9-454">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="142c9-455">As atribuições <xref:Microsoft.AspNetCore.Http.StatusCodes> a seguir são os valores padrão usados pelo middleware.</span><span class="sxs-lookup"><span data-stu-id="142c9-455">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="142c9-456">Altere os valores do código de status de acordo com suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="142c9-456">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="142c9-457">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-457">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="142c9-458">Suprimir os cabeçalhos de cache</span><span class="sxs-lookup"><span data-stu-id="142c9-458">Suppress cache headers</span></span>

<span data-ttu-id="142c9-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controla se o middleware de verificações de integridade adiciona cabeçalhos HTTP a uma resposta de investigação para evitar o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="142c9-460">Se o valor é `false` (padrão), o middleware define ou substitui os cabeçalhos `Cache-Control`, `Expires` e `Pragma` para prevenir o cache de resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-460">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="142c9-461">Se o valor é `true`, o middleware não modifica os cabeçalhos de cache da resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-461">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="142c9-462">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-462">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="142c9-463">Personalizar a saída</span><span class="sxs-lookup"><span data-stu-id="142c9-463">Customize output</span></span>

<span data-ttu-id="142c9-464">A opção <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> obtém ou define um representante usado para gravar a resposta.</span><span class="sxs-lookup"><span data-stu-id="142c9-464">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="142c9-465">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="142c9-465">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="142c9-466">No `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-466">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="142c9-467">O representante padrão grava uma resposta mínima de texto sem formatação com o valor de cadeia de caracteres [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="142c9-467">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="142c9-468">O seguinte delegado personalizado, `WriteResponse`, gera uma resposta JSON personalizada:</span><span class="sxs-lookup"><span data-stu-id="142c9-468">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="142c9-469">O sistema de verificações de integridade não fornece suporte interno para formatos de retorno JSON complexos porque o formato é específico à sua escolha de sistema de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="142c9-469">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="142c9-470">Fique à vontade para personalizar o `JObject` no exemplo anterior, conforme necessário, para atender às suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="142c9-470">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="142c9-471">Investigação de banco de dados</span><span class="sxs-lookup"><span data-stu-id="142c9-471">Database probe</span></span>

<span data-ttu-id="142c9-472">Uma verificação de integridade pode especificar uma consulta de banco de dados a ser executada como um teste booliano para indicar se o banco de dados está respondendo normalmente.</span><span class="sxs-lookup"><span data-stu-id="142c9-472">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="142c9-473">O aplicativo de exemplo usa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), uma biblioteca de verificação de integridade para aplicativos ASP.NET Core, para executar uma verificação de integridade em um banco de dados do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="142c9-473">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="142c9-474">O `AspNetCore.Diagnostics.HealthChecks` executa uma consulta `SELECT 1` no banco de dados para confirmar se a conexão ao banco de dados está íntegra.</span><span class="sxs-lookup"><span data-stu-id="142c9-474">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="142c9-475">Ao verificar uma conexão de banco de dados com uma consulta, escolha uma consulta que retorne rapidamente.</span><span class="sxs-lookup"><span data-stu-id="142c9-475">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="142c9-476">A abordagem de consulta corre o risco de sobrecarregar o banco de dados e prejudicar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="142c9-476">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="142c9-477">Na maioria dos casos, a execução de uma consulta de teste não é necessária.</span><span class="sxs-lookup"><span data-stu-id="142c9-477">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="142c9-478">É suficiente apenas estabelecer uma conexão bem-sucedida ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-478">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="142c9-479">Se você achar necessário executar uma consulta, escolha uma consulta SELECT simples, como `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="142c9-479">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="142c9-480">Inclua uma referência de pacote a [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="142c9-480">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="142c9-481">Forneça uma cadeia de conexão de banco de dados válida no arquivo *appsettings.json* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="142c9-481">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="142c9-482">O aplicativo usa um banco de dados do SQL Server chamado `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="142c9-482">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="142c9-483">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-483">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-484">O aplicativo de exemplo chama o método `AddSqlServer` com a cadeia de conexão do banco de dados (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-484">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-485">O middleware de verificações de integridade de chamada no pipeline de processamento de aplicativo no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="142c9-485">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="142c9-486">Para executar o cenário de investigação de banco de dados usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-486">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="142c9-487">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-487">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="142c9-488">Investigação de DbContext do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="142c9-488">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="142c9-489">A verificação `DbContext` confirma se o aplicativo pode se comunicar com o banco de dados configurado para um `DbContext` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-489">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="142c9-490">A verificação `DbContext` é compatível em aplicativos que:</span><span class="sxs-lookup"><span data-stu-id="142c9-490">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="142c9-491">Usam o [EF (Entity Framework) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="142c9-491">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="142c9-492">Incluem uma referência de pacote para [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="142c9-492">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="142c9-493">`AddDbContextCheck<TContext>` registra uma verificação de integridade para um `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="142c9-493">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="142c9-494">O `DbContext` é fornecido como o `TContext` para o método.</span><span class="sxs-lookup"><span data-stu-id="142c9-494">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="142c9-495">Uma sobrecarga está disponível para configurar o status de falha, marcas e uma consulta de teste personalizada.</span><span class="sxs-lookup"><span data-stu-id="142c9-495">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="142c9-496">Por padrão:</span><span class="sxs-lookup"><span data-stu-id="142c9-496">By default:</span></span>

* <span data-ttu-id="142c9-497">o `DbContextHealthCheck` chama o método `CanConnectAsync` do EF Core.</span><span class="sxs-lookup"><span data-stu-id="142c9-497">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="142c9-498">Você pode personalizar qual operação é executada durante a verificação de integridade usando sobrecargas do método `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="142c9-498">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="142c9-499">O nome da verificação de integridade é o nome do tipo `TContext`.</span><span class="sxs-lookup"><span data-stu-id="142c9-499">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="142c9-500">No aplicativo de exemplo, `AppDbContext` é fornecida para `AddDbContextCheck` e registrada como um serviço no `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-500">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-501">No aplicativo de exemplo, `UseHealthChecks` adiciona o middleware de verificações de integridade em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-501">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="142c9-502">Para executar o cenário de investigação `DbContext` usando o aplicativo de exemplo, confirme se o banco de dados especificado pela cadeia de conexão não existe na instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="142c9-502">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="142c9-503">Se o banco de dados existir, exclua-o.</span><span class="sxs-lookup"><span data-stu-id="142c9-503">If the database exists, delete it.</span></span>

<span data-ttu-id="142c9-504">Execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-504">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="142c9-505">Depois que o aplicativo estiver em execução, verifique o status da integridade fazendo uma solicitação para o ponto de extremidade `/health` em um navegador.</span><span class="sxs-lookup"><span data-stu-id="142c9-505">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="142c9-506">O banco de dados e `AppDbContext` não existem e, portanto, o aplicativo fornece a seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="142c9-506">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="142c9-507">Dispare o aplicativo de exemplo para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-507">Trigger the sample app to create the database.</span></span> <span data-ttu-id="142c9-508">Faça uma solicitação para `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="142c9-508">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="142c9-509">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-509">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="142c9-510">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="142c9-510">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="142c9-511">O banco de dados e o contexto existem e, portanto, o aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-511">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="142c9-512">Dispare o aplicativo de exemplo para excluir o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="142c9-512">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="142c9-513">Faça uma solicitação para `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="142c9-513">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="142c9-514">O aplicativo responde:</span><span class="sxs-lookup"><span data-stu-id="142c9-514">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="142c9-515">Faça uma solicitação ao ponto de extremidade `/health`.</span><span class="sxs-lookup"><span data-stu-id="142c9-515">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="142c9-516">O aplicativo fornece uma resposta não íntegra:</span><span class="sxs-lookup"><span data-stu-id="142c9-516">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="142c9-517">Investigações de preparação e atividade separadas</span><span class="sxs-lookup"><span data-stu-id="142c9-517">Separate readiness and liveness probes</span></span>

<span data-ttu-id="142c9-518">Em alguns cenários de hospedagem, é usado um par de verificações de integridade que distingue dois estados de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="142c9-518">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="142c9-519">O aplicativo está funcionando, mas ainda não está pronto para receber solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-519">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="142c9-520">Esse estado é a *preparação* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-520">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="142c9-521">O aplicativo está funcionando e respondendo a solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-521">The app is functioning and responding to requests.</span></span> <span data-ttu-id="142c9-522">Esse estado é a *atividade* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-522">This state is the app's *liveness*.</span></span>

<span data-ttu-id="142c9-523">A verificação de preparação geralmente executa um conjunto mais amplo e demorado de verificações para determinar se todos os recursos e subsistemas do aplicativo estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="142c9-523">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="142c9-524">Uma verificação de atividade apenas executa uma verificação rápida para determinar se o aplicativo está disponível para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="142c9-524">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="142c9-525">Depois que o aplicativo é aprovado na verificação de preparação, não há nenhuma necessidade de sobrecarregar o aplicativo com o conjunto caro de verificações de preparação – as verificações adicionais exigem somente a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="142c9-525">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="142c9-526">O aplicativo de exemplo contém uma verificação de integridade para relatar a conclusão da tarefa de inicialização de execução longa em um [Serviço Hospedado](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="142c9-526">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="142c9-527">A `StartupHostedServiceHealthCheck` expõe uma propriedade `StartupTaskCompleted`, que o serviço hospedado poderá definir como `true` quando sua tarefa de execução longa estiver concluída (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-527">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="142c9-528">A tarefa em segundo plano de execução longa é iniciada por um [Serviço Hospedado](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="142c9-528">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="142c9-529">Após a conclusão da tarefa, `StartupHostedServiceHealthCheck.StartupTaskCompleted` é definido como `true`:</span><span class="sxs-lookup"><span data-stu-id="142c9-529">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="142c9-530">A verificação de integridade é registrada em <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> no `Startup.ConfigureServices` juntamente com o serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="142c9-530">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="142c9-531">Como o serviço hospedado precisa definir a propriedade na verificação de integridade, a verificação de integridade também é registrada no contêiner de serviço (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-531">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="142c9-532">Chame o middleware de verificações de integridade no pipeline de processamento de aplicativos no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-532">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="142c9-533">No aplicativo de exemplo, os pontos de extremidade de verificação de integridade são criados em `/health/ready` para a verificação de preparação e em `/health/live` para a verificação de atividade.</span><span class="sxs-lookup"><span data-stu-id="142c9-533">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="142c9-534">A verificação de preparação filtra as verificações de integridade para a verificação de integridade com a marca `ready`.</span><span class="sxs-lookup"><span data-stu-id="142c9-534">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="142c9-535">A verificação de atividade filtra a `StartupHostedServiceHealthCheck` retornando `false` no [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (para obter mais informações, confira [Filtrar verificações de integridade](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="142c9-535">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="142c9-536">Para executar o cenário de configuração de preparação/atividade usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-536">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="142c9-537">Em um navegador, visite `/health/ready` várias vezes até terem decorrido 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-537">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="142c9-538">A verificação de integridade informa *Não íntegro* para os primeiros 15 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-538">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="142c9-539">Após 15 segundos, o ponto de extremidade informa *Íntegro*, que reflete a conclusão da tarefa de execução longa pelo serviço hospedado.</span><span class="sxs-lookup"><span data-stu-id="142c9-539">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="142c9-540">Este exemplo também cria um Publicador de Verificação de Integridade (implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>) que executa a primeira verificação de preparação com um atraso de dois segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-540">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="142c9-541">Para saber mais, confira a seção [Publicador de Verificação de Integridade](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="142c9-541">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="142c9-542">Exemplo do Kubernetes</span><span class="sxs-lookup"><span data-stu-id="142c9-542">Kubernetes example</span></span>

<span data-ttu-id="142c9-543">O uso de verificações de preparação e atividade separadas é útil em um ambiente como o [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="142c9-543">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="142c9-544">No Kubernetes, um aplicativo pode precisar executar um trabalho de inicialização demorado antes de aceitar solicitações, como um teste da disponibilidade do banco de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="142c9-544">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="142c9-545">O uso de verificações separadas permite que o orquestrador distinga se o aplicativo está funcionando, mas ainda não está pronto, ou se o aplicativo falhou ao ser iniciado.</span><span class="sxs-lookup"><span data-stu-id="142c9-545">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="142c9-546">Para obter mais informações sobre as investigações de preparação e atividade no Kubernetes, confira [Configurar investigações de preparação e atividade](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) na documentação do Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="142c9-546">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="142c9-547">O seguinte exemplo demonstra uma configuração de investigação de preparação do Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="142c9-547">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="142c9-548">Investigação baseada em métrica com um gravador de resposta personalizada</span><span class="sxs-lookup"><span data-stu-id="142c9-548">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="142c9-549">O aplicativo de exemplo demonstra uma verificação de integridade da memória com um gravador de resposta personalizada.</span><span class="sxs-lookup"><span data-stu-id="142c9-549">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="142c9-550">`MemoryHealthCheck` relatará um status não íntegro se o aplicativo usar mais do que um determinado limite de memória (1 GB no aplicativo de exemplo).</span><span class="sxs-lookup"><span data-stu-id="142c9-550">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="142c9-551">O <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> inclui informações de GC (Coletor de Lixo) para o aplicativo (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-551">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="142c9-552">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-552">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-553">Em vez de permitir a verificação de integridade passando-a para <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, a `MemoryHealthCheck` é registrada como um serviço.</span><span class="sxs-lookup"><span data-stu-id="142c9-553">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="142c9-554">Todos os serviços registrados da <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> estão disponíveis para os serviços de verificação de integridade e middleware.</span><span class="sxs-lookup"><span data-stu-id="142c9-554">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="142c9-555">Recomendamos registrar os serviços de verificação de integridade como serviços Singleton.</span><span class="sxs-lookup"><span data-stu-id="142c9-555">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="142c9-556">No aplicativo de exemplo (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-556">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="142c9-557">Chame o middleware de verificações de integridade no pipeline de processamento de aplicativos no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-557">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="142c9-558">Um representante `WriteResponse` é fornecido para a propriedade `ResponseWriter` para gerar uma resposta JSON personalizada quando a verificação de integridade é executada:</span><span class="sxs-lookup"><span data-stu-id="142c9-558">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="142c9-559">O método `WriteResponse` formata o `CompositeHealthCheckResult` em um objeto JSON e produz a saída JSON para a resposta da verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-559">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="142c9-560">Para executar a investigação baseada em métrica com a saída do gravador de resposta personalizada usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-560">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="142c9-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui cenários de verificação de integridade baseada em métrica, incluindo verificações de investigação de atividade de valor máximo e armazenamento em disco.</span><span class="sxs-lookup"><span data-stu-id="142c9-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="142c9-562">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-562">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="142c9-563">Filtrar por porta</span><span class="sxs-lookup"><span data-stu-id="142c9-563">Filter by port</span></span>

<span data-ttu-id="142c9-564">A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> com uma porta restringe as solicitações de verificação de integridade à porta especificada.</span><span class="sxs-lookup"><span data-stu-id="142c9-564">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="142c9-565">Isso normalmente é usado em um ambiente de contêiner para expor uma porta para os serviços de monitoramento.</span><span class="sxs-lookup"><span data-stu-id="142c9-565">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="142c9-566">O aplicativo de exemplo configura a porta usando o [Provedor de Configuração de Variáveis de Ambiente](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="142c9-566">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="142c9-567">A porta é definida no arquivo *launchSettings.json* e passada para o provedor de configuração por meio de uma variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="142c9-567">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="142c9-568">Você também precisa configurar o servidor para escutar as solicitações na porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-568">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="142c9-569">Para usar o aplicativo de exemplo para demonstrar a configuração de porta de gerenciamento, crie o arquivo *launchSettings.json* em uma pasta *Properties*.</span><span class="sxs-lookup"><span data-stu-id="142c9-569">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="142c9-570">O arquivo de *Propriedades/launchSettings. JSON* a seguir no aplicativo de exemplo não está incluído nos arquivos de projeto do aplicativo de exemplo e deve ser criado manualmente:</span><span class="sxs-lookup"><span data-stu-id="142c9-570">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="142c9-571">Registre os serviços de verificação de integridade com <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="142c9-571">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="142c9-572">A chamada a <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> especifica a porta de gerenciamento (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="142c9-572">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="142c9-573">Evite a criação do arquivo *launchSettings.json* no aplicativo de exemplo definindo as URLs e a porta de gerenciamento explicitamente no código.</span><span class="sxs-lookup"><span data-stu-id="142c9-573">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="142c9-574">Em *Program.cs*, em que o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> é criado, adicione uma chamada a <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> e forneça o ponto de extremidade da resposta normal do aplicativo e o ponto de extremidade da porta de gerenciamento.</span><span class="sxs-lookup"><span data-stu-id="142c9-574">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="142c9-575">Em *ManagementPortStartup.cs*, em que <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> é chamado, especifique a porta de gerenciamento explicitamente.</span><span class="sxs-lookup"><span data-stu-id="142c9-575">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="142c9-576">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="142c9-576">*Program.cs*:</span></span>
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
> <span data-ttu-id="142c9-577">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="142c9-577">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="142c9-578">Para executar o cenário de configuração de porta de gerenciamento usando o aplicativo de exemplo, execute o seguinte comando na pasta do projeto em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="142c9-578">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="142c9-579">Distribuir uma biblioteca de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-579">Distribute a health check library</span></span>

<span data-ttu-id="142c9-580">Para distribuir uma verificação de integridade como uma biblioteca:</span><span class="sxs-lookup"><span data-stu-id="142c9-580">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="142c9-581">Escreva uma verificação de integridade que implementa a interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> como uma classe autônoma.</span><span class="sxs-lookup"><span data-stu-id="142c9-581">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="142c9-582">A classe pode depender da [DI (injeção de dependência)](xref:fundamentals/dependency-injection), da ativação de tipo e das [opções nomeadas](xref:fundamentals/configuration/options) para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="142c9-582">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="142c9-583">Na lógica de verificações de integridade de `CheckHealthAsync`:</span><span class="sxs-lookup"><span data-stu-id="142c9-583">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="142c9-584">`data1` e `data2` são usados no método para executar a lógica de verificação de integridade da investigação.</span><span class="sxs-lookup"><span data-stu-id="142c9-584">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="142c9-585">`AccessViolationException` é manipulado.</span><span class="sxs-lookup"><span data-stu-id="142c9-585">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="142c9-586">Quando ocorre uma <xref:System.AccessViolationException>, a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> é retornada com a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> para permitir que os usuários configurem o status de falha de verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-586">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="142c9-587">Escreva um método de extensão com parâmetros que o aplicativo de consumo chama em seu método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="142c9-587">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="142c9-588">No seguinte exemplo, suponha a seguinte assinatura de método de verificação de integridade:</span><span class="sxs-lookup"><span data-stu-id="142c9-588">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="142c9-589">A assinatura anterior indica que a `ExampleHealthCheck` exige dados adicionais para processar a lógica de investigação de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-589">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="142c9-590">Os dados são fornecidos para o representante usado para criar a instância de verificação de integridade quando a verificação de integridade é registrada em um método de extensão.</span><span class="sxs-lookup"><span data-stu-id="142c9-590">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="142c9-591">No seguinte exemplo, o chamador especifica itens opcionais:</span><span class="sxs-lookup"><span data-stu-id="142c9-591">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="142c9-592">nome da verificação de integridade (`name`).</span><span class="sxs-lookup"><span data-stu-id="142c9-592">health check name (`name`).</span></span> <span data-ttu-id="142c9-593">Se `null`, `example_health_check` é usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-593">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="142c9-594">ponto de dados de cadeia de caracteres para a verificação de integridade (`data1`).</span><span class="sxs-lookup"><span data-stu-id="142c9-594">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="142c9-595">ponto de dados de inteiro para a verificação de integridade (`data2`).</span><span class="sxs-lookup"><span data-stu-id="142c9-595">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="142c9-596">Se `null`, `1` é usado.</span><span class="sxs-lookup"><span data-stu-id="142c9-596">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="142c9-597">status de falha (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="142c9-597">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="142c9-598">O padrão é `null`.</span><span class="sxs-lookup"><span data-stu-id="142c9-598">The default is `null`.</span></span> <span data-ttu-id="142c9-599">Se `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) for informado devido a um status de falha.</span><span class="sxs-lookup"><span data-stu-id="142c9-599">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="142c9-600">marcas (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="142c9-600">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="142c9-601">Publicador de Verificação de Integridade</span><span class="sxs-lookup"><span data-stu-id="142c9-601">Health Check Publisher</span></span>

<span data-ttu-id="142c9-602">Quando um <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> é adicionado ao contêiner de serviços, o sistema de verificação de integridade periodicamente executa sua verificação de integridade e chama `PublishAsync` com o resultado.</span><span class="sxs-lookup"><span data-stu-id="142c9-602">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="142c9-603">Isso é útil em um cenário de sistema de monitoramento de integridade baseada em push que espera que cada processo chame o sistema de monitoramento periodicamente para determinar a integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-603">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="142c9-604">A interface <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> tem um único método:</span><span class="sxs-lookup"><span data-stu-id="142c9-604">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="142c9-605"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> permite que você defina:</span><span class="sxs-lookup"><span data-stu-id="142c9-605"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="142c9-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; O atraso inicial aplicado após o aplicativo começar a executar instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="142c9-607">O atraso é aplicado uma vez na inicialização e não ocorre em iterações subsequentes.</span><span class="sxs-lookup"><span data-stu-id="142c9-607">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="142c9-608">O valor padrão é cinco segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-608">The default value is five seconds.</span></span>
* <span data-ttu-id="142c9-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; O período da execução <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="142c9-610">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-610">The default value is 30 seconds.</span></span>
* <span data-ttu-id="142c9-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> for `null` (padrão), o serviço publicador de verificação de integridade executará todas as verificações de integridade registradas.</span><span class="sxs-lookup"><span data-stu-id="142c9-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="142c9-612">Para executar um subconjunto das verificações de integridade, forneça uma função que filtre o conjunto de verificações.</span><span class="sxs-lookup"><span data-stu-id="142c9-612">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="142c9-613">O predicado é avaliado a cada período.</span><span class="sxs-lookup"><span data-stu-id="142c9-613">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="142c9-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; O tempo limite para a execução de verificações de integridade em todas as instâncias <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="142c9-615">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> para executar sem um tempo limite.</span><span class="sxs-lookup"><span data-stu-id="142c9-615">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="142c9-616">O valor padrão é 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-616">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="142c9-617">Na versão para ASP.NET Core 2.2, a configuração de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> não é respeitada pela implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>; ela define o valor de <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="142c9-617">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="142c9-618">Esse problema foi resolvido no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="142c9-618">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="142c9-619">No aplicativo de exemplo, `ReadinessPublisher` é uma implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="142c9-619">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="142c9-620">O status da verificação de integridade é registrado para cada verificação:</span><span class="sxs-lookup"><span data-stu-id="142c9-620">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="142c9-621">Informações (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) se o status das verificações de integridade for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span><span class="sxs-lookup"><span data-stu-id="142c9-621">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="142c9-622">Erro (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) se o status for <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> ou <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="142c9-622">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="142c9-623">No exemplo `LivenessProbeStartup` do aplicativo de amostra, a verificação de preparação `StartupHostedService` tem um atraso de inicialização de dois segundos, e executa a verificação a cada 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="142c9-623">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="142c9-624">Para ativar a implementação <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, o exemplo registra `ReadinessPublisher` como um serviço singleton no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="142c9-624">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="142c9-625">A solução alternativa a seguir permite a adição de uma instância <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ao contêiner de serviço quando um ou mais serviços hospedados já tiverem sido adicionados ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="142c9-625">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="142c9-626">Essa solução alternativa não será necessária no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="142c9-626">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="142c9-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) inclui publicadores para vários sistemas, incluindo [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="142c9-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="142c9-628">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) não é mantido ou tem suporte da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="142c9-628">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="142c9-629">Restringir verificações de integridade com MapWhen</span><span class="sxs-lookup"><span data-stu-id="142c9-629">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="142c9-630">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> para ramificar condicionalmente o pipeline de solicitação para pontos de extremidade de verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="142c9-630">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="142c9-631">No exemplo a seguir, `MapWhen` ramifica o pipeline de solicitação para ativar o middleware de verificações de integridade se uma solicitação GET for recebida para o ponto de extremidade de `api/HealthCheck`:</span><span class="sxs-lookup"><span data-stu-id="142c9-631">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="142c9-632">Para obter mais informações, consulte <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="142c9-632">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
