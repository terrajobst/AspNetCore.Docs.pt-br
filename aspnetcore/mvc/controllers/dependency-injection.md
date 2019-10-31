---
title: Injeção de dependência em controladores no ASP.NET Core
author: ardalis
description: Saiba como os controladores do ASP.NET Core MVC solicitam suas dependências explicitamente por meio de seus construtores com injeção de dependência no ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 6b08c321f4cae1f4efd8ea40300eaf4dfc2f63a1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890931"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>Injeção de dependência em controladores no ASP.NET Core

<a name="dependency-injection-controllers"></a>

Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)

Controladores do ASP.NET Core MVC solicitam dependências explicitamente por meio de construtores. O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](xref:fundamentals/dependency-injection). A injeção de dependência torna mais fácil testar e manter aplicativos.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Injeção de construtor

Os serviços são adicionados como um parâmetro de construtor e o tempo de execução resolve o serviço de contêiner de serviço. Os serviços são normalmente definidos usando interfaces. Por exemplo, considere um aplicativo que exige a hora atual. A interface a seguir expõe o serviço `IDateTime`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

O código a seguir implementa a interface `IDateTime`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Adicione o serviço ao contêiner de serviço:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Para obter mais informações sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, veja [tempos de vida do serviço DI](xref:fundamentals/dependency-injection#service-lifetimes).

O código a seguir exibe uma saudação ao usuário com base na hora do dia:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Execute o aplicativo e será exibida uma mensagem com base no horário.

## <a name="action-injection-with-fromservices"></a>Injeção de ação com FromServices

O <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> habilita a injeção de um serviço diretamente em um método de ação sem usar a injeção de construtor:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Acessar configurações de um controlador

Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum. O *padrão de opções* descrito em <xref:fundamentals/configuration/options> é a abordagem preferencial para gerenciar as configurações. Em geral, não convém injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> diretamente em um controlador.

Crie uma classe que representa as opções. Por exemplo:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Adicione a classe de configuração à coleção de serviços:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Configure o aplicativo para ler as configurações de um arquivo formatado em JSON:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

O código a seguir solicita as configurações `IOptions<SampleWebSettings>` do contêiner de serviço e usa-as no método `Index`:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* Confira <xref:mvc/controllers/testing> para facilitar o teste do código ao solicitar dependências explicitamente em controladores.

* [Substitui o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement).
