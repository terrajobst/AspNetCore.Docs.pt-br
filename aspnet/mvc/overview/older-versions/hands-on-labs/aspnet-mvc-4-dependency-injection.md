---
uid: mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-dependency-injection
title: Injeção de dependência do ASP.NET MVC 4 | Microsoft Docs
author: rick-anderson
description: 'Observação: Esse laboratório prático pressupõe que você tenha um conhecimento básico dos filtros ASP.NET MVC e ASP.NET MVC 4. Se você não usou filtros ASP.NET MVC 4 antes, podemos rec...'
ms.author: riande
ms.date: 02/18/2013
ms.assetid: 84c7baca-1c54-4c44-8f52-4282122d6acb
msc.legacyurl: /mvc/overview/older-versions/hands-on-labs/aspnet-mvc-4-dependency-injection
msc.type: authoredcontent
ms.openlocfilehash: 3f9222c7b485f552da91f4875c882db7e03cdd0a
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41825195"
---
# <a name="aspnet-mvc-4-dependency-injection"></a><span data-ttu-id="0a065-104">Injeção de dependência do ASP.NET MVC 4</span><span class="sxs-lookup"><span data-stu-id="0a065-104">ASP.NET MVC 4 Dependency Injection</span></span>

<span data-ttu-id="0a065-105">por [Web Camps equipe](https://twitter.com/webcamps)</span><span class="sxs-lookup"><span data-stu-id="0a065-105">By [Web Camps Team](https://twitter.com/webcamps)</span></span>

[<span data-ttu-id="0a065-106">Baixe o Kit de treinamento do Web Camps</span><span class="sxs-lookup"><span data-stu-id="0a065-106">Download Web Camps Training Kit</span></span>](https://aka.ms/webcamps-training-kit)

<span data-ttu-id="0a065-107">Este laboratório prático pressupõe que você tenha um conhecimento básico dos **ASP.NET MVC** e **filtros ASP.NET MVC 4**.</span><span class="sxs-lookup"><span data-stu-id="0a065-107">This Hands-on Lab assumes you have basic knowledge of **ASP.NET MVC** and **ASP.NET MVC 4 filters**.</span></span> <span data-ttu-id="0a065-108">Se você não usou **filtros ASP.NET MVC 4** antes, recomendamos que você passe **filtros de ação do ASP.NET MVC personalizado** laboratório prático.</span><span class="sxs-lookup"><span data-stu-id="0a065-108">If you have not used **ASP.NET MVC 4 filters** before, we recommend you to go over **ASP.NET MVC Custom Action Filters** Hands-on Lab.</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-109">Todo o código de exemplo e trechos de código são incluídos no Web Camps treinamento Kit, disponível no site da [versões Web/Microsoft-WebCampTrainingKit](https://aka.ms/webcamps-training-kit).</span><span class="sxs-lookup"><span data-stu-id="0a065-109">All sample code and snippets are included in the Web Camps Training Kit, available from at [Microsoft-Web/WebCampTrainingKit Releases](https://aka.ms/webcamps-training-kit).</span></span> <span data-ttu-id="0a065-110">O projeto específico para este laboratório está disponível em [injeção de dependência do ASP.NET MVC 4](https://github.com/Microsoft-Web/HOL-MVC4DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="0a065-110">The project specific to this lab is available at [ASP.NET MVC 4 Dependency Injection](https://github.com/Microsoft-Web/HOL-MVC4DependencyInjection).</span></span>

<span data-ttu-id="0a065-111">Na **programação orientada a objeto** paradigma, objetos trabalham em conjunto em um modelo de colaboração nos quais há colaboradores e os consumidores.</span><span class="sxs-lookup"><span data-stu-id="0a065-111">In **Object Oriented Programming** paradigm, objects work together in a collaboration model where there are contributors and consumers.</span></span> <span data-ttu-id="0a065-112">Naturalmente, esse modelo de comunicação gera dependências entre objetos e componentes, tornando-se difícil gerenciar a complexidade aumenta.</span><span class="sxs-lookup"><span data-stu-id="0a065-112">Naturally, this communication model generates dependencies between objects and components, becoming difficult to manage when complexity increases.</span></span>

<span data-ttu-id="0a065-113">![Dependências de classe e a complexidade do modelo](aspnet-mvc-4-dependency-injection/_static/image1.png "dependências de classe e a complexidade do modelo")</span><span class="sxs-lookup"><span data-stu-id="0a065-113">![Class dependencies and model complexity](aspnet-mvc-4-dependency-injection/_static/image1.png "Class dependencies and model complexity")</span></span>

<span data-ttu-id="0a065-114">*Dependências de classe e a complexidade do modelo*</span><span class="sxs-lookup"><span data-stu-id="0a065-114">*Class dependencies and model complexity*</span></span>

<span data-ttu-id="0a065-115">Você já deve ter ouvido sobre o **padrão de fábrica** e a separação entre a interface e a implementação usando os serviços, onde os objetos de cliente geralmente são responsáveis por local do serviço.</span><span class="sxs-lookup"><span data-stu-id="0a065-115">You have probably heard about the **Factory Pattern** and the separation between the interface and the implementation using services, where the client objects are often responsible for service location.</span></span>

<span data-ttu-id="0a065-116">O padrão de injeção de dependência é uma implementação específica de inversão de controle.</span><span class="sxs-lookup"><span data-stu-id="0a065-116">The Dependency Injection pattern is a particular implementation of Inversion of Control.</span></span> <span data-ttu-id="0a065-117">**Inversão de controle (IoC)** significa que os objetos não criar outros objetos que eles dependem para realizar seu trabalho.</span><span class="sxs-lookup"><span data-stu-id="0a065-117">**Inversion of Control (IoC)** means that objects do not create other objects on which they rely to do their work.</span></span> <span data-ttu-id="0a065-118">Em vez disso, eles obtêm os objetos que eles precisam de uma fonte externa (por exemplo, um arquivo de configuração xml).</span><span class="sxs-lookup"><span data-stu-id="0a065-118">Instead, they get the objects that they need from an outside source (for example, an xml configuration file).</span></span>

<span data-ttu-id="0a065-119">**Injeção de dependência (DI)** significa que isso é feito sem a intervenção de objeto, geralmente por um componente de estrutura que passa parâmetros do construtor e definir propriedades.</span><span class="sxs-lookup"><span data-stu-id="0a065-119">**Dependency Injection (DI)** means that this is done without the object intervention, usually by a framework component that passes constructor parameters and set properties.</span></span>

<a id="The_Dependency_Injection_DI_Design_Pattern"></a>
### <a name="the-dependency-injection-di-design-pattern"></a><span data-ttu-id="0a065-120">O padrão de Design de DI (injeção) de dependência</span><span class="sxs-lookup"><span data-stu-id="0a065-120">The Dependency Injection (DI) Design Pattern</span></span>

<span data-ttu-id="0a065-121">Em um alto nível, o objetivo da injeção de dependência é que uma classe de cliente (por exemplo, *o jogador*) precisa de algo que satisfaz uma interface (por exemplo, *IClub*).</span><span class="sxs-lookup"><span data-stu-id="0a065-121">At a high level, the goal of Dependency Injection is that a client class (e.g. *the golfer*) needs something that satisfies an interface (e.g. *IClub*).</span></span> <span data-ttu-id="0a065-122">Não importa o que é o tipo concreto (por exemplo, *WedgeClub WoodClub, IronClub,* ou *PutterClub*), ele quer alguém para lidar com isso (por exemplo, uma boa *caddy*).</span><span class="sxs-lookup"><span data-stu-id="0a065-122">It doesn't care what the concrete type is (e.g. *WoodClub, IronClub, WedgeClub* or *PutterClub*), it wants someone else to handle that (e.g. a good *caddy*).</span></span> <span data-ttu-id="0a065-123">O resolvedor de dependência no ASP.NET MVC pode permitir que você registre sua lógica de dependência em outro lugar (por exemplo, um contêiner ou um *recipiente de tacos*).</span><span class="sxs-lookup"><span data-stu-id="0a065-123">The Dependency Resolver in ASP.NET MVC can allow you to register your dependency logic somewhere else (e.g. a container or a *bag of clubs*).</span></span>

<span data-ttu-id="0a065-124">![Diagrama de injeção de dependência](aspnet-mvc-4-dependency-injection/_static/image2.png "ilustração de injeção de dependência")</span><span class="sxs-lookup"><span data-stu-id="0a065-124">![Dependency Injection diagram](aspnet-mvc-4-dependency-injection/_static/image2.png "Dependency Injection illustration")</span></span>

<span data-ttu-id="0a065-125">*Injeção de dependência - analogia de Golfe*</span><span class="sxs-lookup"><span data-stu-id="0a065-125">*Dependency Injection - Golf analogy*</span></span>

<span data-ttu-id="0a065-126">As vantagens de usar o padrão de injeção de dependência e inversão de controle são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="0a065-126">The advantages of using Dependency Injection pattern and Inversion of Control are the following:</span></span>

- <span data-ttu-id="0a065-127">Reduz o acoplamento de classes</span><span class="sxs-lookup"><span data-stu-id="0a065-127">Reduces class coupling</span></span>
- <span data-ttu-id="0a065-128">Aumenta a reutilização de código</span><span class="sxs-lookup"><span data-stu-id="0a065-128">Increases code reusing</span></span>
- <span data-ttu-id="0a065-129">Melhora a capacidade de manutenção do código</span><span class="sxs-lookup"><span data-stu-id="0a065-129">Improves code maintainability</span></span>
- <span data-ttu-id="0a065-130">Melhora o teste de aplicativos</span><span class="sxs-lookup"><span data-stu-id="0a065-130">Improves application testing</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-131">Injeção de dependência às vezes é comparada com o padrão de Design fábrica abstrata, mas há uma pequena diferença entre as duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="0a065-131">Dependency Injection is sometimes compared with Abstract Factory Design Pattern, but there is a slight difference between both approaches.</span></span> <span data-ttu-id="0a065-132">Injeção de dependência tem uma estrutura trabalhando por trás para resolver as dependências, chamando as fábricas e os serviços registrados.</span><span class="sxs-lookup"><span data-stu-id="0a065-132">DI has a Framework working behind to solve dependencies by calling the factories and the registered services.</span></span>


<span data-ttu-id="0a065-133">Agora que você entende o padrão de injeção de dependência, você aprenderá em todo este laboratório para aplicá-la no ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="0a065-133">Now that you understand the Dependency Injection Pattern, you will learn throughout this lab how to apply it in ASP.NET MVC 4.</span></span> <span data-ttu-id="0a065-134">Você começará a usar a injeção de dependência na **controladores** para incluir um serviço de acesso do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a065-134">You will start using Dependency Injection in the **Controllers** to include a database access service.</span></span> <span data-ttu-id="0a065-135">Em seguida, você aplicará a injeção de dependência para o **modos de exibição** para consumir um serviço e mostram informações.</span><span class="sxs-lookup"><span data-stu-id="0a065-135">Next, you will apply Dependency Injection to the **Views** to consume a service and show information.</span></span> <span data-ttu-id="0a065-136">Por fim, você estenderá a DI para filtros do ASP.NET MVC 4, injetando um filtro de ação personalizada na solução.</span><span class="sxs-lookup"><span data-stu-id="0a065-136">Finally, you will extend the DI to ASP.NET MVC 4 Filters, injecting a custom action filter in the solution.</span></span>

<span data-ttu-id="0a065-137">Neste laboratório prático, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="0a065-137">In this Hands-on Lab, you will learn how to:</span></span>

- <span data-ttu-id="0a065-138">Integrar o ASP.NET MVC 4 com o Unity para injeção de dependência usando pacotes NuGet</span><span class="sxs-lookup"><span data-stu-id="0a065-138">Integrate ASP.NET MVC 4 with Unity for Dependency Injection using NuGet Packages</span></span>
- <span data-ttu-id="0a065-139">Usar injeção de dependência dentro de um controlador ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="0a065-139">Use Dependency Injection inside an ASP.NET MVC Controller</span></span>
- <span data-ttu-id="0a065-140">Usar injeção de dependência dentro de uma exibição ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="0a065-140">Use Dependency Injection inside an ASP.NET MVC View</span></span>
- <span data-ttu-id="0a065-141">Usar injeção de dependência dentro de um filtro de ação do ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="0a065-141">Use Dependency Injection inside an ASP.NET MVC Action Filter</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-142">Este laboratório é usando o pacote do NuGet Unity.Mvc3 para resolução de dependência, mas é possível adaptar qualquer estrutura de injeção de dependência para trabalhar com o ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="0a065-142">This Lab is using Unity.Mvc3 NuGet Package for dependency resolution, but it is possible to adapt any Dependency Injection Framework to work with ASP.NET MVC 4.</span></span>


<a id="Prerequisites"></a>

<a id="Prerequisites"></a>
### <a name="prerequisites"></a><span data-ttu-id="0a065-143">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0a065-143">Prerequisites</span></span>

<span data-ttu-id="0a065-144">Você deve ter os seguintes itens para concluir este laboratório:</span><span class="sxs-lookup"><span data-stu-id="0a065-144">You must have the following items to complete this lab:</span></span>

- <span data-ttu-id="0a065-145">[Microsoft Visual Studio Express 2012 para Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) ou superior (leia [Apêndice A](#AppendixA) para obter instruções sobre como instalá-lo).</span><span class="sxs-lookup"><span data-stu-id="0a065-145">[Microsoft Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/eng/products/visual-studio-express-for-web) or superior (read [Appendix A](#AppendixA) for instructions on how to install it).</span></span>

<a id="Setup"></a>

<a id="Setup"></a>
### <a name="setup"></a><span data-ttu-id="0a065-146">Configuração</span><span class="sxs-lookup"><span data-stu-id="0a065-146">Setup</span></span>

<span data-ttu-id="0a065-147">**Instalando os trechos de código**</span><span class="sxs-lookup"><span data-stu-id="0a065-147">**Installing Code Snippets**</span></span>

<span data-ttu-id="0a065-148">Para sua conveniência, grande parte do código que você estiver gerenciando ao longo deste laboratório está disponível como trechos de código do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0a065-148">For convenience, much of the code you will be managing along this lab is available as Visual Studio code snippets.</span></span> <span data-ttu-id="0a065-149">Para instalar os trechos de código executados **.\Source\Setup\CodeSnippets.vsi** arquivo.</span><span class="sxs-lookup"><span data-stu-id="0a065-149">To install the code snippets run **.\Source\Setup\CodeSnippets.vsi** file.</span></span>

<span data-ttu-id="0a065-150">Se você não estiver familiarizado com os trechos de código do Visual Studio e aprender como usá-los, consulte o Apêndice deste documento &quot; [apêndice b: usando o Code Snippets](#AppendixB)&quot;.</span><span class="sxs-lookup"><span data-stu-id="0a065-150">If you are not familiar with the Visual Studio Code Snippets, and want to learn how to use them, you can refer to the appendix from this document &quot;[Appendix B: Using Code Snippets](#AppendixB)&quot;.</span></span>

* * *

<a id="Exercises"></a>

<a id="Exercises"></a>
## <a name="exercises"></a><span data-ttu-id="0a065-151">Exercícios</span><span class="sxs-lookup"><span data-stu-id="0a065-151">Exercises</span></span>

<span data-ttu-id="0a065-152">Este laboratório prático é composto pelos seguintes exercícios:</span><span class="sxs-lookup"><span data-stu-id="0a065-152">This Hands-On Lab is comprised by the following exercises:</span></span>

1. [<span data-ttu-id="0a065-153">Exercício 1: Injetando um controlador</span><span class="sxs-lookup"><span data-stu-id="0a065-153">Exercise 1: Injecting a Controller</span></span>](#Exercise1)
2. [<span data-ttu-id="0a065-154">Exercício 2: Injetando um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="0a065-154">Exercise 2: Injecting a View</span></span>](#Exercise2)
3. [<span data-ttu-id="0a065-155">Exercício 3: Injetando filtros</span><span class="sxs-lookup"><span data-stu-id="0a065-155">Exercise 3: Injecting Filters</span></span>](#Exercise3)

> [!NOTE]
> <span data-ttu-id="0a065-156">Cada exercício é acompanhado por um **final** pasta que contém a solução resultante, você deve obter depois de concluir os exercícios.</span><span class="sxs-lookup"><span data-stu-id="0a065-156">Each exercise is accompanied by an **End** folder containing the resulting solution you should obtain after completing the exercises.</span></span> <span data-ttu-id="0a065-157">Você pode usar essa solução como um guia se você precisar trabalhar com os exercícios de ajuda adicional.</span><span class="sxs-lookup"><span data-stu-id="0a065-157">You can use this solution as a guide if you need additional help working through the exercises.</span></span>


<span data-ttu-id="0a065-158">Tempo estimado para concluir este laboratório: **30 minutos**.</span><span class="sxs-lookup"><span data-stu-id="0a065-158">Estimated time to complete this lab: **30 minutes**.</span></span>

<a id="Exercise1"></a>

<a id="Exercise_1_Injecting_a_Controller"></a>
### <a name="exercise-1-injecting-a-controller"></a><span data-ttu-id="0a065-159">Exercício 1: Injetando um controlador</span><span class="sxs-lookup"><span data-stu-id="0a065-159">Exercise 1: Injecting a Controller</span></span>

<span data-ttu-id="0a065-160">Neste exercício, você aprenderá como usar injeção de dependência em controladores do ASP.NET MVC, a integração do Unity usando um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="0a065-160">In this exercise, you will learn how to use Dependency Injection in ASP.NET MVC Controllers by integrating Unity using a NuGet Package.</span></span> <span data-ttu-id="0a065-161">Por esse motivo, você incluirá serviços nos controladores MvcMusicStore para separar a lógica de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="0a065-161">For that reason, you will include services into your MvcMusicStore controllers to separate the logic from the data access.</span></span> <span data-ttu-id="0a065-162">Os serviços criará uma nova dependência no construtor do controlador, que será resolvido usando a injeção de dependência com a Ajuda do **Unity**.</span><span class="sxs-lookup"><span data-stu-id="0a065-162">The services will create a new dependency in the controller constructor, which will be resolved using Dependency Injection with the help of **Unity**.</span></span>

<span data-ttu-id="0a065-163">Essa abordagem mostrará como gerar menos aplicativos acoplados, que são mais flexíveis e fáceis de manter e testar.</span><span class="sxs-lookup"><span data-stu-id="0a065-163">This approach will show you how to generate less coupled applications, which are more flexible and easier to maintain and test.</span></span> <span data-ttu-id="0a065-164">Você também aprenderá como integrar o ASP.NET MVC com o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-164">You will also learn how to integrate ASP.NET MVC with Unity.</span></span>

<a id="About_StoreManager_Service"></a>
#### <a name="about-storemanager-service"></a><span data-ttu-id="0a065-165">Sobre o serviço StoreManager</span><span class="sxs-lookup"><span data-stu-id="0a065-165">About StoreManager Service</span></span>

<span data-ttu-id="0a065-166">A Store de música do MVC fornecida na solução begin agora inclui um serviço que gerencia os dados do Store controlador nomeados **StoreService**.</span><span class="sxs-lookup"><span data-stu-id="0a065-166">The MVC Music Store provided in the begin solution now includes a service that manages the Store Controller data named **StoreService**.</span></span> <span data-ttu-id="0a065-167">Abaixo você encontrará a implementação do serviço de Store.</span><span class="sxs-lookup"><span data-stu-id="0a065-167">Below you will find the Store Service implementation.</span></span> <span data-ttu-id="0a065-168">Observe que todos os métodos retornam entidades de modelo.</span><span class="sxs-lookup"><span data-stu-id="0a065-168">Note that all the methods return Model entities.</span></span>

[!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample1.cs)]

<span data-ttu-id="0a065-169">**StoreController** do begin solução agora consome **StoreService**.</span><span class="sxs-lookup"><span data-stu-id="0a065-169">**StoreController** from the begin solution now consumes **StoreService**.</span></span> <span data-ttu-id="0a065-170">Todas as referências de dados foram removidas do **StoreController**e agora é possível modificar o provedor de acesso de dados atual sem alterar qualquer método que consome **StoreService**.</span><span class="sxs-lookup"><span data-stu-id="0a065-170">All the data references were removed from **StoreController**, and now possible to modify the current data access provider without changing any method that consumes **StoreService**.</span></span>

<span data-ttu-id="0a065-171">Abaixo disso, você encontrará os **StoreController** implementação tem uma dependência com **StoreService** dentro do construtor de classe.</span><span class="sxs-lookup"><span data-stu-id="0a065-171">You will find below that the **StoreController** implementation has a dependency with **StoreService** inside the class constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-172">A dependência introduzida neste exercício está relacionada ao **inversão de controle** (IoC).</span><span class="sxs-lookup"><span data-stu-id="0a065-172">The dependency introduced in this exercise is related to **Inversion of Control** (IoC).</span></span>
> 
> <span data-ttu-id="0a065-173">O **StoreController** construtor de classe recebe um **IStoreService** parâmetro de tipo, que é essencial para realizar chamadas de serviço de dentro da classe.</span><span class="sxs-lookup"><span data-stu-id="0a065-173">The **StoreController** class constructor receives an **IStoreService** type parameter, which is essential to perform service calls from inside the class.</span></span> <span data-ttu-id="0a065-174">No entanto, **StoreController** não implementa o construtor padrão (sem parâmetros) que qualquer controlador deve ter para funcionar com o ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="0a065-174">However, **StoreController** does not implement the default constructor (with no parameters) that any controller must have to work with ASP.NET MVC.</span></span>
> 
> <span data-ttu-id="0a065-175">Para resolver a dependência, o controlador deve ser criado por uma fábrica abstrata (uma classe que retorna qualquer objeto do tipo especificado).</span><span class="sxs-lookup"><span data-stu-id="0a065-175">To resolve the dependency, the controller has to be created by an abstract factory (a class that returns any object of the specified type).</span></span>


[!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample2.cs)]

> [!NOTE]
> <span data-ttu-id="0a065-176">Você receberá um erro quando a classe tenta criar a StoreController sem enviar o objeto de serviço, pois não há nenhum construtor sem parâmetros declarado.</span><span class="sxs-lookup"><span data-stu-id="0a065-176">You will get an error when the class tries to create the StoreController without sending the service object, as there is no parameterless constructor declared.</span></span>


<a id="Ex1Task1"></a>

<a id="Task_1_-_Running_the_Application"></a>
#### <a name="task-1---running-the-application"></a><span data-ttu-id="0a065-177">Tarefa 1 - execução do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0a065-177">Task 1 - Running the Application</span></span>

<span data-ttu-id="0a065-178">Nesta tarefa, você executará o aplicativo de início, que inclui o serviço para o controlador de Store que separa o acesso a dados da lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a065-178">In this task, you will run the Begin application, which includes the service into the Store Controller that separates the data access from the application logic.</span></span>

<span data-ttu-id="0a065-179">Ao executar o aplicativo, você receberá uma exceção, como o serviço de controlador não é passado como um parâmetro por padrão:</span><span class="sxs-lookup"><span data-stu-id="0a065-179">When running the application, you will receive an exception, as the controller service is not passed as a parameter by default:</span></span>

1. <span data-ttu-id="0a065-180">Abra o **começar** solução localizada no **Controller\Begin injetando o Source\Ex01**.</span><span class="sxs-lookup"><span data-stu-id="0a065-180">Open the **Begin** solution located in **Source\Ex01-Injecting Controller\Begin**.</span></span>

   1. <span data-ttu-id="0a065-181">Você precisará baixar alguns pacotes do NuGet ausentes antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0a065-181">You will need to download some missing NuGet packages before continue.</span></span> <span data-ttu-id="0a065-182">Para fazer isso, clique o **Project** menu e selecione **Manage NuGet Packages**.</span><span class="sxs-lookup"><span data-stu-id="0a065-182">To do this, click the **Project** menu and select **Manage NuGet Packages**.</span></span>
   2. <span data-ttu-id="0a065-183">No **gerenciar pacotes NuGet** caixa de diálogo, clique em **restaurar** para baixar os pacotes ausentes.</span><span class="sxs-lookup"><span data-stu-id="0a065-183">In the **Manage NuGet Packages** dialog, click **Restore** in order to download missing packages.</span></span>
   3. <span data-ttu-id="0a065-184">Por fim, compile a solução clicando **construir** | **compilar solução**.</span><span class="sxs-lookup"><span data-stu-id="0a065-184">Finally, build the solution by clicking **Build** | **Build Solution**.</span></span>

      > [!NOTE]
      > <span data-ttu-id="0a065-185">Uma das vantagens de usar o NuGet é que você não precisa enviar todas as bibliotecas em seu projeto, reduzindo o tamanho do projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-185">One of the advantages of using NuGet is that you don't have to ship all the libraries in your project, reducing the project size.</span></span> <span data-ttu-id="0a065-186">Com o NuGet Power Tools, especificando as versões do pacote no arquivo Packages. config, você será capaz de baixar todas as bibliotecas necessárias na primeira vez em que você executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-186">With NuGet Power Tools, by specifying the package versions in the Packages.config file, you will be able to download all the required libraries the first time you run the project.</span></span> <span data-ttu-id="0a065-187">É por isso você terá que executar essas etapas depois de abrir uma solução existente neste laboratório.</span><span class="sxs-lookup"><span data-stu-id="0a065-187">This is why you will have to run these steps after you open an existing solution from this lab.</span></span>
2. <span data-ttu-id="0a065-188">Pressione **Ctrl + F5** para executar o aplicativo sem depuração.</span><span class="sxs-lookup"><span data-stu-id="0a065-188">Press **Ctrl + F5** to run the application without debugging.</span></span> <span data-ttu-id="0a065-189">Você receberá a mensagem de erro &quot; **nenhum construtor sem parâmetros definido para este objeto**&quot;:</span><span class="sxs-lookup"><span data-stu-id="0a065-189">You will get the error message &quot;**No parameterless constructor defined for this object**&quot;:</span></span>

    <span data-ttu-id="0a065-190">![Erro durante a execução de aplicativo do ASP.NET MVC começam](aspnet-mvc-4-dependency-injection/_static/image3.png "erro durante a execução de aplicativo do ASP.NET MVC começar")</span><span class="sxs-lookup"><span data-stu-id="0a065-190">![Error while running ASP.NET MVC Begin Application](aspnet-mvc-4-dependency-injection/_static/image3.png "Error while running ASP.NET MVC Begin Application")</span></span>

    <span data-ttu-id="0a065-191">*Erro durante a execução de aplicativo do ASP.NET MVC começar*</span><span class="sxs-lookup"><span data-stu-id="0a065-191">*Error while running ASP.NET MVC Begin Application*</span></span>
3. <span data-ttu-id="0a065-192">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="0a065-192">Close the browser.</span></span>

<span data-ttu-id="0a065-193">As etapas a seguir, você trabalhará na solução de Store de música para injetar a dependência que este controlador precisa.</span><span class="sxs-lookup"><span data-stu-id="0a065-193">In the following steps you will work on the Music Store Solution to inject the dependency this controller needs.</span></span>

<a id="Ex1Task2"></a>

<a id="Task_2_-_Including_Unity_into_MvcMusicStore_Solution"></a>
#### <a name="task-2---including-unity-into-mvcmusicstore-solution"></a><span data-ttu-id="0a065-194">Tarefa 2 - incluindo Unity em solução MvcMusicStore</span><span class="sxs-lookup"><span data-stu-id="0a065-194">Task 2 - Including Unity into MvcMusicStore Solution</span></span>

<span data-ttu-id="0a065-195">Nesta tarefa, você incluirá **Unity.Mvc3** pacote do NuGet para a solução.</span><span class="sxs-lookup"><span data-stu-id="0a065-195">In this task, you will include **Unity.Mvc3** NuGet Package to the solution.</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-196">Pacote de Unity.Mvc3 foi designado para ASP.NET MVC 3, mas ele é totalmente compatível com o ASP.NET MVC 4.</span><span class="sxs-lookup"><span data-stu-id="0a065-196">Unity.Mvc3 package was designed for ASP.NET MVC 3, but it is fully compatible with ASP.NET MVC 4.</span></span>
> 
> <span data-ttu-id="0a065-197">O Unity é um contêiner de injeção de dependência leve e extensível com suporte opcional por instância e interceptação de tipo.</span><span class="sxs-lookup"><span data-stu-id="0a065-197">Unity is a lightweight, extensible dependency injection container with optional support for instance and type interception.</span></span> <span data-ttu-id="0a065-198">Ele é um contêiner de finalidade geral para uso em qualquer tipo de aplicativo do .NET.</span><span class="sxs-lookup"><span data-stu-id="0a065-198">It is a general-purpose container for use in any type of .NET application.</span></span> <span data-ttu-id="0a065-199">Ele fornece todos os recursos comuns encontrados em mecanismos de injeção de dependência incluindo: criação de objetos, a abstração de requisitos com a especificação de dependências em tempo de execução e a flexibilidade, adiando a configuração de componente para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="0a065-199">It provides all the common features found in dependency injection mechanisms including: object creation, abstraction of requirements by specifying dependencies at runtime and flexibility, by deferring the component configuration to the container.</span></span>


1. <span data-ttu-id="0a065-200">Instale **Unity.Mvc3** pacote do NuGet na **MvcMusicStore** projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-200">Install **Unity.Mvc3** NuGet Package in the **MvcMusicStore** project.</span></span> <span data-ttu-id="0a065-201">Para fazer isso, abra o **Package Manager Console** partir **exibição** | **Other Windows**.</span><span class="sxs-lookup"><span data-stu-id="0a065-201">To do this, open the **Package Manager Console** from **View** | **Other Windows**.</span></span>
2. <span data-ttu-id="0a065-202">Execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="0a065-202">Run the following command.</span></span>

    <span data-ttu-id="0a065-203">PMC</span><span class="sxs-lookup"><span data-stu-id="0a065-203">PMC</span></span>

    [!code-powershell[Main](aspnet-mvc-4-dependency-injection/samples/sample3.ps1)]

    <span data-ttu-id="0a065-204">![Instalar o pacote do NuGet Unity.Mvc3](aspnet-mvc-4-dependency-injection/_static/image4.png "instalar o pacote do NuGet Unity.Mvc3")</span><span class="sxs-lookup"><span data-stu-id="0a065-204">![Installing Unity.Mvc3 NuGet Package](aspnet-mvc-4-dependency-injection/_static/image4.png "Installing Unity.Mvc3 NuGet Package")</span></span>

    <span data-ttu-id="0a065-205">*Instalar o pacote do NuGet Unity.Mvc3*</span><span class="sxs-lookup"><span data-stu-id="0a065-205">*Installing Unity.Mvc3 NuGet Package*</span></span>
3. <span data-ttu-id="0a065-206">Uma vez a **Unity.Mvc3** pacote é instalado, explore os arquivos e pastas que adiciona automaticamente a fim de simplificar a configuração do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-206">Once the **Unity.Mvc3** package is installed, explore the files and folders it automatically adds in order to simplify Unity configuration.</span></span>

    <span data-ttu-id="0a065-207">![Pacote de Unity.Mvc3 instalado](aspnet-mvc-4-dependency-injection/_static/image5.png "Unity.Mvc3 pacote instalado")</span><span class="sxs-lookup"><span data-stu-id="0a065-207">![Unity.Mvc3 package installed](aspnet-mvc-4-dependency-injection/_static/image5.png "Unity.Mvc3 package installed")</span></span>

    <span data-ttu-id="0a065-208">*Pacote de Unity.Mvc3 instalado*</span><span class="sxs-lookup"><span data-stu-id="0a065-208">*Unity.Mvc3 package installed*</span></span>

<a id="Ex1Task3"></a>

<a id="Task_3_-_Registering_Unity_in_Globalasaxcs_Application_Start"></a>
#### <a name="task-3---registering-unity-in-globalasaxcs-applicationstart"></a><span data-ttu-id="0a065-209">Tarefa 3 - registrando Unity em Global.asax.cs aplicativo\_iniciar</span><span class="sxs-lookup"><span data-stu-id="0a065-209">Task 3 - Registering Unity in Global.asax.cs Application\_Start</span></span>

<span data-ttu-id="0a065-210">Nesta tarefa, você atualizará o **aplicativo\_começar** método localizado na **Global.asax.cs** para chamar o inicializador de Bootstrapper do Unity e em seguida, atualize o arquivo Bootstrapper o registro o serviço e o controlador usará para injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-210">In this task, you will update the **Application\_Start** method located in **Global.asax.cs** to call the Unity Bootstrapper initializer and then, update the Bootstrapper file registering the Service and Controller you will use for Dependency Injection.</span></span>

1. <span data-ttu-id="0a065-211">Agora, você irá se conectar o Bootstrapper que é o arquivo que inicializa o contêiner do Unity e o resolvedor de dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-211">Now, you will hook up the Bootstrapper which is the file that initializes the Unity container and Dependency Resolver.</span></span> <span data-ttu-id="0a065-212">Para fazer isso, abra **Global.asax.cs** e adicione o seguinte código realçado dentro de **Application\_iniciar** método.</span><span class="sxs-lookup"><span data-stu-id="0a065-212">To do this, open **Global.asax.cs** and add the following highlighted code within the **Application\_Start** method.</span></span>

    <span data-ttu-id="0a065-213">(Código de trecho de código – *laboratório de injeção de dependência do ASP.NET - Ex01 - inicializar Unity*)</span><span class="sxs-lookup"><span data-stu-id="0a065-213">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex01 - Initialize Unity*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample4.cs)]
2. <span data-ttu-id="0a065-214">Abra **Bootstrapper.cs** arquivo.</span><span class="sxs-lookup"><span data-stu-id="0a065-214">Open **Bootstrapper.cs** file.</span></span>
3. <span data-ttu-id="0a065-215">Inclua os seguintes namespaces: **MvcMusicStore.Services** e **MusicStore.Controllers**.</span><span class="sxs-lookup"><span data-stu-id="0a065-215">Include the following namespaces: **MvcMusicStore.Services** and **MusicStore.Controllers**.</span></span>

    <span data-ttu-id="0a065-216">(Código de trecho de código – *injeção de dependência ASP.NET laboratório - Ex01 - Bootstrapper adicionando Namespaces*)</span><span class="sxs-lookup"><span data-stu-id="0a065-216">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex01 - Bootstrapper Adding Namespaces*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample5.cs)]
4. <span data-ttu-id="0a065-217">Substitua **BuildUnityContainer** do conteúdo com o seguinte código que registra o controlador de Store e o serviço de Store de método.</span><span class="sxs-lookup"><span data-stu-id="0a065-217">Replace **BuildUnityContainer** method's content with the following code that registers Store Controller and Store Service.</span></span>

    <span data-ttu-id="0a065-218">(Código de trecho de código – *ASP.NET dependência injeção laboratório - Ex01 - Store registrar controlador e serviço*)</span><span class="sxs-lookup"><span data-stu-id="0a065-218">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex01 - Register Store Controller and Service*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample6.cs)]

