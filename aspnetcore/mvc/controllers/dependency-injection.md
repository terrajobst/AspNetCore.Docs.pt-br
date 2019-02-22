---
title: Injeção de dependência em controladores no ASP.NET Core
author: ardalis
description: Saiba como os controladores do ASP.NET Core MVC solicitam suas dependências explicitamente por meio de seus construtores com injeção de dependência no ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 9d9d0a68927da62fad8df72c868eaf4b8ada440d
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410265"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="48de1-103">Injeção de dependência em controladores no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48de1-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="48de1-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="48de1-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="48de1-105">Controladores do ASP.NET Core MVC devem solicitar suas dependências explicitamente por meio de seus construtores.</span><span class="sxs-lookup"><span data-stu-id="48de1-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="48de1-106">Em algumas instâncias, ações individuais do controlador podem exigir um serviço e pode não fazer sentido solicitá-lo no nível do controlador.</span><span class="sxs-lookup"><span data-stu-id="48de1-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="48de1-107">Nesse caso, você também pode optar por injetar um serviço como um parâmetro no método de ação.</span><span class="sxs-lookup"><span data-stu-id="48de1-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

<span data-ttu-id="48de1-108">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="48de1-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="48de1-109">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="48de1-109">Dependency Injection</span></span>

<span data-ttu-id="48de1-110">O ASP.NET Core tem suporte interno para a [injeção de dependência](../../fundamentals/dependency-injection.md), o que facilita a manutenção e o teste de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="48de1-110">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="48de1-111">Injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="48de1-111">Constructor Injection</span></span>

<span data-ttu-id="48de1-112">O suporte interno do ASP.NET Core para injeção de dependência baseada no construtor se estende para controladores MVC.</span><span class="sxs-lookup"><span data-stu-id="48de1-112">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="48de1-113">Simplesmente adicionando um tipo de serviço ao seu controlador como um parâmetro de construtor, o ASP.NET Core tentará resolver o tipo usando seu contêiner de serviço interno.</span><span class="sxs-lookup"><span data-stu-id="48de1-113">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="48de1-114">Os serviços são normalmente, mas não sempre, definidos usando interfaces.</span><span class="sxs-lookup"><span data-stu-id="48de1-114">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="48de1-115">Por exemplo, se seu aplicativo tiver lógica de negócios que depende da hora atual, você pode injetar um serviço que recupera a hora (em vez fazer o hard-coding), o que permite que seus testes sejam passados em implementações que usam uma hora definida.</span><span class="sxs-lookup"><span data-stu-id="48de1-115">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


<span data-ttu-id="48de1-116">Implementar uma interface como esta para que ele use o relógio do sistema em tempo de execução é simples:</span><span class="sxs-lookup"><span data-stu-id="48de1-116">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


<span data-ttu-id="48de1-117">Com isso em vigor, podemos usar o serviço em nosso controlador.</span><span class="sxs-lookup"><span data-stu-id="48de1-117">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="48de1-118">Nesse caso, adicionamos alguma lógica para ao método `HomeController` `Index` para exibir uma saudação ao usuário com base na hora do dia.</span><span class="sxs-lookup"><span data-stu-id="48de1-118">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

