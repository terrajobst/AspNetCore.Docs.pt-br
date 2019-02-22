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
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Injeção de dependência em controladores no ASP.NET Core

<a name="dependency-injection-controllers"></a>

Por [Steve Smith](https://ardalis.com/)

Controladores do ASP.NET Core MVC devem solicitar suas dependências explicitamente por meio de seus construtores. Em algumas instâncias, ações individuais do controlador podem exigir um serviço e pode não fazer sentido solicitá-lo no nível do controlador. Nesse caso, você também pode optar por injetar um serviço como um parâmetro no método de ação.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="dependency-injection"></a>Injeção de dependência

O ASP.NET Core tem suporte interno para a [injeção de dependência](../../fundamentals/dependency-injection.md), o que facilita a manutenção e o teste de aplicativos.

## <a name="constructor-injection"></a>Injeção de construtor

O suporte interno do ASP.NET Core para injeção de dependência baseada no construtor se estende para controladores MVC. Simplesmente adicionando um tipo de serviço ao seu controlador como um parâmetro de construtor, o ASP.NET Core tentará resolver o tipo usando seu contêiner de serviço interno. Os serviços são normalmente, mas não sempre, definidos usando interfaces. Por exemplo, se seu aplicativo tiver lógica de negócios que depende da hora atual, você pode injetar um serviço que recupera a hora (em vez fazer o hard-coding), o que permite que seus testes sejam passados em implementações que usam uma hora definida.

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


Implementar uma interface como esta para que ele use o relógio do sistema em tempo de execução é simples:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


Com isso em vigor, podemos usar o serviço em nosso controlador. Nesse caso, adicionamos alguma lógica para ao método `HomeController` `Index` para exibir uma saudação ao usuário com base na hora do dia.

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

Se executarmos o aplicativo agora, provavelmente encontraremos um erro:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

Esse erro ocorre quando não configuramos um serviço no método `ConfigureServices` em nossa classe `Startup`. Para especificar que solicitações de `IDateTime` devem ser resolvidas usando uma instância de `SystemDateTime`, adicione a linha realçada à lista abaixo para seu método `ConfigureServices`:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> Esse serviço específico poderia ser implementado usando qualquer uma das várias opções diferentes de tempo de vida (`Transient`, `Scoped` ou `Singleton`). Consulte [Injeção de dependência](../../fundamentals/dependency-injection.md) para entender como cada uma dessas opções de escopo afetará o comportamento de seu serviço.

Depois que o serviço tiver sido configurado, executar o aplicativo e navegar para a home page deve exibir a mensagem baseada em hora conforme o esperado:

![Saudação do servidor](dependency-injection/_static/server-greeting.png)

>[!TIP]
> Confira [Testar a lógica do controlador](testing.md) para facilitar o teste do código ao solicitar dependências explicitamente em controladores.

A injeção de dependência interna do ASP.NET Core dá suporte a apenas um construtor para classes que solicitam serviços. Se tiver mais de um construtor, você poderá receber uma exceção informando:

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

Como a mensagem de erro afirma, você pode corrigir esse problema usando um único construtor. Você também pode [substituir o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement), muitas das quais dão suporte a vários construtores.

## <a name="action-injection-with-fromservices"></a>Injeção de ação com FromServices

Às vezes, você não precisa de um serviço para mais de uma ação em seu controlador. Nesse caso, talvez faça sentido injetar o serviço como um parâmetro do método de ação. Isso é feito marcando o parâmetro com o atributo `[FromServices]`, conforme mostrado aqui:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a>Acessando configurações de um controlador

Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum. Esse acesso deve usar o padrão Opções descrito na [configuração](xref:fundamentals/configuration/index). Geralmente, você não deve solicitar configurações diretamente de seu controlador usando a injeção de dependência. Uma abordagem melhor é solicitar uma instância de `IOptions<T>`, em que `T` é a classe de configuração de que você precisa.

Para trabalhar com o padrão de opções, você precisa criar uma classe que representa as opções, como esta:

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

Em seguida, você precisa configurar o aplicativo para usar o modelo de opções e adicionar sua classe de configuração à coleção de serviços em `ConfigureServices`:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> Na lista acima, estamos configurando o aplicativo para ler as configurações de um arquivo no formato JSON. Você também pode definir as configurações inteiramente no código, como é mostrado no código comentado acima. Consulte [Configuração](xref:fundamentals/configuration/index) para ver outras opções de configuração.

Após ter especificado um objeto de configuração fortemente tipado (nesse caso, `SampleWebSettings`) e tê-lo adicionado à coleção de serviços, você pode solicitá-lo de qualquer método de Ação ou Controlador solicitando uma instância de `IOptions<T>` (nesse caso, `IOptions<SampleWebSettings>`). O código a seguir mostra como alguém solicitaria as configurações de um controlador:

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

Seguir o padrão Opções permite que as definições e configurações sejam dissociadas umas das outras e garante que o controlador esteja seguindo a [separação de preocupações](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), uma vez que ele não precisa saber como nem onde encontrar as informações de configuração. Isso também facilita a [realização do teste de unidade](testing.md) no controlador porque não há a criação de instância direta das classes de configuração dentro da classe do controlador.