<a id="Ex1Task4"></a>

<a id="Task_4_-_Running_the_Application"></a>
#### <a name="task-4---running-the-application"></a><span data-ttu-id="0a065-219">Tarefa 4 - execução do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0a065-219">Task 4 - Running the Application</span></span>

<span data-ttu-id="0a065-220">Nesta tarefa, você executará o aplicativo para verificar que ele agora pode ser carregado após incluir o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-220">In this task, you will run the application to verify that it can now be loaded after including Unity.</span></span>

1. <span data-ttu-id="0a065-221">Pressione **F5** para executar o aplicativo, o aplicativo deve carregar agora sem mostrar qualquer mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="0a065-221">Press **F5** to run the application, the application should now load without showing any error message.</span></span>

    <span data-ttu-id="0a065-222">![Executando o aplicativo com injeção de dependência](aspnet-mvc-4-dependency-injection/_static/image6.png "executando o aplicativo com injeção de dependência")</span><span class="sxs-lookup"><span data-stu-id="0a065-222">![Running Application with Dependency Injection](aspnet-mvc-4-dependency-injection/_static/image6.png "Running Application with Dependency Injection")</span></span>

    <span data-ttu-id="0a065-223">*Aplicativo em execução com injeção de dependência*</span><span class="sxs-lookup"><span data-stu-id="0a065-223">*Running Application with Dependency Injection*</span></span>
