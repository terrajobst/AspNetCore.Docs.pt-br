---
title: Migrar do ASP.NET para o ASP.NET Core
author: isaac2004
description: Receba orientações para migrar os aplicativos existentes do ASP.NET MVC ou da API Web para o ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
uid: migration/proper-to-2x/index
ms.openlocfilehash: 1564b644b774939c3c242a41812851917e96d2b2
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "74803338"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="97a5a-103">Migrar do ASP.NET para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97a5a-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="97a5a-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="97a5a-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="97a5a-105">Este artigo serve como um guia de referência para migração de aplicativos ASP.NET para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="97a5a-106">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="97a5a-106">Prerequisites</span></span>

[<span data-ttu-id="97a5a-107">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="97a5a-107">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download)

## <a name="target-frameworks"></a><span data-ttu-id="97a5a-108">Frameworks de destino</span><span class="sxs-lookup"><span data-stu-id="97a5a-108">Target frameworks</span></span>

<span data-ttu-id="97a5a-109">Projetos do ASP.NET Core oferecem aos desenvolvedores a flexibilidade de direcionamento para o .NET Core, .NET Framework ou ambos.</span><span class="sxs-lookup"><span data-stu-id="97a5a-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="97a5a-110">Consulte [Escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qual estrutura de destino é mais apropriada.</span><span class="sxs-lookup"><span data-stu-id="97a5a-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="97a5a-111">Ao usar o .NET Framework como destino, projetos precisam fazer referência a pacotes NuGet individuais.</span><span class="sxs-lookup"><span data-stu-id="97a5a-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="97a5a-112">Usar o .NET Core como destino permite que você elimine várias referências de pacote explícitas, graças ao [metapacote](xref:fundamentals/metapackage-app) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="97a5a-113">Instale o metapacote `Microsoft.AspNetCore.App` em seu projeto:</span><span class="sxs-lookup"><span data-stu-id="97a5a-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="97a5a-114">Quando o metapacote é usado, nenhum pacote referenciado no metapacote é implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="97a5a-115">O repositório de runtime do .NET Core inclui esses ativos e eles são pré-compilados para melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="97a5a-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="97a5a-116">Para saber mais, confira [Metapacote Microsoft.AspNetCore.App para ASP.NET Core](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="97a5a-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="97a5a-117">Diferenças de estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="97a5a-117">Project structure differences</span></span>

<span data-ttu-id="97a5a-118">O formato de arquivo *.csproj* foi simplificado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="97a5a-119">Algumas alterações importantes incluem:</span><span class="sxs-lookup"><span data-stu-id="97a5a-119">Some notable changes include:</span></span>

- <span data-ttu-id="97a5a-120">A inclusão explícita de arquivos não é necessária para que eles possam ser considerados como parte do projeto.</span><span class="sxs-lookup"><span data-stu-id="97a5a-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="97a5a-121">Isso reduz o risco de conflitos de mesclagem XML ao trabalhar em grandes equipes.</span><span class="sxs-lookup"><span data-stu-id="97a5a-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="97a5a-122">Não há referências baseadas em GUID a outros projetos, o que melhora a legibilidade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="97a5a-123">O arquivo pode ser editado sem descarregá-lo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="97a5a-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Opção de menu de contexto Editar CSPROJ no Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="97a5a-125">Substituição do arquivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="97a5a-125">Global.asax file replacement</span></span>

