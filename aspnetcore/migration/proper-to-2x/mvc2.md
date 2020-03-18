---
title: Migrar do ASP.NET para o ASP.NET Core 2.0
author: isaac2004
description: Receba diretrizes para migrar aplicativos existentes do ASP.NET MVC ou da API Web para ASP.NET Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 5a2437f8dddee49d402bf7df67cb15aa895367b2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511074"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="e3e8b-103">Migrar do ASP.NET para o ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="e3e8b-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="e3e8b-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="e3e8b-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="e3e8b-105">Este artigo serve como um guia de referência para migração de aplicativos ASP.NET para o ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e3e8b-106">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="e3e8b-106">Prerequisites</span></span>

<span data-ttu-id="e3e8b-107">Instale **um** dos seguintes itens de [downloads do .net: Windows](https://dotnet.microsoft.com/download):</span><span class="sxs-lookup"><span data-stu-id="e3e8b-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="e3e8b-108">SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="e3e8b-108">.NET Core SDK</span></span>
* <span data-ttu-id="e3e8b-109">Visual Studio para Windows</span><span class="sxs-lookup"><span data-stu-id="e3e8b-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="e3e8b-110">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="e3e8b-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="e3e8b-111">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="e3e8b-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="e3e8b-112">Frameworks de destino</span><span class="sxs-lookup"><span data-stu-id="e3e8b-112">Target frameworks</span></span>

<span data-ttu-id="e3e8b-113">Projetos do ASP.NET Core 2.0 oferecem aos desenvolvedores a flexibilidade de direcionamento para o .NET Core, .NET Framework ou ambos.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="e3e8b-114">Consulte [Escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qual estrutura de destino é mais apropriada.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="e3e8b-115">Ao usar o .NET Framework como destino, projetos precisam fazer referência a pacotes NuGet individuais.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="e3e8b-116">Usar o .NET Core como destino permite que você elimine várias referências de pacote explícitas, graças ao [metapacote](xref:fundamentals/metapackage) do ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="e3e8b-117">Instale o metapacote `Microsoft.AspNetCore.All` em seu projeto:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="e3e8b-118">Quando o metapacote é usado, nenhum pacote referenciado no metapacote é implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="e3e8b-119">O repositório de runtime do .NET Core inclui esses ativos e eles são pré-compilados para melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="e3e8b-120">Consulte <xref:fundamentals/metapackage> para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="e3e8b-121">Diferenças de estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="e3e8b-121">Project structure differences</span></span>

<span data-ttu-id="e3e8b-122">O formato de arquivo *.csproj* foi simplificado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="e3e8b-123">Algumas alterações importantes incluem:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-123">Some notable changes include:</span></span>

* <span data-ttu-id="e3e8b-124">A inclusão explícita de arquivos não é necessária para que eles possam ser considerados como parte do projeto.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="e3e8b-125">Isso reduz o risco de conflitos de mesclagem XML ao trabalhar em grandes equipes.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="e3e8b-126">Não há referências baseadas em GUID a outros projetos, o que melhora a legibilidade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="e3e8b-127">O arquivo pode ser editado sem descarregá-lo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Opção de menu de contexto Editar CSPROJ no Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="e3e8b-129">Substituição do arquivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="e3e8b-129">Global.asax file replacement</span></span>

<span data-ttu-id="e3e8b-130">O ASP.NET Core introduziu um novo mecanismo para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="e3e8b-131">O ponto de entrada para aplicativos ASP.NET é o arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="e3e8b-132">Tarefas como configuração de roteamento e registros de filtro e de área são tratadas no arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="e3e8b-133">Essa abordagem associa o aplicativo e o servidor no qual ele é implantado de uma forma que interfere na implementação.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="e3e8b-134">Em um esforço para desassociar, a [OWIN](https://owin.org/) foi introduzida para fornecer uma maneira mais limpa de usar várias estruturas juntas.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="e3e8b-135">A OWIN fornece um pipeline para adicionar somente os módulos necessários.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="e3e8b-136">O ambiente de hospedagem leva uma função [Startup](xref:fundamentals/startup) para configurar serviços e pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="e3e8b-137">`Startup` registra um conjunto de middleware com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="e3e8b-138">Para cada solicitação, o aplicativo chama cada um dos componentes de middleware com o ponteiro de cabeçalho de uma lista vinculada para um conjunto existente de manipuladores.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="e3e8b-139">Cada componente de middleware pode adicionar um ou mais manipuladores para a pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="e3e8b-140">Isso é feito retornando uma referência para o manipulador que é o novo cabeçalho da lista.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="e3e8b-141">Cada manipulador é responsável por se lembrar do próximo manipulador na lista e por invocá-lo.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="e3e8b-142">Com o ASP.NET Core, o ponto de entrada para um aplicativo é `Startup` e você não tem mais uma dependência de *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="e3e8b-143">Ao usar a OWIN com o .NET Framework, use algo parecido com o seguinte como um pipeline:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="e3e8b-144">Isso configura as rotas padrão e usa XmlSerialization em Json por padrão.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="e3e8b-145">Adicione outro Middleware para este pipeline conforme necessário (carregamento de serviços, definições de configuração, arquivos estáticos, etc.).</span><span class="sxs-lookup"><span data-stu-id="e3e8b-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="e3e8b-146">O ASP.NET Core usa uma abordagem semelhante, mas não depende de OWIN para manipular a entrada.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="e3e8b-147">Em vez disso, isso é feito por meio do método de `Main` *Program.cs* (semelhante a aplicativos de console) e `Startup` é carregado por aí.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="e3e8b-148">`Startup` deve incluir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="e3e8b-149">Em `Configure`, adicione o middleware necessário ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="e3e8b-150">No exemplo a seguir (com base no modelo de site da Web padrão), vários métodos de extensão são usados para configurar o pipeline com suporte para:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="e3e8b-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="e3e8b-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="e3e8b-152">Páginas de erro</span><span class="sxs-lookup"><span data-stu-id="e3e8b-152">Error pages</span></span>
* <span data-ttu-id="e3e8b-153">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="e3e8b-153">Static files</span></span>
* <span data-ttu-id="e3e8b-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e3e8b-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="e3e8b-155">Identity</span><span class="sxs-lookup"><span data-stu-id="e3e8b-155">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="e3e8b-156">O host e o aplicativo foram separados, o que fornece a flexibilidade de mover para uma plataforma diferente no futuro.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-156">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="e3e8b-157">Para obter uma referência mais detalhada para ASP.NET Core inicialização e middleware, consulte <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-157">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="e3e8b-158">Armazenando configurações</span><span class="sxs-lookup"><span data-stu-id="e3e8b-158">Storing configurations</span></span>

<span data-ttu-id="e3e8b-159">O ASP.NET dá suporte ao armazenamento de configurações.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-159">ASP.NET supports storing settings.</span></span> <span data-ttu-id="e3e8b-160">Essas configurações são usadas, por exemplo, para dar suporte ao ambiente no qual os aplicativos foram implantados.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-160">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="e3e8b-161">Uma prática comum era armazenar todos os pares chave-valor personalizados na seção `<appSettings>` do arquivo *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-161">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="e3e8b-162">Aplicativos leem essas configurações usando a coleção `ConfigurationManager.AppSettings` no namespace `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-162">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="e3e8b-163">O ASP.NET Core pode armazenar dados de configuração para o aplicativo em qualquer arquivo e carregá-los como parte da inicialização de middleware.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-163">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="e3e8b-164">O arquivo padrão usado em modelos de projeto é *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-164">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="e3e8b-165">Carregar esse arquivo em uma instância de `IConfiguration` dentro de seu aplicativo é feito em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-165">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="e3e8b-166">O aplicativo lê de `Configuration` para obter as configurações:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-166">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="e3e8b-167">Existem extensões para essa abordagem para tornar o processo mais robusto, tais como o uso de DI ([injeção de dependência](xref:fundamentals/dependency-injection)) para carregar um serviço com esses valores.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-167">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="e3e8b-168">A abordagem de DI fornece um conjunto fortemente tipado de objetos de configuração.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-168">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="e3e8b-169">**Observação:** Para obter uma referência mais detalhada para ASP.NET Core configuração, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-169">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="e3e8b-170">Injeção de dependência nativa</span><span class="sxs-lookup"><span data-stu-id="e3e8b-170">Native dependency injection</span></span>

<span data-ttu-id="e3e8b-171">Uma meta importante ao criar aplicativos escalonáveis e grandes é o acoplamento flexível de componentes e serviços.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-171">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="e3e8b-172">A [injeção de dependência](xref:fundamentals/dependency-injection) é uma técnica popular para conseguir isso, e é um componente nativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-172">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="e3e8b-173">Em aplicativos ASP.NET, os desenvolvedores contam com uma biblioteca de terceiros para implementar a injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-173">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="e3e8b-174">Um biblioteca desse tipo é a [Unity](https://github.com/unitycontainer/unity), fornecida pelas Diretrizes da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-174">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="e3e8b-175">Um exemplo de configuração da injeção de dependência com o Unity é implementar `IDependencyResolver` que encapsula um `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-175">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="e3e8b-176">Crie uma instância de sua `UnityContainer`, registre seu serviço e defina o resolvedor de dependência de `HttpConfiguration` para a nova instância de `UnityResolver` para o contêiner:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-176">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="e3e8b-177">Injete `IProductRepository` quando necessário:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-177">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="e3e8b-178">Como a injeção de dependência faz parte do ASP.NET Core, você pode adicionar seu serviço no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-178">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="e3e8b-179">O repositório pode ser injetado em qualquer lugar, como ocorria com a Unity.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-179">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="e3e8b-180">Para obter mais informações sobre injeção de dependência em ASP.NET Core, consulte <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-180">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="e3e8b-181">Servindo arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="e3e8b-181">Serving static files</span></span>

<span data-ttu-id="e3e8b-182">Uma parte importante do desenvolvimento da Web é a capacidade de servir ativos estáticos, do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-182">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="e3e8b-183">Os exemplos mais comuns de arquivos estáticos são HTML, CSS, Javascript e imagens.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-183">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="e3e8b-184">Esses arquivos precisam ser salvos no local de publicação do aplicativo (ou CDN) e referenciados para que eles possam ser carregados por uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-184">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="e3e8b-185">Esse processo foi alterado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-185">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="e3e8b-186">No ASP.NET, arquivos estáticos são armazenados em vários diretórios e referenciados nas exibições.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-186">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="e3e8b-187">No ASP.NET Core, arquivos estáticos são armazenados na "raiz da Web" ( *&lt;raiz do conteúdo&gt;/wwwroot*), a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-187">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="e3e8b-188">Os arquivos são carregados no pipeline de solicitação invocando o método de extensão `UseStaticFiles` de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e3e8b-188">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="e3e8b-189">**Observação**: se você usar o .NET Framework como destino, instale o pacote NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-189">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="e3e8b-190">Por exemplo, um ativo de imagem na pasta *wwwroot/imagens* está acessível para o navegador em um local como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-190">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="e3e8b-191">**Observação:** Para obter uma referência mais detalhada para o fornecimento de arquivos estáticos no ASP.NET Core, consulte <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="e3e8b-191">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e3e8b-192">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e3e8b-192">Additional resources</span></span>

* [<span data-ttu-id="e3e8b-193">Fazendo a portabilidade de Bibliotecas para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="e3e8b-193">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