2. <span data-ttu-id="0a065-224">Navegue até **/Store**.</span><span class="sxs-lookup"><span data-stu-id="0a065-224">Browse to **/Store**.</span></span> <span data-ttu-id="0a065-225">Isso invocará **StoreController**, que foi criado usando **Unity**.</span><span class="sxs-lookup"><span data-stu-id="0a065-225">This will invoke **StoreController**, which is now created using **Unity**.</span></span>

    <span data-ttu-id="0a065-226">![Store de música do MVC](aspnet-mvc-4-dependency-injection/_static/image7.png "Store de música do MVC")</span><span class="sxs-lookup"><span data-stu-id="0a065-226">![MVC Music Store](aspnet-mvc-4-dependency-injection/_static/image7.png "MVC Music Store")</span></span>

    <span data-ttu-id="0a065-227">*Store de música do MVC*</span><span class="sxs-lookup"><span data-stu-id="0a065-227">*MVC Music Store*</span></span>
3. <span data-ttu-id="0a065-228">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="0a065-228">Close the browser.</span></span>

<span data-ttu-id="0a065-229">Os seguintes exercícios, você aprenderá a estender o escopo de injeção de dependência para usá-lo dentro de exibições do ASP.NET MVC e filtros de ação.</span><span class="sxs-lookup"><span data-stu-id="0a065-229">In the following exercises you will learn how to extend the Dependency Injection scope to use it inside ASP.NET MVC Views and Action Filters.</span></span>