<span data-ttu-id="97a5a-126">O ASP.NET Core introduziu um novo mecanismo para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="97a5a-127">O ponto de entrada para aplicativos ASP.NET é o arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97a5a-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="97a5a-128">Tarefas como configuração de roteamento e registros de filtro e de área são tratadas no arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97a5a-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="97a5a-129">Essa abordagem associa o aplicativo e o servidor no qual ele é implantado de uma forma que interfere na implementação.</span><span class="sxs-lookup"><span data-stu-id="97a5a-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="97a5a-130">Em um esforço para desassociar, a [OWIN](https://owin.org/) foi introduzida para fornecer uma maneira mais limpa de usar várias estruturas juntas.</span><span class="sxs-lookup"><span data-stu-id="97a5a-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="97a5a-131">A OWIN fornece um pipeline para adicionar somente os módulos necessários.</span><span class="sxs-lookup"><span data-stu-id="97a5a-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="97a5a-132">O ambiente de hospedagem leva uma função [Startup](xref:fundamentals/startup) para configurar serviços e pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="97a5a-133">`Startup` registra um conjunto de middleware com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="97a5a-134">Para cada solicitação, o aplicativo chama cada um dos componentes de middleware com o ponteiro de cabeçalho de uma lista vinculada para um conjunto existente de manipuladores.</span><span class="sxs-lookup"><span data-stu-id="97a5a-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="97a5a-135">Cada componente de middleware pode adicionar um ou mais manipuladores para a pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="97a5a-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="97a5a-136">Isso é feito retornando uma referência para o manipulador que é o novo cabeçalho da lista.</span><span class="sxs-lookup"><span data-stu-id="97a5a-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="97a5a-137">Cada manipulador é responsável por se lembrar do próximo manipulador na lista e por invocá-lo.</span><span class="sxs-lookup"><span data-stu-id="97a5a-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="97a5a-138">Com o ASP.NET Core, o ponto de entrada para um aplicativo é `Startup` e você não tem mais uma dependência de *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="97a5a-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="97a5a-139">Ao usar a OWIN com o .NET Framework, use algo parecido com o seguinte como um pipeline:</span><span class="sxs-lookup"><span data-stu-id="97a5a-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="97a5a-140">Isso configura as rotas padrão e usa XmlSerialization em Json por padrão.</span><span class="sxs-lookup"><span data-stu-id="97a5a-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="97a5a-141">Adicione outro Middleware para este pipeline conforme necessário (carregamento de serviços, definições de configuração, arquivos estáticos, etc.).</span><span class="sxs-lookup"><span data-stu-id="97a5a-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="97a5a-142">O ASP.NET Core usa uma abordagem semelhante, mas não depende de OWIN para manipular a entrada.</span><span class="sxs-lookup"><span data-stu-id="97a5a-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="97a5a-143">Em vez disso, isso é feito por meio do método `Main` de *Program.cs* (semelhante a aplicativos de console) e `Startup` é carregado por lá.</span><span class="sxs-lookup"><span data-stu-id="97a5a-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="97a5a-144">`Startup` deve incluir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="97a5a-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="97a5a-145">Em `Configure`, adicione o middleware necessário ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="97a5a-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="97a5a-146">No exemplo a seguir (com base no modelo de site da Web padrão), os métodos de extensão configuram o pipeline com suporte para:</span><span class="sxs-lookup"><span data-stu-id="97a5a-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="97a5a-147">Páginas de erro</span><span class="sxs-lookup"><span data-stu-id="97a5a-147">Error pages</span></span>
- <span data-ttu-id="97a5a-148">Segurança de Transporte Estrita de HTTP</span><span class="sxs-lookup"><span data-stu-id="97a5a-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="97a5a-149">Redirecionamento de HTTP para HTTPS</span><span class="sxs-lookup"><span data-stu-id="97a5a-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="97a5a-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="97a5a-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="97a5a-151">O host e o aplicativo foram separados, o que fornece a flexibilidade de mover para uma plataforma diferente no futuro.</span><span class="sxs-lookup"><span data-stu-id="97a5a-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="97a5a-152">Para obter uma referência mais aprofundada sobre o Startup do ASP.NET Core e middleware, veja [Startup no ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="97a5a-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="97a5a-153">Armazenar configurações</span><span class="sxs-lookup"><span data-stu-id="97a5a-153">Store configurations</span></span>

<span data-ttu-id="97a5a-154">O ASP.NET dá suporte ao armazenamento de configurações.</span><span class="sxs-lookup"><span data-stu-id="97a5a-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="97a5a-155">Essas configurações são usadas, por exemplo, para dar suporte ao ambiente no qual os aplicativos foram implantados.</span><span class="sxs-lookup"><span data-stu-id="97a5a-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="97a5a-156">Uma prática comum era armazenar todos os pares chave-valor personalizados na seção `<appSettings>` do arquivo *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="97a5a-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="97a5a-157">Aplicativos leem essas configurações usando a coleção `ConfigurationManager.AppSettings` no namespace `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="97a5a-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="97a5a-158">O ASP.NET Core pode armazenar dados de configuração para o aplicativo em qualquer arquivo e carregá-los como parte da inicialização de middleware.</span><span class="sxs-lookup"><span data-stu-id="97a5a-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="97a5a-159">O arquivo padrão usado em modelos de projeto é *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="97a5a-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="97a5a-160">Carregar esse arquivo em uma instância de `IConfiguration` dentro de seu aplicativo é feito em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="97a5a-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="97a5a-161">O aplicativo lê de `Configuration` para obter as configurações:</span><span class="sxs-lookup"><span data-stu-id="97a5a-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="97a5a-162">Existem extensões para essa abordagem para tornar o processo mais robusto, tais como o uso de DI ([injeção de dependência](xref:fundamentals/dependency-injection)) para carregar um serviço com esses valores.</span><span class="sxs-lookup"><span data-stu-id="97a5a-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="97a5a-163">A abordagem de DI fornece um conjunto fortemente tipado de objetos de configuração.</span><span class="sxs-lookup"><span data-stu-id="97a5a-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

