---
title: Inicialização de aplicativo no ASP.NET Core
author: tdykstra
description: Saiba como a classe Startup no ASP.NET Core configura serviços e o pipeline de solicitação do aplicativo.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/17/2019
uid: fundamentals/startup
ms.openlocfilehash: 7e1741d2bed15f36a967713a2f9bd0d93801c8d0
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874948"
---
# <a name="app-startup-in-aspnet-core"></a>Inicialização de aplicativo no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) e [Steve Smith](https://ardalis.com)

A classe `Startup` configura serviços e o pipeline de solicitação do aplicativo.

## <a name="the-startup-class"></a>A classe Startup

Os aplicativos do ASP.NET Core usam uma classe `Startup`, que é chamada de `Startup` por convenção. A classe `Startup`:

* Opcionalmente, inclua um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> para configurar os *serviços* do aplicativo. Um serviço é um componente reutilizável que fornece a funcionalidade do aplicativo. Os serviços estão configurados&mdash;também descritos como *registrados*&mdash;em `ConfigureServices` e consumidos no aplicativo por meio da [DI (injeção de dependência)](xref:fundamentals/dependency-injection) ou <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.
* Inclui um método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> para criar o pipeline de processamento de solicitações do aplicativo.

`ConfigureServices` e `Configure` são chamados pelo tempo de execução do ASP.NET Core quando o aplicativo é iniciado:

[!code-csharp[](startup/sample_snapshot/Startup1.cs?highlight=4,10)]