<a id="Exercise2"></a>

<a id="Exercise_2_Injecting_a_View"></a>
### <a name="exercise-2-injecting-a-view"></a><span data-ttu-id="0a065-230">Exercício 2: Injetando um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="0a065-230">Exercise 2: Injecting a View</span></span>

<span data-ttu-id="0a065-231">Neste exercício, você aprenderá como usar a injeção de dependência em uma exibição com os novos recursos do ASP.NET MVC 4 para integração com o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-231">In this exercise, you will learn how to use Dependency Injection in a view with the new features of ASP.NET MVC 4 for Unity integration.</span></span> <span data-ttu-id="0a065-232">Para fazer isso, você chamará um serviço personalizado dentro do Store procurar na exibição, que mostrará uma mensagem e uma imagem a seguir.</span><span class="sxs-lookup"><span data-stu-id="0a065-232">In order to do that, you will call a custom service inside the Store Browse View, which will show a message and an image below.</span></span>

<span data-ttu-id="0a065-233">Em seguida, você irá integrar o projeto com o Unity e criar um resolvedor de dependência personalizadas para injetar dependências.</span><span class="sxs-lookup"><span data-stu-id="0a065-233">Then, you will integrate the project with Unity and create a custom dependency resolver to inject the dependencies.</span></span>

<a id="Ex2Task1"></a>

<a id="Task_1_-_Creating_a_View_that_Consumes_a_Service"></a>
#### <a name="task-1---creating-a-view-that-consumes-a-service"></a><span data-ttu-id="0a065-234">Tarefa 1 - criar uma exibição que consome um serviço</span><span class="sxs-lookup"><span data-stu-id="0a065-234">Task 1 - Creating a View that Consumes a Service</span></span>

<span data-ttu-id="0a065-235">Nesta tarefa, você criará uma exibição que executa uma chamada de serviço para gerar uma nova dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-235">In this task, you will create a view that performs a service call to generate a new dependency.</span></span> <span data-ttu-id="0a065-236">O serviço consiste em um serviço de mensagens simple incluído nessa solução.</span><span class="sxs-lookup"><span data-stu-id="0a065-236">The service consists in a simple messaging service included in this solution.</span></span>

1. <span data-ttu-id="0a065-237">Abra o **começar** solução localizada na **View\Begin Source\Ex02-injetando** pasta.</span><span class="sxs-lookup"><span data-stu-id="0a065-237">Open the **Begin** solution located in the **Source\Ex02-Injecting View\Begin** folder.</span></span> <span data-ttu-id="0a065-238">Caso contrário, você pode continuar usando o **final** solução obtida ao concluir o exercício anterior.</span><span class="sxs-lookup"><span data-stu-id="0a065-238">Otherwise, you might continue using the **End** solution obtained by completing the previous exercise.</span></span>

   1. <span data-ttu-id="0a065-239">Se você abriu fornecido **começar** solução, você precisará baixar alguns pacotes do NuGet ausentes antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0a065-239">If you opened the provided **Begin** solution, you will need to download some missing NuGet packages before continue.</span></span> <span data-ttu-id="0a065-240">Para fazer isso, clique o **Project** menu e selecione **Manage NuGet Packages**.</span><span class="sxs-lookup"><span data-stu-id="0a065-240">To do this, click the **Project** menu and select **Manage NuGet Packages**.</span></span>
   2. <span data-ttu-id="0a065-241">No **gerenciar pacotes NuGet** caixa de diálogo, clique em **restaurar** para baixar os pacotes ausentes.</span><span class="sxs-lookup"><span data-stu-id="0a065-241">In the **Manage NuGet Packages** dialog, click **Restore** in order to download missing packages.</span></span>
   3. <span data-ttu-id="0a065-242">Por fim, compile a solução clicando **construir** | **compilar solução**.</span><span class="sxs-lookup"><span data-stu-id="0a065-242">Finally, build the solution by clicking **Build** | **Build Solution**.</span></span>

      > [!NOTE]
      > <span data-ttu-id="0a065-243">Uma das vantagens de usar o NuGet é que você não precisa enviar todas as bibliotecas em seu projeto, reduzindo o tamanho do projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-243">One of the advantages of using NuGet is that you don't have to ship all the libraries in your project, reducing the project size.</span></span> <span data-ttu-id="0a065-244">Com o NuGet Power Tools, especificando as versões do pacote no arquivo Packages. config, você será capaz de baixar todas as bibliotecas necessárias na primeira vez em que você executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-244">With NuGet Power Tools, by specifying the package versions in the Packages.config file, you will be able to download all the required libraries the first time you run the project.</span></span> <span data-ttu-id="0a065-245">É por isso você terá que executar essas etapas depois de abrir uma solução existente neste laboratório.</span><span class="sxs-lookup"><span data-stu-id="0a065-245">This is why you will have to run these steps after you open an existing solution from this lab.</span></span>
      > 
      > <span data-ttu-id="0a065-246">Para obter mais informações, consulte este artigo: [ http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages ](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages).</span><span class="sxs-lookup"><span data-stu-id="0a065-246">For more information, see this article: [http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages).</span></span>
2. <span data-ttu-id="0a065-247">Incluir o **MessageService.cs** e o **IMessageService.cs** classes localizado no **\Assets da fonte** pasta no **/serviços**.</span><span class="sxs-lookup"><span data-stu-id="0a065-247">Include the **MessageService.cs** and the **IMessageService.cs** classes located in the **Source \Assets** folder in **/Services**.</span></span> <span data-ttu-id="0a065-248">Para fazer isso, clique com botão direito **Services** pasta e selecione **Add Existing Item**.</span><span class="sxs-lookup"><span data-stu-id="0a065-248">To do this, right-click **Services** folder and select **Add Existing Item**.</span></span> <span data-ttu-id="0a065-249">Navegue até o local dos arquivos e incluí-los.</span><span class="sxs-lookup"><span data-stu-id="0a065-249">Browse to the files' location and include them.</span></span>

    <span data-ttu-id="0a065-250">![Adicionando o serviço de mensagens e a Interface de serviço](aspnet-mvc-4-dependency-injection/_static/image8.png "adicionando o serviço de mensagens e a Interface de serviço")</span><span class="sxs-lookup"><span data-stu-id="0a065-250">![Adding Message Service and Service Interface](aspnet-mvc-4-dependency-injection/_static/image8.png "Adding Message Service and Service Interface")</span></span>

    <span data-ttu-id="0a065-251">*Adicionar serviço de mensagens e a Interface de serviço*</span><span class="sxs-lookup"><span data-stu-id="0a065-251">*Adding Message Service and Service Interface*</span></span>

    > [!NOTE]
    > <span data-ttu-id="0a065-252">O **IMessageService** interface define duas propriedades implementadas pelo **MessageService** classe.</span><span class="sxs-lookup"><span data-stu-id="0a065-252">The **IMessageService** interface defines two properties implemented by the **MessageService** class.</span></span> <span data-ttu-id="0a065-253">Essas propriedades -**mensagem** e **ImageUrl**-armazenar a mensagem e a URL da imagem a ser exibida.</span><span class="sxs-lookup"><span data-stu-id="0a065-253">These properties -**Message** and **ImageUrl**- store the message and the URL of the image to be displayed.</span></span>
3. <span data-ttu-id="0a065-254">Crie a pasta **/Pages** no projeto de pasta raiz e, em seguida, adicione a classe existente **MyBasePage.cs** da **Source\Assets**.</span><span class="sxs-lookup"><span data-stu-id="0a065-254">Create the folder **/Pages** in the project's root folder, and then add the existing class **MyBasePage.cs** from **Source\Assets**.</span></span> <span data-ttu-id="0a065-255">Página de base, que você herda tem a seguinte estrutura.</span><span class="sxs-lookup"><span data-stu-id="0a065-255">The base page you will inherit from has the following structure.</span></span>

    <span data-ttu-id="0a065-256">![Pasta de páginas](aspnet-mvc-4-dependency-injection/_static/image9.png "pasta páginas")</span><span class="sxs-lookup"><span data-stu-id="0a065-256">![Pages folder](aspnet-mvc-4-dependency-injection/_static/image9.png "Pages folder")</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample7.cs)]