> [!NOTE]
> <span data-ttu-id="97a5a-164">Para uma referência mais detalhada sobre configuração do ASP.NET Core, veja [Configuração no ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="97a5a-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="97a5a-165">Injeção de dependência nativa</span><span class="sxs-lookup"><span data-stu-id="97a5a-165">Native dependency injection</span></span>

<span data-ttu-id="97a5a-166">Uma meta importante ao criar aplicativos escalonáveis e grandes é o acoplamento flexível de componentes e serviços.</span><span class="sxs-lookup"><span data-stu-id="97a5a-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="97a5a-167">A [injeção de dependência](xref:fundamentals/dependency-injection) é uma técnica popular para conseguir isso e é também um componente nativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="97a5a-168">Em aplicativos ASP.NET, os desenvolvedores contam com uma biblioteca de terceiros para implementar injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="97a5a-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="97a5a-169">Um biblioteca desse tipo é a [Unity](https://github.com/unitycontainer/unity), fornecida pelas Diretrizes da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="97a5a-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="97a5a-170">Um exemplo de configuração da injeção de dependência com Unity é a implementação de `IDependencyResolver`, que encapsula uma `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="97a5a-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="97a5a-171">Crie uma instância de sua `UnityContainer`, registre seu serviço e defina o resolvedor de dependência de `HttpConfiguration` para a nova instância de `UnityResolver` para o contêiner:</span><span class="sxs-lookup"><span data-stu-id="97a5a-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="97a5a-172">Injete `IProductRepository` quando necessário:</span><span class="sxs-lookup"><span data-stu-id="97a5a-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="97a5a-173">Já que a injeção de dependência é parte do ASP.NET Core, você pode adicionar o serviço no método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="97a5a-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="97a5a-174">O repositório pode ser injetado em qualquer lugar, como ocorria com a Unity.</span><span class="sxs-lookup"><span data-stu-id="97a5a-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="97a5a-175">Para obter mais informações sobre injeção de dependência, confira [Injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="97a5a-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="97a5a-176">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="97a5a-176">Serve static files</span></span>

<span data-ttu-id="97a5a-177">Uma parte importante do desenvolvimento da Web é a capacidade de servir ativos estáticos, do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="97a5a-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="97a5a-178">Os exemplos mais comuns de arquivos estáticos são HTML, CSS, Javascript e imagens.</span><span class="sxs-lookup"><span data-stu-id="97a5a-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="97a5a-179">Esses arquivos precisam ser salvos no local de publicação do aplicativo (ou CDN) e referenciados para que eles possam ser carregados por uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="97a5a-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="97a5a-180">Esse processo foi alterado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="97a5a-181">No ASP.NET, arquivos estáticos são armazenados em vários diretórios e referenciados nas exibições.</span><span class="sxs-lookup"><span data-stu-id="97a5a-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="97a5a-182">No ASP.NET Core, arquivos estáticos são armazenados na "raiz da Web" ( *&lt;raiz do conteúdo&gt;/wwwroot*), a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="97a5a-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="97a5a-183">Os arquivos são carregados no pipeline de solicitação invocando o método de extensão `UseStaticFiles` de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="97a5a-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="97a5a-184">Se você usar o .NET Framework como destino, instale o pacote NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="97a5a-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="97a5a-185">Por exemplo, um ativo de imagem na pasta *wwwroot/imagens* está acessível para o navegador em um local como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="97a5a-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="97a5a-186">Para obter uma referência mais aprofundada sobre como servir arquivos estáticos no ASP.NET Core, veja [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="97a5a-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-cookies"></a><span data-ttu-id="97a5a-187">Cookies com vários valores</span><span class="sxs-lookup"><span data-stu-id="97a5a-187">Multi-value cookies</span></span>

<span data-ttu-id="97a5a-188">Não há suporte para [cookies de vários valores](xref:System.Web.HttpCookie.Values) no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="97a5a-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="97a5a-189">Crie um cookie por valor.</span><span class="sxs-lookup"><span data-stu-id="97a5a-189">Create one cookie per value.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97a5a-190">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="97a5a-190">Additional resources</span></span>

- [<span data-ttu-id="97a5a-191">Fazendo a portabilidade de Bibliotecas para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="97a5a-191">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