<span data-ttu-id="48de1-119">Se executarmos o aplicativo agora, provavelmente encontraremos um erro:</span><span class="sxs-lookup"><span data-stu-id="48de1-119">If we run the application now, we will most likely encounter an error:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="48de1-120">Esse erro ocorre quando não configuramos um serviço no método `ConfigureServices` em nossa classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="48de1-120">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="48de1-121">Para especificar que solicitações de `IDateTime` devem ser resolvidas usando uma instância de `SystemDateTime`, adicione a linha realçada à lista abaixo para seu método `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="48de1-121">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> <span data-ttu-id="48de1-122">Esse serviço específico poderia ser implementado usando qualquer uma das várias opções diferentes de tempo de vida (`Transient`, `Scoped` ou `Singleton`).</span><span class="sxs-lookup"><span data-stu-id="48de1-122">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="48de1-123">Consulte [Injeção de dependência](../../fundamentals/dependency-injection.md) para entender como cada uma dessas opções de escopo afetará o comportamento de seu serviço.</span><span class="sxs-lookup"><span data-stu-id="48de1-123">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="48de1-124">Depois que o serviço tiver sido configurado, executar o aplicativo e navegar para a home page deve exibir a mensagem baseada em hora conforme o esperado:</span><span class="sxs-lookup"><span data-stu-id="48de1-124">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![Saudação do servidor](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="48de1-126">Confira [Testar a lógica do controlador](testing.md) para facilitar o teste do código ao solicitar dependências explicitamente em controladores.</span><span class="sxs-lookup"><span data-stu-id="48de1-126">See [Test controller logic](testing.md) to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

<span data-ttu-id="48de1-127">A injeção de dependência interna do ASP.NET Core dá suporte a apenas um construtor para classes que solicitam serviços.</span><span class="sxs-lookup"><span data-stu-id="48de1-127">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="48de1-128">Se tiver mais de um construtor, você poderá receber uma exceção informando:</span><span class="sxs-lookup"><span data-stu-id="48de1-128">If you have more than one constructor, you may get an exception stating:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="48de1-129">Como a mensagem de erro afirma, você pode corrigir esse problema usando um único construtor.</span><span class="sxs-lookup"><span data-stu-id="48de1-129">As the error message states, you can correct this problem with the use of a single constructor.</span></span> <span data-ttu-id="48de1-130">Você também pode [substituir o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement), muitas das quais dão suporte a vários construtores.</span><span class="sxs-lookup"><span data-stu-id="48de1-130">You can also [replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="48de1-131">Injeção de ação com FromServices</span><span class="sxs-lookup"><span data-stu-id="48de1-131">Action Injection with FromServices</span></span>

<span data-ttu-id="48de1-132">Às vezes, você não precisa de um serviço para mais de uma ação em seu controlador.</span><span class="sxs-lookup"><span data-stu-id="48de1-132">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="48de1-133">Nesse caso, talvez faça sentido injetar o serviço como um parâmetro do método de ação.</span><span class="sxs-lookup"><span data-stu-id="48de1-133">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="48de1-134">Isso é feito marcando o parâmetro com o atributo `[FromServices]`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="48de1-134">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="48de1-135">Acessando configurações de um controlador</span><span class="sxs-lookup"><span data-stu-id="48de1-135">Accessing Settings from a Controller</span></span>

<span data-ttu-id="48de1-136">Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum.</span><span class="sxs-lookup"><span data-stu-id="48de1-136">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="48de1-137">Esse acesso deve usar o padrão Opções descrito na [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="48de1-137">This access should use the Options pattern described in [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="48de1-138">Geralmente, você não deve solicitar configurações diretamente de seu controlador usando a injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="48de1-138">You generally shouldn't request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="48de1-139">Uma abordagem melhor é solicitar uma instância de `IOptions<T>`, em que `T` é a classe de configuração de que você precisa.</span><span class="sxs-lookup"><span data-stu-id="48de1-139">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="48de1-140">Para trabalhar com o padrão de opções, você precisa criar uma classe que representa as opções, como esta:</span><span class="sxs-lookup"><span data-stu-id="48de1-140">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

<span data-ttu-id="48de1-141">Em seguida, você precisa configurar o aplicativo para usar o modelo de opções e adicionar sua classe de configuração à coleção de serviços em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="48de1-141">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> <span data-ttu-id="48de1-142">Na lista acima, estamos configurando o aplicativo para ler as configurações de um arquivo no formato JSON.</span><span class="sxs-lookup"><span data-stu-id="48de1-142">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="48de1-143">Você também pode definir as configurações inteiramente no código, como é mostrado no código comentado acima.</span><span class="sxs-lookup"><span data-stu-id="48de1-143">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="48de1-144">Consulte [Configuração](xref:fundamentals/configuration/index) para ver outras opções de configuração.</span><span class="sxs-lookup"><span data-stu-id="48de1-144">See [Configuration](xref:fundamentals/configuration/index) for further configuration options.</span></span>

<span data-ttu-id="48de1-145">Após ter especificado um objeto de configuração fortemente tipado (nesse caso, `SampleWebSettings`) e tê-lo adicionado à coleção de serviços, você pode solicitá-lo de qualquer método de Ação ou Controlador solicitando uma instância de `IOptions<T>` (nesse caso, `IOptions<SampleWebSettings>`).</span><span class="sxs-lookup"><span data-stu-id="48de1-145">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="48de1-146">O código a seguir mostra como alguém solicitaria as configurações de um controlador:</span><span class="sxs-lookup"><span data-stu-id="48de1-146">The following code shows how one would request the settings from a controller:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

<span data-ttu-id="48de1-147">Seguir o padrão Opções permite que as definições e configurações sejam dissociadas umas das outras e garante que o controlador esteja seguindo a [separação de preocupações](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), uma vez que ele não precisa saber como nem onde encontrar as informações de configuração.</span><span class="sxs-lookup"><span data-stu-id="48de1-147">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="48de1-148">Isso também facilita a [realização do teste de unidade](testing.md) no controlador porque não há a criação de instância direta das classes de configuração dentro da classe do controlador.</span><span class="sxs-lookup"><span data-stu-id="48de1-148">It also makes the controller easier to [unit test](testing.md), since there's no direct instantiation of settings classes within the controller class.</span></span>