4. <span data-ttu-id="0a065-257">Abra **Browse.cshtml** exibir de **/Views/Store** pasta e torná-lo herdar **MyBasePage.cs**.</span><span class="sxs-lookup"><span data-stu-id="0a065-257">Open **Browse.cshtml** view from **/Views/Store** folder, and make it inherit from **MyBasePage.cs**.</span></span>

    [!code-cshtml[Main](aspnet-mvc-4-dependency-injection/samples/sample8.cshtml)]
5. <span data-ttu-id="0a065-258">No **navegue** exibir, adicionar uma chamada para **MessageService** para exibir uma imagem e uma mensagem recuperada pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="0a065-258">In the **Browse** view, add a call to **MessageService** to display an image and a message retrieved by the service.</span></span>
   <span data-ttu-id="0a065-259">(C#)</span><span class="sxs-lookup"><span data-stu-id="0a065-259">(C#)</span></span>

    [!code-cshtml[Main](aspnet-mvc-4-dependency-injection/samples/sample9.cshtml)]

<a id="Ex2Task2"></a>

<a id="Task_2_-_Including_a_Custom_Dependency_Resolver_and_a_Custom_View_Page_Activator"></a>
#### <a name="task-2---including-a-custom-dependency-resolver-and-a-custom-view-page-activator"></a><span data-ttu-id="0a065-260">Tarefa 2 – incluindo um resolvedor de dependência personalizada e um ativador de página do modo de exibição personalizado</span><span class="sxs-lookup"><span data-stu-id="0a065-260">Task 2 - Including a Custom Dependency Resolver and a Custom View Page Activator</span></span>

<span data-ttu-id="0a065-261">Na tarefa anterior, que injetou uma nova dependência dentro de um modo de exibição para executar uma chamada de serviço dentro dele.</span><span class="sxs-lookup"><span data-stu-id="0a065-261">In the previous task, you injected a new dependency inside a view to perform a service call inside it.</span></span> <span data-ttu-id="0a065-262">Agora, você resolverá essa dependência ao implementar as interfaces de injeção de dependência do ASP.NET MVC **IViewPageActivator** e **IDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="0a065-262">Now, you will resolve that dependency by implementing the ASP.NET MVC Dependency Injection interfaces **IViewPageActivator** and **IDependencyResolver**.</span></span> <span data-ttu-id="0a065-263">Você incluirá na solução de uma implementação de **IDependencyResolver** que lidará com a recuperação de serviço usando o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-263">You will include in the solution an implementation of **IDependencyResolver** that will deal with the service retrieval by using Unity.</span></span> <span data-ttu-id="0a065-264">Em seguida, você incluirá outra implementação personalizada de **IViewPageActivator** interface que resolverá a criação dos modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="0a065-264">Then, you will include another custom implementation of **IViewPageActivator** interface that will solve the creation of the views.</span></span>

> [!NOTE]
> <span data-ttu-id="0a065-265">Desde o ASP.NET MVC 3, a implementação para injeção de dependência tinha simplificado as interfaces para registrar os serviços.</span><span class="sxs-lookup"><span data-stu-id="0a065-265">Since ASP.NET MVC 3, the implementation for Dependency Injection had simplified the interfaces to register services.</span></span> <span data-ttu-id="0a065-266">**IDependencyResolver** e **IViewPageActivator** fazem parte dos recursos do ASP.NET MVC 3 para injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-266">**IDependencyResolver** and **IViewPageActivator** are part of ASP.NET MVC 3 features for Dependency Injection.</span></span>
> 
> <span data-ttu-id="0a065-267">**-IDependencyResolver** interface substitui o IMvcServiceLocator anterior.</span><span class="sxs-lookup"><span data-stu-id="0a065-267">**- IDependencyResolver** interface replaces the previous IMvcServiceLocator.</span></span> <span data-ttu-id="0a065-268">Os implementadores IDependencyResolver devem retornar uma instância do serviço ou uma coleção de serviços.</span><span class="sxs-lookup"><span data-stu-id="0a065-268">Implementers of IDependencyResolver must return an instance of the service or a service collection.</span></span>
> 
> 
> [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample10.cs)]
> 
> <span data-ttu-id="0a065-269">**-IViewPageActivator** interface fornece controle mais refinado sobre como exibir páginas são instanciadas por meio da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-269">**- IViewPageActivator** interface provides more fine-grained control over how view pages are instantiated via dependency injection.</span></span> <span data-ttu-id="0a065-270">As classes que implementam **IViewPageActivator** interface pode criar instâncias de exibição usando informações de contexto.</span><span class="sxs-lookup"><span data-stu-id="0a065-270">The classes that implement **IViewPageActivator** interface can create view instances using context information.</span></span>
> 
> 
> [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample11.cs)]


1. <span data-ttu-id="0a065-271">Criar o /**fábricas** pasta na pasta raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-271">Create the /**Factories** folder in the project's root folder.</span></span>
2. <span data-ttu-id="0a065-272">Incluir **CustomViewPageActivator.cs** à sua solução de **/fontes/ativos/** para **fábricas** pasta.</span><span class="sxs-lookup"><span data-stu-id="0a065-272">Include **CustomViewPageActivator.cs** to your solution from **/Sources/Assets/** to **Factories** folder.</span></span> <span data-ttu-id="0a065-273">Para fazer isso, clique com botão direito do **/Factories** pasta, selecione **adicionar | Item existente** e, em seguida, selecione **CustomViewPageActivator.cs**.</span><span class="sxs-lookup"><span data-stu-id="0a065-273">To do that, right-click the **/Factories** folder, select **Add | Existing Item** and then select **CustomViewPageActivator.cs**.</span></span> <span data-ttu-id="0a065-274">Essa classe implementa a **IViewPageActivator** interface para manter o contêiner do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-274">This class implements the **IViewPageActivator** interface to hold the Unity Container.</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample12.cs)]

    > [!NOTE]
    > <span data-ttu-id="0a065-275">**CustomViewPageActivator** é responsável por gerenciar a criação de um modo de exibição usando um contêiner do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-275">**CustomViewPageActivator** is responsible for managing the creation of a view by using a Unity container.</span></span>
3. <span data-ttu-id="0a065-276">Incluir **UnityDependencyResolver.cs** arquivo do **fontes/ativos** para **/Factories** pasta.</span><span class="sxs-lookup"><span data-stu-id="0a065-276">Include **UnityDependencyResolver.cs** file from **/Sources/Assets** to **/Factories** folder.</span></span> <span data-ttu-id="0a065-277">Para fazer isso, clique com botão direito do **/Factories** pasta, selecione **adicionar | Item existente** e, em seguida, selecione **UnityDependencyResolver.cs** arquivo.</span><span class="sxs-lookup"><span data-stu-id="0a065-277">To do that, right-click the **/Factories** folder, select **Add | Existing Item** and then select **UnityDependencyResolver.cs** file.</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample13.cs)]

    > [!NOTE]
    > <span data-ttu-id="0a065-278">**UnityDependencyResolver** classe é um DependencyResolver personalizado para o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-278">**UnityDependencyResolver** class is a custom DependencyResolver for Unity.</span></span> <span data-ttu-id="0a065-279">Quando um serviço não pode ser encontrado dentro do contêiner do Unity, o resolvedor de base é invocated.</span><span class="sxs-lookup"><span data-stu-id="0a065-279">When a service cannot be found inside the Unity container, the base resolver is invocated.</span></span>

<span data-ttu-id="0a065-280">Na tarefa a seguir, ambas as implementações serão registradas para o modelo de informar o local dos serviços e os modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="0a065-280">In the following task both implementations will be registered to let the model know the location of the services and the views.</span></span>

<a id="Ex2Task3"></a>

<a id="Task_3_-_Registering_for_Dependency_Injection_within_Unity_container"></a>
#### <a name="task-3---registering-for-dependency-injection-within-unity-container"></a><span data-ttu-id="0a065-281">Tarefa 3 - Registrando para injeção de dependência dentro do contêiner do Unity</span><span class="sxs-lookup"><span data-stu-id="0a065-281">Task 3 - Registering for Dependency Injection within Unity container</span></span>

<span data-ttu-id="0a065-282">Nesta tarefa, você colocará tudo o que anteriores juntas para fazer a injeção de dependência funcione.</span><span class="sxs-lookup"><span data-stu-id="0a065-282">In this task, you will put all the previous things together to make Dependency Injection work.</span></span>

<span data-ttu-id="0a065-283">Até agora, sua solução tem os seguintes elementos:</span><span class="sxs-lookup"><span data-stu-id="0a065-283">Up to now your solution has the following elements:</span></span>

- <span data-ttu-id="0a065-284">Um **navegue** modo de exibição que herda **MyBaseClass** e consome **MessageService**.</span><span class="sxs-lookup"><span data-stu-id="0a065-284">A **Browse** View that inherits from **MyBaseClass** and consumes **MessageService**.</span></span>
- <span data-ttu-id="0a065-285">Uma classe intermediária -**MyBaseClass**-que foi declarada para a interface do serviço de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0a065-285">An intermediate class -**MyBaseClass**- that has dependency injection declared for the service interface.</span></span>
- <span data-ttu-id="0a065-286">Um serviço - **MessageService** - e sua interface **IMessageService**.</span><span class="sxs-lookup"><span data-stu-id="0a065-286">A service - **MessageService** - and its interface **IMessageService**.</span></span>
- <span data-ttu-id="0a065-287">Um resolvedor de dependência personalizadas para Unity - **UnityDependencyResolver** -que lida com a recuperação de serviço.</span><span class="sxs-lookup"><span data-stu-id="0a065-287">A custom dependency resolver for Unity - **UnityDependencyResolver** - that deals with service retrieval.</span></span>
- <span data-ttu-id="0a065-288">Um ativador de página de exibição - **CustomViewPageActivator** -que cria a página.</span><span class="sxs-lookup"><span data-stu-id="0a065-288">A View Page activator - **CustomViewPageActivator** - that creates the page.</span></span>

<span data-ttu-id="0a065-289">Injetar **procurar** modo de exibição, você registrar o resolvedor de dependência personalizada no contêiner do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-289">To inject **Browse** View, you will now register the custom dependency resolver in the Unity container.</span></span>

1. <span data-ttu-id="0a065-290">Abra **Bootstrapper.cs** arquivo.</span><span class="sxs-lookup"><span data-stu-id="0a065-290">Open **Bootstrapper.cs** file.</span></span>
2. <span data-ttu-id="0a065-291">Registrar uma instância de **MessageService** no contêiner do Unity para inicializar o serviço:</span><span class="sxs-lookup"><span data-stu-id="0a065-291">Register an instance of **MessageService** into the Unity container to initialize the service:</span></span>

    <span data-ttu-id="0a065-292">(Código de trecho de código – *serviço de mensagem Register do laboratório - Ex02 - de injeção de dependência ASP.NET*)</span><span class="sxs-lookup"><span data-stu-id="0a065-292">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex02 - Register Message Service*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample14.cs)]
3. <span data-ttu-id="0a065-293">Adicione uma referência ao **MvcMusicStore.Factories** namespace.</span><span class="sxs-lookup"><span data-stu-id="0a065-293">Add a reference to **MvcMusicStore.Factories** namespace.</span></span>

    <span data-ttu-id="0a065-294">(Código de trecho de código – *ASP.NET dependência injeção laboratório - Ex02 - fábricas Namespace*)</span><span class="sxs-lookup"><span data-stu-id="0a065-294">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex02 - Factories Namespace*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample15.cs)]
