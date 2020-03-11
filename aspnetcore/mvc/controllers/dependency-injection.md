---
title: Injeção de dependência em controladores no ASP.NET Core
author: ardalis
description: Saiba como os controladores do ASP.NET Core MVC solicitam suas dependências explicitamente por meio de seus construtores com injeção de dependência no ASP.NET Core.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 202b62d4b30c5c61c407abdc8509a2a75e181cb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660079"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="e6315-103">Injeção de dependência em controladores no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6315-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="e6315-104">Por [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="e6315-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="e6315-105">Controladores do ASP.NET Core MVC solicitam dependências explicitamente por meio de construtores.</span><span class="sxs-lookup"><span data-stu-id="e6315-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="e6315-106">O ASP.NET Core tem suporte interno para [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6315-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e6315-107">A injeção de dependência torna mais fácil testar e manter aplicativos.</span><span class="sxs-lookup"><span data-stu-id="e6315-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="e6315-108">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e6315-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="e6315-109">Injeção de construtor</span><span class="sxs-lookup"><span data-stu-id="e6315-109">Constructor Injection</span></span>

<span data-ttu-id="e6315-110">Os serviços são adicionados como um parâmetro de construtor e o runtime resolve o serviço de contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="e6315-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="e6315-111">Os serviços são normalmente definidos usando interfaces.</span><span class="sxs-lookup"><span data-stu-id="e6315-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="e6315-112">Por exemplo, considere um aplicativo que exige a hora atual.</span><span class="sxs-lookup"><span data-stu-id="e6315-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="e6315-113">A interface a seguir expõe o serviço `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="e6315-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="e6315-114">O código a seguir implementa a interface `IDateTime`:</span><span class="sxs-lookup"><span data-stu-id="e6315-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="e6315-115">Adicione o serviço ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="e6315-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="e6315-116">Para obter mais informações sobre <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, veja [tempos de vida do serviço DI](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e6315-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="e6315-117">O código a seguir exibe uma saudação ao usuário com base na hora do dia:</span><span class="sxs-lookup"><span data-stu-id="e6315-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="e6315-118">Execute o aplicativo e será exibida uma mensagem com base no horário.</span><span class="sxs-lookup"><span data-stu-id="e6315-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="e6315-119">Injeção de ação com FromServices</span><span class="sxs-lookup"><span data-stu-id="e6315-119">Action injection with FromServices</span></span>

<span data-ttu-id="e6315-120">O <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> habilita a injeção de um serviço diretamente em um método de ação sem usar a injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="e6315-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="e6315-121">Acessar configurações de um controlador</span><span class="sxs-lookup"><span data-stu-id="e6315-121">Access settings from a controller</span></span>

<span data-ttu-id="e6315-122">Acessar definições de configuração ou do aplicativo de dentro de um controlador é um padrão comum.</span><span class="sxs-lookup"><span data-stu-id="e6315-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="e6315-123">O *padrão de opções* descrito em <xref:fundamentals/configuration/options> é a abordagem preferencial para gerenciar as configurações.</span><span class="sxs-lookup"><span data-stu-id="e6315-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="e6315-124">Em geral, não convém injetar <xref:Microsoft.Extensions.Configuration.IConfiguration> diretamente em um controlador.</span><span class="sxs-lookup"><span data-stu-id="e6315-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="e6315-125">Crie uma classe que representa as opções.</span><span class="sxs-lookup"><span data-stu-id="e6315-125">Create a class that represents the options.</span></span> <span data-ttu-id="e6315-126">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6315-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="e6315-127">Adicione a classe de configuração à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="e6315-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="e6315-128">Configure o aplicativo para ler as configurações de um arquivo formatado em JSON:</span><span class="sxs-lookup"><span data-stu-id="e6315-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="e6315-129">O código a seguir solicita as configurações `IOptions<SampleWebSettings>` do contêiner de serviço e usa-as no método `Index`:</span><span class="sxs-lookup"><span data-stu-id="e6315-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="e6315-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6315-130">Additional resources</span></span>

* <span data-ttu-id="e6315-131">Confira <xref:mvc/controllers/testing> para facilitar o teste do código ao solicitar dependências explicitamente em controladores.</span><span class="sxs-lookup"><span data-stu-id="e6315-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="e6315-132">[Substitui o contêiner de injeção de dependência padrão por uma implementação de terceiros](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="e6315-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
