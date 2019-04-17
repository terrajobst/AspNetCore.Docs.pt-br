---
title: Injeção de dependência no ASP.NET Core
author: guardrex
description: Saiba como o ASP.NET Core implementa a injeção de dependência e como usá-la.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: da6ddf1f0efd164a58f017ff55ce216bbefa7cc6
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068317"
---
# <a name="dependency-injection-in-aspnet-core"></a>Injeção de dependência no ASP.NET Core

Por [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) e [Luke Latham](https://github.com/guardrex)

O ASP.NET Core dá suporte ao padrão de design de software de DI (injeção de dependência), que é uma técnica para conseguir [IoC (inversão de controle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

Para obter mais informações específicas sobre injeção de dependência em controladores de MVC, consulte <xref:mvc/controllers/dependency-injection>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Visão geral da injeção de dependência

Uma *dependência* é qualquer objeto exigido por outro objeto. Examine a classe `MyDependency` a seguir com um método `WriteMessage` do qual outras classes em um aplicativo dependem:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Uma instância da classe `MyDependency` pode ser criada para tornar o método `WriteMessage` disponível para uma classe. A classe `MyDependency` é uma dependência da classe `IndexModel`:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

A classe cria e depende diretamente da instância `MyDependency`. As dependências de código (como no exemplo anterior) são problemáticas e devem ser evitadas por estes motivos:

* Para substituir `MyDependency` por uma implementação diferente, a classe deve ser modificada.
* Se `MyDependency` tiver dependências, elas deverão ser configuradas pela classe. Em um projeto grande com várias classes dependendo da `MyDependency`, o código de configuração fica pulverizado por todo o aplicativo.
* É difícil testar a unidade dessa implementação. O aplicativo deve usar uma simulação ou stub da classe `MyDependency`, o que não é possível com essa abordagem.

Injeção de dependência trata desses problemas da seguinte maneira:

* Usando uma interface para abstrair a implementação da dependência.
* Registrando a dependência em um contêiner de serviço. O ASP.NET Core fornece um contêiner de serviço interno, o [IServiceProvider](/dotnet/api/system.iserviceprovider). Os serviços são registrados no método `Startup.ConfigureServices` do aplicativo.
* *Injeção* do serviço no construtor da classe na qual ele é usado. A estrutura assume a responsabilidade de criar uma instância da dependência e de descartá-la quando não for mais necessária.

No [exemplo de aplicativo](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), a interface `IMyDependency` define um método que o serviço fornece ao aplicativo:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Essa interface é implementada por um tipo concreto, `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` solicita um [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) em seu construtor. Não é incomum usar a injeção de dependência de uma maneira encadeada. Por sua vez, cada dependência solicitada solicita suas próprias dependências. O contêiner resolve as dependências no grafo e retorna o serviço totalmente resolvido. O conjunto de dependências que precisa ser resolvido normalmente é chamado de *árvore de dependência*, *grafo de dependência* ou *grafo de objeto*.

`IMyDependency` e `ILogger<TCategoryName>` devem ser registrados no contêiner do serviço. `IMyDependency` está registrado em `Startup.ConfigureServices`. `ILogger<TCategoryName>` é registrado pela infraestrutura de abstrações de registro em log; portanto, é um [serviço fornecido por estrutura registrado por padrão](#framework-provided-services) pela estrutura.

O contêiner resolve `ILogger<TCategoryName>` aproveitando os [tipos abertos (genéricos)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminando a necessidade de registrar todo [tipo construído (genérico)](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

No exemplo de aplicativo, o serviço `IMyDependency` está registrado com o tipo concreto `MyDependency`. O registro define o escopo do tempo de vida do serviço para o tempo de vida de uma única solicitação. Descreveremos posteriormente neste tópico os [tempos de vida do serviço](#service-lifetimes).

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Cada método de extensão `services.Add{SERVICE_NAME}` adiciona (e possivelmente configura) serviços. Por exemplo, `services.AddMvc()` adiciona os serviços exigidos pelo MVC e por Razor Pages. Recomendamos que os aplicativos sigam essa convenção. Coloque os métodos de extensão no namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) para encapsular grupos de registros de serviço.

Se o construtor do serviço exigir um [tipo interno](/dotnet/csharp/language-reference/keywords/built-in-types-table), como `string`, o tipo poderá ser injetado usando a [configuração](xref:fundamentals/configuration/index) ou o [padrão de opções](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Uma instância do serviço é solicitada por meio do construtor de uma classe, onde o serviço é usado e atribuído a um campo particular. O campo é usado para acessar o serviço conforme o necessário na classe.

No exemplo de aplicativo, a instância `IMyDependency` é solicitada e usada para chamar o método `WriteMessage` do serviço:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a>Serviços fornecidos pela estrutura

O método `Startup.ConfigureServices` é responsável por definir os serviços usados pelo aplicativo, incluindo recursos de plataforma como o Entity Framework Core e o ASP.NET Core MVC. Inicialmente, a `IServiceCollection` fornecida ao `ConfigureServices` tem os seguintes serviços definidos (dependendo de [como o host foi configurado](xref:fundamentals/index#host)):

| Tipo de serviço | Tempo de vida |
| ------------ | -------- |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | Transitório |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | Singleton |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartup](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | Transitório |
| [Microsoft.AspNetCore.Hosting.Server.IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | Singleton |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | Transitório |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](/dotnet/api/microsoft.extensions.logging.ilogger) | Singleton |
| [Microsoft.Extensions.Logging.ILoggerFactory](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | Singleton |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | Singleton |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | Transitório |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.ioptions-1) | Singleton |
| [System.Diagnostics.DiagnosticSource](/dotnet/core/api/system.diagnostics.diagnosticsource) | Singleton |
| [System.Diagnostics.DiagnosticListener](/dotnet/core/api/system.diagnostics.diagnosticlistener) | Singleton |

Quando um método de extensão de coleta do serviço estiver disponível para registrar um serviço (e seus serviços dependentes, se for necessário), a convenção é usar um único método de extensão `Add{SERVICE_NAME}` para registrar todos os serviços exigidos por esse serviço. O código a seguir é um exemplo de como adicionar outros serviços ao contêiner usando os métodos de extensão [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) e [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

Para saber mais, confira a [Classe ServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) na documentação da API.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Escolha um tempo de vida apropriado para cada serviço registrado. Os serviços do ASP.NET Core podem ser configurados com os seguintes tempos de vida:

**Transitório**

Serviços temporários de tempo de vida são criados cada vez que são solicitados pelo contêiner de serviço. Esse tempo de vida funciona melhor para serviços leves e sem estado.

**Com escopo**

Os serviços com tempo de vida com escopo são criados uma vez por solicitação de cliente (conexão).

> [!WARNING]
> Ao usar um serviço com escopo em um middleware, injete o serviço no método `Invoke` ou `InvokeAsync`. Não injete por meio de injeção de construtor porque isso força o serviço a se comportar como um singleton. Para obter mais informações, consulte <xref:fundamentals/middleware/index>.

**Singleton**

Serviços de tempo de vida singleton são criados na primeira solicitação (ou quando `ConfigureServices` é executado e uma instância é especificada com o registro do serviço). Cada solicitação subsequente usa a mesma instância. Se o aplicativo exigir um comportamento de singleton, recomendamos permitir que o contêiner do serviço gerencie o tempo de vida do serviço. Não implemente o padrão de design singleton e forneça o código de usuário para gerenciar o tempo de vida do objeto na classe.

> [!WARNING]
> É perigoso resolver um serviço com escopo de um singleton. Pode fazer com que o serviço tenha um estado incorreto durante o processamento das solicitações seguintes.

### <a name="constructor-injection-behavior"></a>Comportamento da injeção de construtor

Os serviços podem ser resolvidos por dois mecanismos:

* `IServiceProvider`
* [ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permite a criação de objetos sem registro do serviço no contêiner de injeção de dependência. `ActivatorUtilities` é usado com abstrações voltadas ao usuário, como Auxiliares de Marca, controladores MVC e associadores de modelo.

Os construtores podem aceitar argumentos que não são fornecidos pela injeção de dependência, mas que precisam atribuir valores padrão.

Quando os serviços são resolvidos por `IServiceProvider` ou `ActivatorUtilities`, a injeção do construtor exige um construtor *público*.

Quando os serviços são resolvidos por `ActivatorUtilities`, a injeção de construtor exige a existência de apenas de um construtor aplicável. Há suporte para sobrecargas de construtor, mas somente uma sobrecarga pode existir, cujos argumentos podem ser todos atendidos pela injeção de dependência.

## <a name="entity-framework-contexts"></a>Contextos de Entity Framework

Contextos do Entity Framework geralmente são adicionados ao contêiner de serviço usando o [tempo de vida com escopo](#service-lifetimes), pois o escopo de operações de banco de dados de aplicativo Web normalmente é para a solicitação do cliente. O tempo de vida padrão tem escopo se um tempo de vida não é especificado por uma sobrecarga de <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> ao registrar o contexto de banco de dados. Serviços com um determinado tempo de vida não devem usar um contexto de banco de dados com um tempo de vida mais curto que aquele do serviço.

## <a name="lifetime-and-registration-options"></a>Opções de tempo de vida e de registro

Para demonstrar a diferença entre as opções de tempo de vida e de registro, considere as interfaces a seguir, que representam tarefas como uma operação com um identificador exclusivo, `OperationId`. Dependendo de como o tempo de vida de um serviço de operações é configurado para as interfaces a seguir, o contêiner fornece a mesma instância do serviço, ou outra diferente, quando recebe uma solicitação de uma classe:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

As interfaces são implementadas na classe `Operation`. O construtor `Operation` gera um GUID, caso um não seja fornecido:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Há um `OperationService` registrado que depende de cada um dos outros `Operation` tipos. Quando `OperationService` é solicitado por meio da injeção de dependência, ele recebe a uma nova instância de cada serviço, ou uma instância existente com base no tempo de vida do serviço dependente.

* Quando os serviços temporários forem criados mediante solicitação do contêiner, o `OperationId` do serviço `IOperationTransient` será diferente do `OperationId` do `OperationService`. `OperationService` recebe uma nova instância da classe `IOperationTransient`. A nova instância produz outro `OperationId`.
* Quando os serviços com escopo forem criados por solicitação do cliente, o `OperationId` do serviço `IOperationScoped` será o mesmo que o `OperationService` dentro de uma solicitação do cliente. Em todas as solicitações do cliente, os dois serviços compartilham um valor de `OperationId` diferente.
* Quando os serviços singleton e de instância singleton forem criados uma vez e usados em todas as solicitações de cliente e em todos os serviços, o `OperationId` será constante entre todas as solicitações de serviço.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

Em `Startup.ConfigureServices`, cada tipo é adicionado ao contêiner de acordo com seu tempo de vida nomeado:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

O serviço `IOperationSingletonInstance` está usando uma instância específica com uma ID conhecida de `Guid.Empty`. Fica claro quando esse tipo está em uso (seu GUID contém apenas zeros).

O exemplo de aplicativo demonstra os tempos de vida do objeto dentro e entre solicitações individuais. O exemplo de aplicativo `IndexModel` solicita cada tipo `IOperation` e o `OperationService`. Depois, a página exibe todos os valores de `OperationId` da classe de modelo de página e do serviço por meio de atribuições de propriedade:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

As duas saídas a seguir mostram os resultados das duas solicitações:

**Primeira solicitação:**

Operações do controlador:

Transitória: d233e165-f417-469b-a866-1cf1935d2518  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Com escopo: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

**Segunda solicitação:**

Operações do controlador:

Transitória: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

`OperationService` operações:

Transitória: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Com escopo: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instância: 00000000-0000-0000-0000-000000000000

Observe qual dos valores de `OperationId` varia em uma solicitação, e entre as solicitações:

* Os objetos *transitórios* sempre são diferentes. O valor `OperationId` transitório da primeira e da segunda solicitações de cliente é diferente para as duas operações `OperationService` e em todas as solicitações de cliente. Uma nova instância é fornecida para cada solicitação de serviço e solicitação de cliente.
* Objetos *com escopo* são os mesmos em uma solicitação de cliente, mas diferentes entre solicitações de cliente.
* Os objetos *singleton* são os mesmos para cada objeto e solicitação, independentemente de uma instância `Operation` ser fornecida em `ConfigureServices`.

## <a name="call-services-from-main"></a>Chamar os serviços desde o principal

Crie um [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) com [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) para resolver um serviço com escopo dentro do escopo do aplicativo. Essa abordagem é útil para acessar um serviço com escopo durante a inicialização para executar tarefas de inicialização. O exemplo a seguir mostra como obter um contexto para o `MyScopedService` em `Program.Main`:

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a>Validação de escopo

Quando o aplicativo está em execução no ambiente de desenvolvimento, o provedor de serviço padrão executa verificações para saber se:

* Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.
* Os serviços com escopo não são injetados direta ou indiretamente em singletons.

O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado. O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.

Os serviços com escopo são descartados pelo contêiner que os criou. Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado. A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.

Para obter mais informações, consulte <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Serviços de solicitação

Os serviços disponíveis em uma solicitação do ASP.NET de `HttpContext` são expostos por meio da coleção [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices).

Os Serviços de Solicitação representam os serviços configurados e solicitados como parte do aplicativo. Quando os objetos especificam dependências, elas são atendidas pelos tipos encontrados em `RequestServices`, não em `ApplicationServices`.

Em geral, o aplicativo não deve usar essas propriedades diretamente. Em vez disso, solicite os tipos exigidos pelas classes por meio de construtores de classe e permita que a estrutura injete as dependências. Isso resulta em classes que são mais fáceis de testar.

> [!NOTE]
> Prefira solicitar dependências como parâmetros de construtor para acessar a coleção `RequestServices`.

## <a name="design-services-for-dependency-injection"></a>Projetar serviços para injeção de dependência

As melhores práticas são:

* Projetar serviços para usar a injeção de dependência a fim de obter suas dependências.
* Evite chamadas de método estático com estado.
* Evite a instanciação direta das classes dependentes em serviços. A instanciação direta acopla o código a uma implementação específica.
* Verifique as classes de aplicativo pequenas, bem fatoradas e facilmente testadas.

Se parecer que uma classe tem muitas dependências injetadas, normalmente é um sinal de que a classe tem muitas responsabilidades e está violando o [Princípio da Responsabilidade Única (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Tente refatorar a classe movendo algumas de suas responsabilidades para uma nova classe. Lembre-se de que as classes de modelo de página de Razor Pages e as classes do controlador de MVC devem se concentrar em questões de interface do usuário. Os detalhes de implementação de acesso aos dados e as regras de negócios devem ser mantidos em classes apropriadas a esses [interesses separados](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Descarte de serviços

O contêiner chama `Dispose` para os tipos `IDisposable` criados por ele. Se uma instância for adicionada ao contêiner pelo código do usuário, ele não será descartado automaticamente.

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

## <a name="default-service-container-replacement"></a>Substituição do contêiner de serviço padrão

O contêiner de serviço interno deve atender às necessidades da estrutura e da maioria dos aplicativos do consumidor. É recomendado usar o contêiner interno, a menos que você precise de um recurso específico que não seja compatível com ele. Alguns dos recursos compatíveis com contêineres de terceiros não encontrados no contêiner interno:

* Injeção de propriedade
* Injeção com base no nome
* Contêineres filho
* Gerenciamento de tempo de vida personalizado
* `Func<T>` suporte para inicialização lenta

Confira o [arquivo readme.md de injeção de dependência](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) para obter uma lista de alguns dos contêineres que dão suporte a adaptadores.

O exemplo a seguir substitui o contêiner interno por [Autofac](https://autofac.org/):

* Instale os pacotes de contêiner apropriados:

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* Configure o contêiner no `Startup.ConfigureServices` e retorne um `IServiceProvider`:

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    Para usar um contêiner de terceiros, `Startup.ConfigureServices` deve retornar `IServiceProvider`.

* Configure o Autofac em `DefaultModule`:

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

Em tempo de execução, o Autofac é usado para resolver tipos e injetar dependências. Para saber mais sobre como usar o Autofac com o ASP.NET Core, consulte a [documentação do Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Acesso thread-safe

Crie serviços singleton thread-safe. Se um serviço singleton tiver uma dependência em um serviço transitório, o serviço transitório também precisará ter acesso thread-safe, dependendo de como ele é usado pelo singleton.

O método de fábrica de um único serviço, como o segundo argumento para [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), não precisa ser thread-safe. Como um construtor do tipo (`static`), ele tem garantia de ser chamado uma vez por um único thread.

## <a name="recommendations"></a>Recomendações

* `async/await` e a resolução de serviço baseada em `Task` não é compatível. O C# não é compatível com os construtores assíncronos. Portanto, o padrão recomendado é usar os métodos assíncronos após a resolução síncrona do serviço.

* Evite armazenar dados e a configuração diretamente no contêiner do serviço. Por exemplo, o carrinho de compras de um usuário normalmente não deve ser adicionado ao contêiner do serviço. A configuração deve usar o [padrão de opções](xref:fundamentals/configuration/options). Da mesma forma, evite objetos de "suporte de dados" que existem somente para permitir o acesso a outro objeto. É melhor solicitar o item real por meio da DI.

* Evite o acesso estático aos serviços (por exemplo, digitando estaticamente [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) para usar em outro lugar).

* Evite usar o *padrão do localizador de serviço*. Por exemplo, não invoque <xref:System.IServiceProvider.GetService*> para obter uma instância de serviço quando for possível usar a DI:

  **Incorreto:**

  ```csharp
  public void MyMethod()
  {
      var options = 
          _services.GetService<IOptionsMonitor<MyOptions>>();
      var option = options.CurrentValue.Option;

      ...
  }
  ```

  **Correto**:

  ```csharp
  private readonly MyOptions _options;

  public MyClass(IOptionsMonitor<MyOptions> options)
  {
      _options = options.CurrentValue;
  }

  public void MyMethod()
  {
      var option = _options.Option;

      ...
  }
  ```

* Outra variação de localizador de serviço a ser evitada é injetar um alocador que resolve as dependências em tempo de execução. Essas duas práticas misturam estratégias de [inversão de controle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

* Evite o acesso estático a `HttpContext` (por exemplo, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).

Como todos os conjuntos de recomendações, talvez você encontre situações em que é necessário ignorar uma recomendação. As exceções são raras &mdash;principalmente casos especiais dentro da própria estrutura.

A DI é uma *alternativa* aos padrões de acesso a objeto estático/global. Talvez você não obtenha os benefícios da DI se combiná-lo com o acesso a objeto estático.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Como gravar um código limpo no ASP.NET Core com injeção de dependência (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princípio de Dependências Explícitas](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inversão de Contêineres de Controle e o Padrão de Injeção de Dependência (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Como registrar um serviço com várias interfaces na DI do ASP.NET Core](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