4. <span data-ttu-id="0a065-295">Registre **CustomViewPageActivator** como um ativador de página de exibição para o contêiner do Unity:</span><span class="sxs-lookup"><span data-stu-id="0a065-295">Register **CustomViewPageActivator** as a View Page activator into the Unity container:</span></span>

    <span data-ttu-id="0a065-296">(Código de trecho de código – *ASP.NET dependência injeção laboratório - Ex02 - Register CustomViewPageActivator*)</span><span class="sxs-lookup"><span data-stu-id="0a065-296">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex02 - Register CustomViewPageActivator*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample16.cs)]
5. <span data-ttu-id="0a065-297">Substitua o resolvedor de dependência padrão ASP.NET MVC 4 com uma instância do **UnityDependencyResolver**.</span><span class="sxs-lookup"><span data-stu-id="0a065-297">Replace ASP.NET MVC 4 default dependency resolver with an instance of **UnityDependencyResolver**.</span></span> <span data-ttu-id="0a065-298">Para fazer isso, substitua **Initialise** método conteúdo com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="0a065-298">To do this, replace **Initialise** method content with the following code:</span></span>

    <span data-ttu-id="0a065-299">(Código de trecho de código – *resolvedor de dependência atualização do laboratório - Ex02 - de injeção de dependência ASP.NET*)</span><span class="sxs-lookup"><span data-stu-id="0a065-299">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex02 - Update Dependency Resolver*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample17.cs)]

    > [!NOTE]
    > <span data-ttu-id="0a065-300">ASP.NET MVC fornece uma classe do resolvedor de dependência padrão.</span><span class="sxs-lookup"><span data-stu-id="0a065-300">ASP.NET MVC provides a default dependency resolver class.</span></span> <span data-ttu-id="0a065-301">Para trabalhar com resolvedores de dependência personalizada que criamos para unity, esse resolvedor deve ser substituído.</span><span class="sxs-lookup"><span data-stu-id="0a065-301">To work with custom dependency resolvers as the one we have created for unity, this resolver has to be replaced.</span></span>

<a id="Ex2Task4"></a>

<a id="Task_4_-_Running_the_Application"></a>
#### <a name="task-4---running-the-application"></a><span data-ttu-id="0a065-302">Tarefa 4 - execução do aplicativo</span><span class="sxs-lookup"><span data-stu-id="0a065-302">Task 4 - Running the Application</span></span>

<span data-ttu-id="0a065-303">Nesta tarefa, você executará o aplicativo para verificar se o navegador Store consome o serviço e mostra a imagem e a mensagem recuperada:</span><span class="sxs-lookup"><span data-stu-id="0a065-303">In this task, you will run the application to verify that the Store Browser consumes the service and shows the image and the message retrieved:</span></span>

1. <span data-ttu-id="0a065-304">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a065-304">Press **F5** to run the application.</span></span>
2. <span data-ttu-id="0a065-305">Clique em **Rock** dentro do Menu de gêneros e veja como o **MessageService** foi inserida para o modo de exibição e carregado a mensagem de boas-vinda e a imagem.</span><span class="sxs-lookup"><span data-stu-id="0a065-305">Click **Rock** within the Genres Menu and see how the **MessageService** was injected to the view and loaded the welcome message and the image.</span></span> <span data-ttu-id="0a065-306">Neste exemplo, podemos estiver inserindo para &quot; **Rock**&quot;:</span><span class="sxs-lookup"><span data-stu-id="0a065-306">In this example, we are entering to &quot;**Rock**&quot;:</span></span>

    <span data-ttu-id="0a065-307">![Store de música do MVC - injeção de exibição](aspnet-mvc-4-dependency-injection/_static/image10.png "Store de música do MVC - injeção de exibição")</span><span class="sxs-lookup"><span data-stu-id="0a065-307">![MVC Music Store - View Injection](aspnet-mvc-4-dependency-injection/_static/image10.png "MVC Music Store - View Injection")</span></span>

    <span data-ttu-id="0a065-308">*Store de música do MVC - injeção de exibição*</span><span class="sxs-lookup"><span data-stu-id="0a065-308">*MVC Music Store - View Injection*</span></span>
3. <span data-ttu-id="0a065-309">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="0a065-309">Close the browser.</span></span>

<a id="Exercise3"></a>

<a id="Exercise_3_Injecting_Action_Filters"></a>
### <a name="exercise-3-injecting-action-filters"></a><span data-ttu-id="0a065-310">Exercício 3: Injetando filtros de ação</span><span class="sxs-lookup"><span data-stu-id="0a065-310">Exercise 3: Injecting Action Filters</span></span>

<span data-ttu-id="0a065-311">O laboratório prático anterior **filtros de ação personalizado** você trabalhou com a injeção e personalização de filtros.</span><span class="sxs-lookup"><span data-stu-id="0a065-311">In the previous Hands-On lab **Custom Action Filters** you have worked with filters customization and injection.</span></span> <span data-ttu-id="0a065-312">Neste exercício, você aprenderá como inserir filtros com injeção de dependência usando o contêiner do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-312">In this exercise, you will learn how to inject filters with Dependency Injection by using the Unity container.</span></span> <span data-ttu-id="0a065-313">Para fazer isso, você adicionará à solução Music Store um filtro de ação personalizada que será a atividade do site de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="0a065-313">To do that, you will add to the Music Store solution a custom action filter that will trace the activity of the site.</span></span>

<a id="Ex3Task1"></a>

<a id="Task_1_-_Including_the_Tracking_Filter_in_the_Solution"></a>
#### <a name="task-1---including-the-tracking-filter-in-the-solution"></a><span data-ttu-id="0a065-314">Tarefa 1 – incluindo o filtro de rastreamento na solução</span><span class="sxs-lookup"><span data-stu-id="0a065-314">Task 1 - Including the Tracking Filter in the Solution</span></span>

<span data-ttu-id="0a065-315">Nesta tarefa, você incluirá na Store a música um filtro de ação personalizada para eventos de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="0a065-315">In this task, you will include in the Music Store a custom action filter to trace events.</span></span> <span data-ttu-id="0a065-316">Como o filtro de ação personalizada conceitos já sejam tratados no laboratório anterior &quot;filtros de ação personalizado&quot;, inclua apenas a classe de filtro da pasta ativos deste laboratório e, em seguida, crie um provedor de filtro para Unity:</span><span class="sxs-lookup"><span data-stu-id="0a065-316">As custom action filter concepts are already treated in the previous Lab &quot;Custom Action Filters&quot;, you will just include the filter class from the Assets folder of this lab, and then create a Filter Provider for Unity:</span></span>

1. <span data-ttu-id="0a065-317">Abra o **começar** solução localizada na **Source\Ex03 - injeção de ação Filter\Begin** pasta.</span><span class="sxs-lookup"><span data-stu-id="0a065-317">Open the **Begin** solution located in the **Source\Ex03 - Injecting Action Filter\Begin** folder.</span></span> <span data-ttu-id="0a065-318">Caso contrário, você pode continuar usando o **final** solução obtida ao concluir o exercício anterior.</span><span class="sxs-lookup"><span data-stu-id="0a065-318">Otherwise, you might continue using the **End** solution obtained by completing the previous exercise.</span></span>

   1. <span data-ttu-id="0a065-319">Se você abriu fornecido **começar** solução, você precisará baixar alguns pacotes do NuGet ausentes antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0a065-319">If you opened the provided **Begin** solution, you will need to download some missing NuGet packages before continue.</span></span> <span data-ttu-id="0a065-320">Para fazer isso, clique o **Project** menu e selecione **Manage NuGet Packages**.</span><span class="sxs-lookup"><span data-stu-id="0a065-320">To do this, click the **Project** menu and select **Manage NuGet Packages**.</span></span>
   2. <span data-ttu-id="0a065-321">No **gerenciar pacotes NuGet** caixa de diálogo, clique em **restaurar** para baixar os pacotes ausentes.</span><span class="sxs-lookup"><span data-stu-id="0a065-321">In the **Manage NuGet Packages** dialog, click **Restore** in order to download missing packages.</span></span>
   3. <span data-ttu-id="0a065-322">Por fim, compile a solução clicando **construir** | **compilar solução**.</span><span class="sxs-lookup"><span data-stu-id="0a065-322">Finally, build the solution by clicking **Build** | **Build Solution**.</span></span>

      > [!NOTE]
      > <span data-ttu-id="0a065-323">Uma das vantagens de usar o NuGet é que você não precisa enviar todas as bibliotecas em seu projeto, reduzindo o tamanho do projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-323">One of the advantages of using NuGet is that you don't have to ship all the libraries in your project, reducing the project size.</span></span> <span data-ttu-id="0a065-324">Com o NuGet Power Tools, especificando as versões do pacote no arquivo Packages. config, você será capaz de baixar todas as bibliotecas necessárias na primeira vez em que você executar o projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-324">With NuGet Power Tools, by specifying the package versions in the Packages.config file, you will be able to download all the required libraries the first time you run the project.</span></span> <span data-ttu-id="0a065-325">É por isso você terá que executar essas etapas depois de abrir uma solução existente neste laboratório.</span><span class="sxs-lookup"><span data-stu-id="0a065-325">This is why you will have to run these steps after you open an existing solution from this lab.</span></span>
      > 
      > <span data-ttu-id="0a065-326">Para obter mais informações, consulte este artigo: [ http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages ](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages).</span><span class="sxs-lookup"><span data-stu-id="0a065-326">For more information, see this article: [http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages).</span></span>
2. <span data-ttu-id="0a065-327">Incluir **TraceActionFilter.cs** arquivo do **fontes/ativos** para **/filtra** pasta.</span><span class="sxs-lookup"><span data-stu-id="0a065-327">Include **TraceActionFilter.cs** file from **/Sources/Assets** to **/Filters** folder.</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample18.cs)]

    > [!NOTE]
    > <span data-ttu-id="0a065-328">Esse filtro de ação personalizada executa o rastreamento do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0a065-328">This custom action filter performs ASP.NET tracing.</span></span> <span data-ttu-id="0a065-329">Você pode verificar &quot;local do ASP.NET MVC 4 e filtros de ação dinâmicos&quot; laboratório para obter mais referência.</span><span class="sxs-lookup"><span data-stu-id="0a065-329">You can check &quot;ASP.NET MVC 4 local and Dynamic Action Filters&quot; Lab for more reference.</span></span>
3. <span data-ttu-id="0a065-330">Adicione a classe vazia **FilterProvider.cs** ao projeto na pasta   **/filtros.**</span><span class="sxs-lookup"><span data-stu-id="0a065-330">Add the empty class **FilterProvider.cs** to the project in the folder **/Filters.**</span></span>
4. <span data-ttu-id="0a065-331">Adicione a **System.Web.Mvc** e **Microsoft.Practices.Unity** namespaces no **FilterProvider.cs**.</span><span class="sxs-lookup"><span data-stu-id="0a065-331">Add the **System.Web.Mvc** and **Microsoft.Practices.Unity** namespaces in **FilterProvider.cs**.</span></span>

    <span data-ttu-id="0a065-332">(Código de trecho de código – *provedor ASP.NET dependência injeção laboratório - Ex03 - filtro adicionando Namespaces*)</span><span class="sxs-lookup"><span data-stu-id="0a065-332">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex03 - Filter Provider Adding Namespaces*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample19.cs)]