A classe `Startup` é especificada para o aplicativo quando o [host](xref:fundamentals/index#host) do aplicativo é criado. O host do aplicativo é compilado quando `Build` é chamado no construtor do host na classe `Program`. A classe `Startup` geralmente é especificada chamando o método [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) no construtor do host:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=10)]

O host fornece serviços que estão disponíveis para o construtor de classe `Startup`. O aplicativo adiciona serviços adicionais por meio de `ConfigureServices`. Os serviços de aplicativos e o host ficam, então, disponíveis em `Configure` e em todo o aplicativo.

Um uso comum da [injeção de dependência](xref:fundamentals/dependency-injection) na classe `Startup` é injetar:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> para configurar serviços pelo ambiente.
* <xref:Microsoft.Extensions.Configuration.IConfiguration> para ler a configuração.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> para criar um agente em `Startup.ConfigureServices`.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Uma alternativa a injetar `IHostingEnvironment` é usar uma abordagem baseada em convenções. Quando o aplicativo define classes `Startup` separadas para ambientes diferentes (por exemplo, `StartupDevelopment`), a classe `Startup` apropriada é selecionada no tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se o aplicativo for executado no ambiente de desenvolvimento e incluir uma classe `Startup` e uma classe `StartupDevelopment`, a classe `StartupDevelopment` será usada. Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Para saber mais sobre o host, confira [O host](xref:fundamentals/index#host). Para obter informações sobre como tratar erros durante a inicialização, consulte [Tratamento de exceção na inicialização](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>O método ConfigureServices

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> é:

* Opcional.
* Chamado pelo host antes do método `Configure` para configurar os serviços do aplicativo.
* Onde as [opções de configuração](xref:fundamentals/configuration/index) são definidas por convenção.

O padrão típico consiste em chamar todos os métodos `Add{Service}` e, em seguida, chamar todos os métodos `services.Configure{Service}`. Por exemplo, veja o tópico [Configurar serviços de identidade](xref:security/authentication/identity#pw).

O host pode configurar alguns serviços antes que métodos `Startup` sejam chamados. Para obter mais informações, confira [O host](xref:fundamentals/index#host).

Para recursos que exigem uma configuração significativa, há métodos de extensão `Add{Service}` em <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>. Um aplicativo ASP.NET Core típico registra serviços para o Entity Framework, Identity e MVC:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Adicionar serviços ao contêiner de serviços os torna disponíveis dentro do aplicativo e no método `Configure`. Os serviços são resolvidos por meio da [injeção de dependência](xref:fundamentals/dependency-injection) ou de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.

Confira [SetCompatibilityVersion](xref:mvc/compatibility-version) para obter mais informações sobre `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>O método Configure

O método <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> é usado para especificar como o aplicativo responde às solicitações HTTP. O pipeline de solicitação é configurado adicionando componentes de [middleware](xref:fundamentals/middleware/index) a uma instância de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>. `IApplicationBuilder` está disponível para o método `Configure`, mas não é registrado no contêiner de serviço. A hospedagem cria um `IApplicationBuilder` e o passa diretamente para `Configure`.

Os [modelos do ASP.NET Core](/dotnet/core/tools/dotnet-new) configuram o pipeline com suporte para:

* [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page)
* [Manipulador de exceção](xref:fundamentals/error-handling#exception-handler-page)
* [Segurança de Transporte Estrita de HTTP (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [Redirecionamento de HTTPS](xref:security/enforcing-ssl)
* [Arquivos estáticos](xref:fundamentals/static-files)
* [RGPD (Regulamento Geral sobre a Proteção de Dados) da UE](xref:security/gdpr)
* [MVC](xref:mvc/overview) do ASP.NET Core e [Razor Pages](xref:razor-pages/index)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Cada método de extensão `Use` adiciona um ou mais componentes de middleware ao pipeline de solicitação. Por exemplo, o método de extensão `UseMvc` adiciona o [Middleware de roteamento](xref:fundamentals/routing) ao pipeline de solicitação e configura o [MVC](xref:mvc/overview) como o manipulador padrão.

Cada componente de middleware no pipeline de solicitação é responsável por invocar o próximo componente no pipeline ou causar um curto-circuito da cadeia, se apropriado. Se o curto-circuito não ocorrer ao longo da cadeia de middleware, cada middleware terá uma segunda chance de processar a solicitação antes que ela seja enviada ao cliente.

Serviços adicionais, como `IHostingEnvironment` e `ILoggerFactory`, também podem ser especificados na assinatura do método `Configure`. Quando especificados, serviços adicionais são injetados quando estão disponíveis.

Para obter mais informações de como usar o `IApplicationBuilder` e a ordem de processamento de middleware, confira <xref:fundamentals/middleware/index>.

## <a name="convenience-methods"></a>Métodos de conveniência

Para configurar serviços e o pipeline de processamento de solicitação sem usar uma classe `Startup`, chame os métodos de conveniência `ConfigureServices` e `Configure` no construtor do host. Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras. Se houver várias chamadas de método `Configure`, a última chamada de `Configure` será usada.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Estender a inicialização com filtros de inicialização

Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> para configurar o middleware no início ou no final do pipeline do middleware [Configure](#the-configure-method) de um aplicativo. `IStartupFilter` é útil para garantir que um middleware seja executado antes ou depois do middleware adicionado pelas bibliotecas no início ou no final do pipeline de processamento de solicitação do aplicativo.

`IStartupFilter` implementa um único método, <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, que recebe e retorna um `Action<IApplicationBuilder>`. Um <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> define uma classe para configurar o pipeline de solicitação do aplicativo. Para obter mais informações, confira [Criar um pipeline de middleware com o IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Cada `IStartupFilter` implementa uma ou mais middlewares no pipeline de solicitação. Os filtros são invocados na ordem em que foram adicionados ao contêiner de serviço. Filtros podem adicionar middleware antes ou depois de passar o controle para o filtro seguinte, de modo que eles são acrescentados ao início ou no final do pipeline de aplicativo.

O exemplo a seguir demonstra como registrar um middleware com `IStartupFilter`.

O middleware `RequestSetOptionsMiddleware` define um valor de opção de um parâmetro de cadeia de caracteres de consulta:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

O `RequestSetOptionsMiddleware` é configurado na classe `RequestSetOptionsStartupFilter`:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

O `IStartupFilter` é registrado no contêiner de serviço em <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> e aumenta `Startup` de fora da classe `Startup`:

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Quando um parâmetro de cadeia de caracteres de consulta para `option` é fornecido, o middleware processa a atribuição de valor antes que o middleware do MVC renderize a resposta:

![Janela do navegador mostrando a página de Índice renderizada. O valor de Option é renderizado como "From Middleware" com base na solicitação da página com o parâmetro da cadeia de caracteres de consulta e o valor da opção definido como "From Middleware".](startup/_static/index.png)

A ordem de execução do middleware é definida pela ordem dos registros `IStartupFilter`:

* Várias implementações de `IStartupFilter` podem interagir com os mesmos objetos. Se a ordem for importante, ordene seus registros de serviço `IStartupFilter` para corresponder à ordem em que os middlewares devem ser executados.
* Bibliotecas podem adicionar middleware com uma ou mais implementações de `IStartupFilter` que são executadas antes ou depois de outro middleware de aplicativo registrado com `IStartupFilter`. Para invocar um middleware `IStartupFilter` antes de um middleware adicionado pelo `IStartupFilter` de uma biblioteca, posicione o registro do serviço antes da biblioteca ser adicionada ao contêiner de serviço. Para invocá-lo posteriormente, posicione o registro do serviço após a biblioteca ser adicionada.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Adicionar configuração na inicialização usando um assembly externo

Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo. Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Recursos adicionais

* [O host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