5. <span data-ttu-id="0a065-333">Tornar a classe herdar **IFilterProvider** Interface.</span><span class="sxs-lookup"><span data-stu-id="0a065-333">Make the class inherit from **IFilterProvider** Interface.</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample20.cs)]
6. <span data-ttu-id="0a065-334">Adicionar um **IUnityContainer** propriedade no **FilterProvider** de classe e, em seguida, crie um construtor de classe para atribuir o contêiner.</span><span class="sxs-lookup"><span data-stu-id="0a065-334">Add a **IUnityContainer** property in the **FilterProvider** class, and then create a class constructor to assign the container.</span></span>

    <span data-ttu-id="0a065-335">(Código de trecho de código – *construtor de provedor filtro do laboratório - Ex03 - de injeção de dependência ASP.NET*)</span><span class="sxs-lookup"><span data-stu-id="0a065-335">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex03 - Filter Provider Constructor*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample21.cs)]

    > [!NOTE]
    > <span data-ttu-id="0a065-336">O construtor de classe do provedor de filtro não está criando um **novo** dentro do objeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-336">The filter provider class constructor is not creating a **new** object inside.</span></span> <span data-ttu-id="0a065-337">O contêiner é passado como um parâmetro e a dependência é resolvida com o Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-337">The container is passed as a parameter, and the dependency is solved by Unity.</span></span>
7. <span data-ttu-id="0a065-338">No **FilterProvider** classe, implemente o método **GetFilters** da **IFilterProvider** interface.</span><span class="sxs-lookup"><span data-stu-id="0a065-338">In the **FilterProvider** class, implement the method **GetFilters** from **IFilterProvider** interface.</span></span>

    <span data-ttu-id="0a065-339">(Código de trecho de código – *GetFilters de provedor filtro do laboratório - Ex03 - de injeção de dependência ASP.NET*)</span><span class="sxs-lookup"><span data-stu-id="0a065-339">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex03 - Filter Provider GetFilters*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample22.cs)]

<a id="Ex3Task2"></a>

<a id="Task_2_-_Registering_and_Enabling_the_Filter"></a>
#### <a name="task-2---registering-and-enabling-the-filter"></a><span data-ttu-id="0a065-340">Tarefa 2: registrar e habilitar o filtro</span><span class="sxs-lookup"><span data-stu-id="0a065-340">Task 2 - Registering and Enabling the Filter</span></span>

<span data-ttu-id="0a065-341">Nesta tarefa, você habilitará o controle de site.</span><span class="sxs-lookup"><span data-stu-id="0a065-341">In this task, you will enable site tracking.</span></span> <span data-ttu-id="0a065-342">Para fazer isso, você registrará o filtro na **Bootstrapper.cs BuildUnityContainer** método para iniciar o rastreamento:</span><span class="sxs-lookup"><span data-stu-id="0a065-342">To do that, you will register the filter in **Bootstrapper.cs BuildUnityContainer** method to start tracing:</span></span>

1. <span data-ttu-id="0a065-343">Abra **Web. config** localizados na raiz do projeto e habilitar o acompanhamento de rastreamento no grupo de System. Web.</span><span class="sxs-lookup"><span data-stu-id="0a065-343">Open **Web.config** located in the project root and enable trace tracking at System.Web group.</span></span>

    [!code-xml[Main](aspnet-mvc-4-dependency-injection/samples/sample23.xml)]
2. <span data-ttu-id="0a065-344">Abra **Bootstrapper.cs** na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="0a065-344">Open **Bootstrapper.cs** at project root.</span></span>
3. <span data-ttu-id="0a065-345">Adicione uma referência para o **MvcMusicStore.Filters** namespace.</span><span class="sxs-lookup"><span data-stu-id="0a065-345">Add a reference to the **MvcMusicStore.Filters** namespace.</span></span>

    <span data-ttu-id="0a065-346">(Código de trecho de código – *injeção de dependência ASP.NET laboratório - Ex03 - Bootstrapper adicionando Namespaces*)</span><span class="sxs-lookup"><span data-stu-id="0a065-346">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex03 - Bootstrapper Adding Namespaces*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample24.cs)]
4. <span data-ttu-id="0a065-347">Selecione o **BuildUnityContainer** método e registre-se o filtro no contêiner do Unity.</span><span class="sxs-lookup"><span data-stu-id="0a065-347">Select the **BuildUnityContainer** method and register the filter in the Unity Container.</span></span> <span data-ttu-id="0a065-348">Você precisará registrar o provedor de filtro, bem como o filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="0a065-348">You will have to register the filter provider as well as the action filter.</span></span>

    <span data-ttu-id="0a065-349">(Código de trecho de código – *ASP.NET dependência injeção laboratório - Ex03 - Register FilterProvider e ActionFilter*)</span><span class="sxs-lookup"><span data-stu-id="0a065-349">(Code Snippet - *ASP.NET Dependency Injection Lab - Ex03 - Register FilterProvider and ActionFilter*)</span></span>

    [!code-csharp[Main](aspnet-mvc-4-dependency-injection/samples/sample25.cs)]

<a id="Ex3Task3"></a>

<a id="Task_3_-_Running_the_Application"></a>
#### <a name="task-3---running-the-application"></a><span data-ttu-id="0a065-350">Tarefa 3: executar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="0a065-350">Task 3 - Running the Application</span></span>

<span data-ttu-id="0a065-351">Nesta tarefa, você irá executar o aplicativo e que o filtro de ação personalizada está rastreando a atividade de teste:</span><span class="sxs-lookup"><span data-stu-id="0a065-351">In this task, you will run the application and test that the custom action filter is tracing the activity:</span></span>

1. <span data-ttu-id="0a065-352">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a065-352">Press **F5** to run the application.</span></span>
2. <span data-ttu-id="0a065-353">Clique em **Rock** dentro do Menu de gêneros.</span><span class="sxs-lookup"><span data-stu-id="0a065-353">Click **Rock** within the Genres Menu.</span></span> <span data-ttu-id="0a065-354">Você pode navegar até gêneros mais se você quiser.</span><span class="sxs-lookup"><span data-stu-id="0a065-354">You can browse to more genres if you want to.</span></span>

    <span data-ttu-id="0a065-355">![Store música](aspnet-mvc-4-dependency-injection/_static/image11.png "Music Store")</span><span class="sxs-lookup"><span data-stu-id="0a065-355">![Music Store](aspnet-mvc-4-dependency-injection/_static/image11.png "Music Store")</span></span>

    <span data-ttu-id="0a065-356">*Loja de Música*</span><span class="sxs-lookup"><span data-stu-id="0a065-356">*Music Store*</span></span>
3. <span data-ttu-id="0a065-357">Navegue até **/Trace.axd** para ver o rastreamento de aplicativo de página e, em seguida, clique em **exibir detalhes**.</span><span class="sxs-lookup"><span data-stu-id="0a065-357">Browse to **/Trace.axd** to see the Application Trace page, and then click **View Details**.</span></span>

    <span data-ttu-id="0a065-358">![Log de rastreamento do aplicativo](aspnet-mvc-4-dependency-injection/_static/image12.png "Log de rastreamento do aplicativo")</span><span class="sxs-lookup"><span data-stu-id="0a065-358">![Application Trace Log](aspnet-mvc-4-dependency-injection/_static/image12.png "Application Trace Log")</span></span>

    <span data-ttu-id="0a065-359">*Log de rastreamento do aplicativo*</span><span class="sxs-lookup"><span data-stu-id="0a065-359">*Application Trace Log*</span></span>

    <span data-ttu-id="0a065-360">![Rastreamento de aplicativo - detalhes da solicitação](aspnet-mvc-4-dependency-injection/_static/image13.png "rastreamento de aplicativo - detalhes da solicitação")</span><span class="sxs-lookup"><span data-stu-id="0a065-360">![Application Trace - Request Details](aspnet-mvc-4-dependency-injection/_static/image13.png "Application Trace - Request Details")</span></span>

    <span data-ttu-id="0a065-361">*Rastreamento de aplicativo - detalhes da solicitação*</span><span class="sxs-lookup"><span data-stu-id="0a065-361">*Application Trace - Request Details*</span></span>
4. <span data-ttu-id="0a065-362">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="0a065-362">Close the browser.</span></span>

* * *

<a id="Summary"></a>

<a id="Summary"></a>
## <a name="summary"></a><span data-ttu-id="0a065-363">Resumo</span><span class="sxs-lookup"><span data-stu-id="0a065-363">Summary</span></span>

<span data-ttu-id="0a065-364">Ao concluir este laboratório prático, você aprendeu a usar injeção de dependência no ASP.NET MVC 4, a integração do Unity usando um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="0a065-364">By completing this Hands-On Lab you have learned how to use Dependency Injection in ASP.NET MVC 4 by integrating Unity using a NuGet Package.</span></span> <span data-ttu-id="0a065-365">Para fazer isso, você usou a injeção de dependência dentro de controladores, exibições e filtros de ação.</span><span class="sxs-lookup"><span data-stu-id="0a065-365">To achieve that, you have used Dependency Injection inside controllers, views and action filters.</span></span>

<span data-ttu-id="0a065-366">Os conceitos a seguir foram abordados:</span><span class="sxs-lookup"><span data-stu-id="0a065-366">The following concepts were covered:</span></span>

- <span data-ttu-id="0a065-367">Recursos de injeção de dependência do ASP.NET MVC 4</span><span class="sxs-lookup"><span data-stu-id="0a065-367">ASP.NET MVC 4 Dependency Injection features</span></span>
- <span data-ttu-id="0a065-368">Integração do Unity usando o pacote do NuGet Unity.Mvc3</span><span class="sxs-lookup"><span data-stu-id="0a065-368">Unity integration using Unity.Mvc3 NuGet Package</span></span>
- <span data-ttu-id="0a065-369">Injeção de dependência em controladores</span><span class="sxs-lookup"><span data-stu-id="0a065-369">Dependency Injection in Controllers</span></span>
- <span data-ttu-id="0a065-370">Injeção de dependência em exibições</span><span class="sxs-lookup"><span data-stu-id="0a065-370">Dependency Injection in Views</span></span>
- <span data-ttu-id="0a065-371">Injeção de dependência de filtros de ação</span><span class="sxs-lookup"><span data-stu-id="0a065-371">Dependency injection of Action Filters</span></span>

<a id="AppendixA"></a>

<a id="Appendix_A_Installing_Visual_Studio_Express_2012_for_Web"></a>
## <a name="appendix-a-installing-visual-studio-express-2012-for-web"></a><span data-ttu-id="0a065-372">Apêndice a: instalação do Visual Studio Express 2012 para Web</span><span class="sxs-lookup"><span data-stu-id="0a065-372">Appendix A: Installing Visual Studio Express 2012 for Web</span></span>

<span data-ttu-id="0a065-373">Você pode instalar **Microsoft Visual Studio Express 2012 para Web** ou outra &quot;Express&quot; versão usando o **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)**.</span><span class="sxs-lookup"><span data-stu-id="0a065-373">You can install **Microsoft Visual Studio Express 2012 for Web** or another &quot;Express&quot; version using the **[Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx)**.</span></span> <span data-ttu-id="0a065-374">As instruções a seguir guia você pelas etapas necessárias para instalar *Visual studio Express 2012 para Web* usando *Microsoft Web Platform Installer*.</span><span class="sxs-lookup"><span data-stu-id="0a065-374">The following instructions guide you through the steps required to install *Visual studio Express 2012 for Web* using *Microsoft Web Platform Installer*.</span></span>

1. <span data-ttu-id="0a065-375">Vá para [ [ https://go.microsoft.com/? linkid = 9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169).</span><span class="sxs-lookup"><span data-stu-id="0a065-375">Go to [[https://go.microsoft.com/? linkid=9810169](https://go.microsoft.com/?linkid=9810169)](https://go.microsoft.com/?linkid=9810169).</span></span> <span data-ttu-id="0a065-376">Como alternativa, se você já tiver instalado o Web Platform Installer, você pode abri-lo e pesquisar o produto &quot; <em>Visual Studio Express 2012 para Web com o SDK do Windows Azure</em>&quot;.</span><span class="sxs-lookup"><span data-stu-id="0a065-376">Alternatively, if you already have installed Web Platform Installer, you can open it and search for the product &quot;<em>Visual Studio Express 2012 for Web with Windows Azure SDK</em>&quot;.</span></span>
2. <span data-ttu-id="0a065-377">Clique em **instalar agora**.</span><span class="sxs-lookup"><span data-stu-id="0a065-377">Click on **Install Now**.</span></span> <span data-ttu-id="0a065-378">Se você não tiver **Web Platform Installer** você será redirecionado para baixar e instalá-lo primeiro.</span><span class="sxs-lookup"><span data-stu-id="0a065-378">If you do not have **Web Platform Installer** you will be redirected to download and install it first.</span></span>
3. <span data-ttu-id="0a065-379">Uma vez **Web Platform Installer** é aberto, clique em **instalar** para iniciar a instalação.</span><span class="sxs-lookup"><span data-stu-id="0a065-379">Once **Web Platform Installer** is open, click **Install** to start the setup.</span></span>

    <span data-ttu-id="0a065-380">![Instalar o Visual Studio Express](aspnet-mvc-4-dependency-injection/_static/image14.png "instalar o Visual Studio Express")</span><span class="sxs-lookup"><span data-stu-id="0a065-380">![Install Visual Studio Express](aspnet-mvc-4-dependency-injection/_static/image14.png "Install Visual Studio Express")</span></span>

    <span data-ttu-id="0a065-381">*Instalar o Visual Studio Express*</span><span class="sxs-lookup"><span data-stu-id="0a065-381">*Install Visual Studio Express*</span></span>
4. <span data-ttu-id="0a065-382">Leia os termos e licenças de todos os produtos e clique em **aceito** para continuar.</span><span class="sxs-lookup"><span data-stu-id="0a065-382">Read all the products' licenses and terms and click **I Accept** to continue.</span></span>

    ![Aceitar os termos de licença](aspnet-mvc-4-dependency-injection/_static/image15.png)

    <span data-ttu-id="0a065-384">*Aceitar os termos de licença*</span><span class="sxs-lookup"><span data-stu-id="0a065-384">*Accepting the license terms*</span></span>
5. <span data-ttu-id="0a065-385">Aguarde até que o processo de download e instalação for concluído.</span><span class="sxs-lookup"><span data-stu-id="0a065-385">Wait until the downloading and installation process completes.</span></span>

    ![Progresso da instalação](aspnet-mvc-4-dependency-injection/_static/image16.png)

    <span data-ttu-id="0a065-387">*Progresso da instalação*</span><span class="sxs-lookup"><span data-stu-id="0a065-387">*Installation progress*</span></span>
6. <span data-ttu-id="0a065-388">Quando a instalação for concluída, clique em **concluir**.</span><span class="sxs-lookup"><span data-stu-id="0a065-388">When the installation completes, click **Finish**.</span></span>

    ![Instalação concluída](aspnet-mvc-4-dependency-injection/_static/image17.png)

    <span data-ttu-id="0a065-390">*Instalação concluída*</span><span class="sxs-lookup"><span data-stu-id="0a065-390">*Installation completed*</span></span>
7. <span data-ttu-id="0a065-391">Clique em **Exit** para fechar o Web Platform Installer.</span><span class="sxs-lookup"><span data-stu-id="0a065-391">Click **Exit** to close Web Platform Installer.</span></span>
8. <span data-ttu-id="0a065-392">Para abrir o Visual Studio Express para Web, vá para o **iniciar** de tela e comece a escrever &quot; **VS Express**&quot;, em seguida, clique no **VS Express para Web** lado a lado.</span><span class="sxs-lookup"><span data-stu-id="0a065-392">To open Visual Studio Express for Web, go to the **Start** screen and start writing &quot;**VS Express**&quot;, then click on the **VS Express for Web** tile.</span></span>

    ![VS Express para o bloco da Web](aspnet-mvc-4-dependency-injection/_static/image18.png)

    <span data-ttu-id="0a065-394">*VS Express para o bloco da Web*</span><span class="sxs-lookup"><span data-stu-id="0a065-394">*VS Express for Web tile*</span></span>

<a id="AppendixB"></a>

<a id="Appendix_B_Using_Code_Snippets"></a>
## <a name="appendix-b-using-code-snippets"></a><span data-ttu-id="0a065-395">Apêndice b: usar trechos de código</span><span class="sxs-lookup"><span data-stu-id="0a065-395">Appendix B: Using Code Snippets</span></span>

<span data-ttu-id="0a065-396">Com trechos de código, você tem todo o código que necessário ao seu alcance.</span><span class="sxs-lookup"><span data-stu-id="0a065-396">With code snippets, you have all the code you need at your fingertips.</span></span> <span data-ttu-id="0a065-397">O documento de laboratório lhe dirá exatamente quando você pode usá-los, conforme mostrado na figura a seguir.</span><span class="sxs-lookup"><span data-stu-id="0a065-397">The lab document will tell you exactly when you can use them, as shown in the following figure.</span></span>

<span data-ttu-id="0a065-398">![Usar trechos de código do Visual Studio para inserir código em seu projeto](aspnet-mvc-4-dependency-injection/_static/image19.png "trechos de código usando o Visual Studio para inserir código em seu projeto")</span><span class="sxs-lookup"><span data-stu-id="0a065-398">![Using Visual Studio code snippets to insert code into your project](aspnet-mvc-4-dependency-injection/_static/image19.png "Using Visual Studio code snippets to insert code into your project")</span></span>

<span data-ttu-id="0a065-399">*Usar trechos de código do Visual Studio para inserir código em seu projeto*</span><span class="sxs-lookup"><span data-stu-id="0a065-399">*Using Visual Studio code snippets to insert code into your project*</span></span>

<span data-ttu-id="0a065-400">***Para adicionar um trecho de código usando o teclado (somente c#)***</span><span class="sxs-lookup"><span data-stu-id="0a065-400">***To add a code snippet using the keyboard (C# only)***</span></span>

1. <span data-ttu-id="0a065-401">Coloque o cursor onde você deseja inserir o código.</span><span class="sxs-lookup"><span data-stu-id="0a065-401">Place the cursor where you would like to insert the code.</span></span>
2. <span data-ttu-id="0a065-402">Comece a digitar o nome do trecho de código (sem espaços ou hifens).</span><span class="sxs-lookup"><span data-stu-id="0a065-402">Start typing the snippet name (without spaces or hyphens).</span></span>
3. <span data-ttu-id="0a065-403">Assista como o IntelliSense exibe os nomes dos trechos de código correspondentes.</span><span class="sxs-lookup"><span data-stu-id="0a065-403">Watch as IntelliSense displays matching snippets' names.</span></span>
4. <span data-ttu-id="0a065-404">Selecione o trecho de código correto (ou continue digitando até que o nome do trecho toda está selecionado).</span><span class="sxs-lookup"><span data-stu-id="0a065-404">Select the correct snippet (or keep typing until the entire snippet's name is selected).</span></span>
5. <span data-ttu-id="0a065-405">Pressione a tecla Tab duas vezes para inserir o trecho de código no local do cursor.</span><span class="sxs-lookup"><span data-stu-id="0a065-405">Press the Tab key twice to insert the snippet at the cursor location.</span></span>

<span data-ttu-id="0a065-406">![Comece a digitar o nome do trecho](aspnet-mvc-4-dependency-injection/_static/image20.png "comece a digitar o nome do trecho de código")</span><span class="sxs-lookup"><span data-stu-id="0a065-406">![Start typing the snippet name](aspnet-mvc-4-dependency-injection/_static/image20.png "Start typing the snippet name")</span></span>

<span data-ttu-id="0a065-407">*Comece a digitar o nome do trecho de código*</span><span class="sxs-lookup"><span data-stu-id="0a065-407">*Start typing the snippet name*</span></span>

<span data-ttu-id="0a065-408">![Pressione Tab para selecionar o trecho de código realçado](aspnet-mvc-4-dependency-injection/_static/image21.png "pressione Tab para selecionar o trecho de código realçado")</span><span class="sxs-lookup"><span data-stu-id="0a065-408">![Press Tab to select the highlighted snippet](aspnet-mvc-4-dependency-injection/_static/image21.png "Press Tab to select the highlighted snippet")</span></span>

<span data-ttu-id="0a065-409">*Pressione Tab para selecionar o trecho de código realçado*</span><span class="sxs-lookup"><span data-stu-id="0a065-409">*Press Tab to select the highlighted snippet*</span></span>

<span data-ttu-id="0a065-410">![Pressione Tab novamente e o trecho de código serão expandido](aspnet-mvc-4-dependency-injection/_static/image22.png "pressione Tab novamente e o trecho de código serão expandido")</span><span class="sxs-lookup"><span data-stu-id="0a065-410">![Press Tab again and the snippet will expand](aspnet-mvc-4-dependency-injection/_static/image22.png "Press Tab again and the snippet will expand")</span></span>

<span data-ttu-id="0a065-411">*Pressione Tab novamente e o trecho de código serão expandido*</span><span class="sxs-lookup"><span data-stu-id="0a065-411">*Press Tab again and the snippet will expand*</span></span>

<span data-ttu-id="0a065-412">***Para adicionar um trecho de código usando o mouse (c#, Visual Basic e XML)*** 1.</span><span class="sxs-lookup"><span data-stu-id="0a065-412">***To add a code snippet using the mouse (C#, Visual Basic and XML)*** 1.</span></span> <span data-ttu-id="0a065-413">Clique com botão direito no qual você deseja inserir o trecho de código.</span><span class="sxs-lookup"><span data-stu-id="0a065-413">Right-click where you want to insert the code snippet.</span></span>

1. <span data-ttu-id="0a065-414">Selecione **Inserir trecho** seguido **Meus trechos de código**.</span><span class="sxs-lookup"><span data-stu-id="0a065-414">Select **Insert Snippet** followed by **My Code Snippets**.</span></span>
2. <span data-ttu-id="0a065-415">Selecione o trecho relevante na lista, clicando nele.</span><span class="sxs-lookup"><span data-stu-id="0a065-415">Pick the relevant snippet from the list, by clicking on it.</span></span>

<span data-ttu-id="0a065-416">![Clique com botão direito no qual você deseja inserir o trecho de código e selecione Insert Snippet](aspnet-mvc-4-dependency-injection/_static/image23.png "botão direito do mouse em que você deseja inserir o trecho de código e selecione Insert Snippet")</span><span class="sxs-lookup"><span data-stu-id="0a065-416">![Right-click where you want to insert the code snippet and select Insert Snippet](aspnet-mvc-4-dependency-injection/_static/image23.png "Right-click where you want to insert the code snippet and select Insert Snippet")</span></span>

<span data-ttu-id="0a065-417">*Clique com botão direito no qual você deseja inserir o trecho de código e selecione Insert Snippet*</span><span class="sxs-lookup"><span data-stu-id="0a065-417">*Right-click where you want to insert the code snippet and select Insert Snippet*</span></span>

<span data-ttu-id="0a065-418">![Escolher o trecho relevante na lista, clicando nele](aspnet-mvc-4-dependency-injection/_static/image24.png "escolher o trecho relevante na lista, clicando nele")</span><span class="sxs-lookup"><span data-stu-id="0a065-418">![Pick the relevant snippet from the list, by clicking on it](aspnet-mvc-4-dependency-injection/_static/image24.png "Pick the relevant snippet from the list, by clicking on it")</span></span>

<span data-ttu-id="0a065-419">*Escolher o trecho relevante na lista, clicando nele*</span><span class="sxs-lookup"><span data-stu-id="0a065-419">*Pick the relevant snippet from the list, by clicking on it*</span></span>
