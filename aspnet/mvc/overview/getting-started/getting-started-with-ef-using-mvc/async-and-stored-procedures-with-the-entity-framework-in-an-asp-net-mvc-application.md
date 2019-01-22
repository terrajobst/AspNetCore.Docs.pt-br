---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Usar async e procedimentos armazenados com o EF em um aplicativo do ASP.NET MVC'
description: Neste tutorial, você verá como implementar o modelo de programação assíncrono e saiba como usar procedimentos armazenados.
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 0896664174bc2fee65b73ecf256d994f2abacc0a
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444357"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="ba648-103">Tutorial: Usar async e procedimentos armazenados com o EF em um aplicativo do ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="ba648-103">Tutorial: Use async and stored procedures with EF in an ASP.NET MVC App</span></span>

<span data-ttu-id="ba648-104">Nos tutoriais anteriores, você aprendeu como ler e atualizar dados usando o modelo de programação síncrono.</span><span class="sxs-lookup"><span data-stu-id="ba648-104">In earlier tutorials you learned how to read and update data using the synchronous programming model.</span></span> <span data-ttu-id="ba648-105">Neste tutorial, você verá como implementar o modelo de programação assíncrono.</span><span class="sxs-lookup"><span data-stu-id="ba648-105">In this tutorial you see how to implement the asynchronous programming model.</span></span> <span data-ttu-id="ba648-106">O código assíncrono pode ajudar a um aplicativo têm melhor desempenho porque faz melhor uso dos recursos de servidor.</span><span class="sxs-lookup"><span data-stu-id="ba648-106">Asynchronous code can help an application perform better because it makes better use of server resources.</span></span>

<span data-ttu-id="ba648-107">Neste tutorial, você também verá como usar procedimentos armazenados para inserção, atualização e operações de exclusão em uma entidade.</span><span class="sxs-lookup"><span data-stu-id="ba648-107">In this tutorial you also see how to use stored procedures for insert, update, and delete operations on an entity.</span></span>

<span data-ttu-id="ba648-108">Por fim, você reimplantar o aplicativo no Azure, juntamente com todas as alterações de banco de dados que você já implementou desde a primeira vez que você implantou.</span><span class="sxs-lookup"><span data-stu-id="ba648-108">Finally, you redeploy the application to Azure, along with all of the database changes that you've implemented since the first time you deployed.</span></span>

<span data-ttu-id="ba648-109">As ilustrações a seguir mostram algumas das páginas com as quais você trabalhará.</span><span class="sxs-lookup"><span data-stu-id="ba648-109">The following illustrations show some of the pages that you'll work with.</span></span>

![Página de departamentos](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Criar departamento](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="ba648-112">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="ba648-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba648-113">Saiba mais sobre o código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ba648-113">Learn about asynchronous code</span></span>
> * <span data-ttu-id="ba648-114">Criar um controlador de departamento</span><span class="sxs-lookup"><span data-stu-id="ba648-114">Create a Department controller</span></span>
> * <span data-ttu-id="ba648-115">Use os procedimentos armazenados</span><span class="sxs-lookup"><span data-stu-id="ba648-115">Use stored procedures</span></span>
> * <span data-ttu-id="ba648-116">Implantar no Azure</span><span class="sxs-lookup"><span data-stu-id="ba648-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ba648-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="ba648-117">Prerequisites</span></span>

* [<span data-ttu-id="ba648-118">Atualização de dados relacionados</span><span class="sxs-lookup"><span data-stu-id="ba648-118">Updating Related Data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a><span data-ttu-id="ba648-119">Por que usar o código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ba648-119">Why use asynchronous code</span></span>

<span data-ttu-id="ba648-120">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="ba648-120">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ba648-121">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="ba648-121">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ba648-122">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="ba648-122">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ba648-123">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="ba648-123">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ba648-124">Como resultado, o código assíncrono permite que os recursos de servidor a ser usar com mais eficiência e o servidor está habilitado para manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="ba648-124">As a result, asynchronous code enables server resources to be use more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="ba648-125">Em versões anteriores do .NET, escrever e testar o código assíncrono eram complexo, difícil de depurar e propensa a erros.</span><span class="sxs-lookup"><span data-stu-id="ba648-125">In earlier versions of .NET, writing and testing asynchronous code was complex, error prone, and hard to debug.</span></span> <span data-ttu-id="ba648-126">É muito mais fácil do que em geral você deve escrever código assíncrono, a menos que você tenha um motivo para não escrevendo, testando e depurando código assíncrono no .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="ba648-126">In .NET 4.5, writing, testing, and debugging asynchronous code is so much easier that you should generally write asynchronous code unless you have a reason not to.</span></span> <span data-ttu-id="ba648-127">O código assíncrono introduz uma pequena quantidade de sobrecarga, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="ba648-127">Asynchronous code does introduce a small amount of overhead, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ba648-128">Para obter mais informações sobre a programação assíncrona, consulte [suporte de assíncrono do uso .NET 4.5 para evitar o bloqueio de chamadas](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span><span class="sxs-lookup"><span data-stu-id="ba648-128">For more information about asynchronous programming, see [Use .NET 4.5's async support to avoid blocking calls](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span></span>

## <a name="create-department-controller"></a><span data-ttu-id="ba648-129">Criar um controlador de departamento</span><span class="sxs-lookup"><span data-stu-id="ba648-129">Create Department controller</span></span>

<span data-ttu-id="ba648-130">Criar um controlador de departamento da mesma maneira que você fez controladores anteriores, exceto que desta vez selecione o **usar ações do controlador assíncrono** caixa de seleção.</span><span class="sxs-lookup"><span data-stu-id="ba648-130">Create a Department controller the same way you did the earlier controllers, except this time select the **Use async controller actions** check box.</span></span>

<span data-ttu-id="ba648-131">Os seguintes destaques mostram o que foi adicionado para o código síncrono para o `Index` método para torná-la assíncrona:</span><span class="sxs-lookup"><span data-stu-id="ba648-131">The following highlights show what was added to the synchronous code for the `Index` method to make it asynchronous:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

<span data-ttu-id="ba648-132">Quatro alterações foram aplicadas para habilitar a consulta de banco de dados do Entity Framework executar de forma assíncrona:</span><span class="sxs-lookup"><span data-stu-id="ba648-132">Four changes were applied to enable the Entity Framework database query to execute asynchronously:</span></span>

- <span data-ttu-id="ba648-133">O método é marcado com o `async` palavra-chave, que informa ao compilador para gerar retornos de chamada para partes do corpo do método e para criar automaticamente o `Task<ActionResult>` objeto que é retornado.</span><span class="sxs-lookup"><span data-stu-id="ba648-133">The method is marked with the `async` keyword, which tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<ActionResult>` object that is returned.</span></span>
- <span data-ttu-id="ba648-134">O tipo de retorno foi alterado de `ActionResult` para `Task<ActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="ba648-134">The return type was changed from `ActionResult` to `Task<ActionResult>`.</span></span> <span data-ttu-id="ba648-135">O `Task<T>` tipo representa um trabalho em andamento com um resultado do tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="ba648-135">The `Task<T>` type represents ongoing work with a result of type `T`.</span></span>
- <span data-ttu-id="ba648-136">O `await` palavra-chave foi aplicada para a chamada de serviço web.</span><span class="sxs-lookup"><span data-stu-id="ba648-136">The `await` keyword was applied to the web service call.</span></span> <span data-ttu-id="ba648-137">Quando o compilador vê essa palavra-chave, nos bastidores ele divide o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="ba648-137">When the compiler sees this keyword, behind the scenes it splits the method into two parts.</span></span> <span data-ttu-id="ba648-138">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ba648-138">The first part ends with the operation that is started asynchronously.</span></span> <span data-ttu-id="ba648-139">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação for concluída.</span><span class="sxs-lookup"><span data-stu-id="ba648-139">The second part is put into a callback method that is called when the operation completes.</span></span>
- <span data-ttu-id="ba648-140">A versão assíncrona do `ToList` método de extensão foi chamado.</span><span class="sxs-lookup"><span data-stu-id="ba648-140">The asynchronous version of the `ToList` extension method was called.</span></span>

<span data-ttu-id="ba648-141">Por que é o `departments.ToList` instrução foi modificado, mas não o `departments = db.Departments` instrução?</span><span class="sxs-lookup"><span data-stu-id="ba648-141">Why is the `departments.ToList` statement modified but not the `departments = db.Departments` statement?</span></span> <span data-ttu-id="ba648-142">O motivo é que apenas as instruções que fazem com que consultas ou comandos sejam enviados para o banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ba648-142">The reason is that only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ba648-143">O `departments = db.Departments` instrução define uma consulta, mas a consulta não é executada até que o `ToList` método é chamado.</span><span class="sxs-lookup"><span data-stu-id="ba648-143">The `departments = db.Departments` statement sets up a query but the query is not executed until the `ToList` method is called.</span></span> <span data-ttu-id="ba648-144">Portanto, apenas o `ToList` método é executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ba648-144">Therefore, only the `ToList` method is executed asynchronously.</span></span>

<span data-ttu-id="ba648-145">No `Details` método e o `HttpGet` `Edit` e `Delete` métodos, o `Find` método é o que faz com que uma consulta a ser enviado ao banco de dados, portanto, que é o método que é executado de forma assíncrona:</span><span class="sxs-lookup"><span data-stu-id="ba648-145">In the `Details` method and the `HttpGet` `Edit` and `Delete` methods, the `Find` method is the one that causes a query to be sent to the database, so that's the method that gets executed asynchronously:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

<span data-ttu-id="ba648-146">No `Create`, `HttpPost Edit`, e `DeleteConfirmed` métodos, ele é o `SaveChanges` chamada de método que faz com que um comando a ser executado, não instruções como `db.Departments.Add(department)` que apenas fazer com que entidades na memória a ser modificado.</span><span class="sxs-lookup"><span data-stu-id="ba648-146">In the `Create`, `HttpPost Edit`, and `DeleteConfirmed` methods, it is the `SaveChanges` method call that causes a command to be executed, not statements such as `db.Departments.Add(department)` which only cause entities in memory to be modified.</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

<span data-ttu-id="ba648-147">Abra *Views\Department\Index.cshtml*e substitua o código de modelo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="ba648-147">Open *Views\Department\Index.cshtml*, and replace the template code with the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

<span data-ttu-id="ba648-148">Esse código altera o título do índice para departamentos, move o nome do administrador para a direita e fornece o nome completo do administrador.</span><span class="sxs-lookup"><span data-stu-id="ba648-148">This code changes the title from Index to Departments, moves the Administrator name to the right, and provides the full name of the administrator.</span></span>

<span data-ttu-id="ba648-149">Em criar, excluir, detalhes e editar modos de exibição, alterar a legenda para o `InstructorID` campo para "Administrator" da mesma forma que você alterou o campo de nome de departamento para "Departamento" nas exibições do curso.</span><span class="sxs-lookup"><span data-stu-id="ba648-149">In the Create, Delete, Details, and Edit views, change the caption for the `InstructorID` field to "Administrator" the same way you changed the department name field to "Department" in the Course views.</span></span>

<span data-ttu-id="ba648-150">Na criar e editar modos de exibição usam o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ba648-150">In the Create and Edit views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

<span data-ttu-id="ba648-151">Nas exibições de excluir e detalhes, use o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ba648-151">In the Delete and Details views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

<span data-ttu-id="ba648-152">Executar o aplicativo e, em seguida, clique no **departamentos** guia.</span><span class="sxs-lookup"><span data-stu-id="ba648-152">Run the application, and click the **Departments** tab.</span></span>

<span data-ttu-id="ba648-153">Tudo o que funciona da mesma maneira os outros controladores, mas neste controlador todas as consultas SQL estão em execução assíncrona.</span><span class="sxs-lookup"><span data-stu-id="ba648-153">Everything works the same as in the other controllers, but in this controller all of the SQL queries are executing asynchronously.</span></span>

<span data-ttu-id="ba648-154">Algumas coisas a serem consideradas quando você estiver usando a programação assíncrona com o Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="ba648-154">Some things to be aware of when you are using asynchronous programming with the Entity Framework:</span></span>

- <span data-ttu-id="ba648-155">O código assíncrono não é thread-safe.</span><span class="sxs-lookup"><span data-stu-id="ba648-155">The async code is not thread safe.</span></span> <span data-ttu-id="ba648-156">Em outras palavras, em outras palavras, não tente realizar várias operações em paralelo usando a mesma instância de contexto.</span><span class="sxs-lookup"><span data-stu-id="ba648-156">In other words, in other words, don't try to do multiple operations in parallel using the same context instance.</span></span>
- <span data-ttu-id="ba648-157">Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ba648-157">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

## <a name="use-stored-procedures"></a><span data-ttu-id="ba648-158">Use os procedimentos armazenados</span><span class="sxs-lookup"><span data-stu-id="ba648-158">Use stored procedures</span></span>

<span data-ttu-id="ba648-159">Alguns desenvolvedores e DBAs preferem usar os procedimentos armazenados para acesso de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ba648-159">Some developers and DBAs prefer to use stored procedures for database access.</span></span> <span data-ttu-id="ba648-160">Em versões anteriores do Entity Framework, você pode recuperar dados usando um procedimento armazenado pelo [executando uma consulta SQL bruta](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), mas não é possível instruir o EF para usar procedimentos armazenados para operações de atualização.</span><span class="sxs-lookup"><span data-stu-id="ba648-160">In earlier versions of Entity Framework you can retrieve data using a stored procedure by [executing a raw SQL query](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), but you can't instruct EF to use stored procedures for update operations.</span></span> <span data-ttu-id="ba648-161">No EF 6 é fácil de configurar o Code First para usar procedimentos armazenados.</span><span class="sxs-lookup"><span data-stu-id="ba648-161">In EF 6 it's easy to configure Code First to use stored procedures.</span></span>

1. <span data-ttu-id="ba648-162">Na *DAL\SchoolContext.cs*, adicione o código realçado para o `OnModelCreating` método.</span><span class="sxs-lookup"><span data-stu-id="ba648-162">In *DAL\SchoolContext.cs*, add the highlighted code to the `OnModelCreating` method.</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    <span data-ttu-id="ba648-163">Este código instrui o Entity Framework para procedimentos armazenado de uso para inserção, atualizar e excluir operações o `Department` entidade.</span><span class="sxs-lookup"><span data-stu-id="ba648-163">This code instructs Entity Framework to use stored procedures for insert, update, and delete operations on the `Department` entity.</span></span>
2. <span data-ttu-id="ba648-164">No Console de gerenciamento de pacote, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ba648-164">In Package Manage Console, enter the following command:</span></span>

    `add-migration DepartmentSP`

    <span data-ttu-id="ba648-165">Abra *migrações\&lt; carimbo de hora&gt;\_DepartmentSP.cs* para ver o código no `Up` método que cria Insert, Update e Delete de procedimentos armazenados:</span><span class="sxs-lookup"><span data-stu-id="ba648-165">Open *Migrations\&lt;timestamp&gt;\_DepartmentSP.cs* to see the code in the `Up` method that creates Insert, Update, and Delete stored procedures:</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. <span data-ttu-id="ba648-166">No Console de gerenciamento de pacote, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="ba648-166">In Package Manage Console, enter the following command:</span></span>

     `update-database`
4. <span data-ttu-id="ba648-167">Executar o aplicativo no modo de depuração, clique o **departamentos** guia e, em seguida, clique em **criar novo**.</span><span class="sxs-lookup"><span data-stu-id="ba648-167">Run the application in debug mode, click the **Departments** tab, and then click **Create New**.</span></span>
5. <span data-ttu-id="ba648-168">Insira dados para outro departamento e, em seguida, clique em **criar**.</span><span class="sxs-lookup"><span data-stu-id="ba648-168">Enter data for a new department, and then click **Create**.</span></span>

6. <span data-ttu-id="ba648-169">No Visual Studio, examine os logs na **saída** janela para ver se um procedimento armazenado foi usado para inserir a nova linha de departamento.</span><span class="sxs-lookup"><span data-stu-id="ba648-169">In Visual Studio, look at the logs in the **Output** window to see that a stored procedure was used to insert the new Department row.</span></span>

     ![SP de inserção do departamento](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

<span data-ttu-id="ba648-171">Primeiro, o código cria nomes de procedimento armazenado de padrão.</span><span class="sxs-lookup"><span data-stu-id="ba648-171">Code First creates default stored procedure names.</span></span> <span data-ttu-id="ba648-172">Se você estiver usando um banco de dados existente, talvez você precise personalizar os nomes de procedimento armazenado para usar procedimentos armazenados já definidos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ba648-172">If you are using an existing database, you might need to customize the stored procedure names in order to use stored procedures already defined in the database.</span></span> <span data-ttu-id="ba648-173">Para obter informações sobre como fazer isso, consulte [Entity Framework código primeiro inserir/atualizar/excluir procedimentos armazenados](https://msdn.microsoft.com/data/dn468673).</span><span class="sxs-lookup"><span data-stu-id="ba648-173">For information about how to do that, see [Entity Framework Code First Insert/Update/Delete Stored Procedures](https://msdn.microsoft.com/data/dn468673).</span></span>

<span data-ttu-id="ba648-174">Se você quiser personalizar quais gerados procedimentos armazenados, você pode editar o código gerado automaticamente para as migrações `Up` método que cria o procedimento armazenado.</span><span class="sxs-lookup"><span data-stu-id="ba648-174">If you want to customize what generated stored procedures do, you can edit the scaffolded code for the migrations `Up` method that creates the stored procedure.</span></span> <span data-ttu-id="ba648-175">Dessa forma, suas alterações serão refletidas sempre que a migração é executada e será aplicada ao banco de dados de produção quando as migrações é executado automaticamente em produção após a implantação.</span><span class="sxs-lookup"><span data-stu-id="ba648-175">That way your changes are reflected whenever that migration is run and will be applied to your production database when migrations runs automatically in production after deployment.</span></span>

<span data-ttu-id="ba648-176">Se você quiser alterar um procedimento armazenado existente que foi criado em uma migração anterior, você pode usar o comando Add-Migration para gerar uma migração em branco e, em seguida, escrever manualmente o código que chama o [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) método .</span><span class="sxs-lookup"><span data-stu-id="ba648-176">If you want to change an existing stored procedure that was created in a previous migration, you can use the Add-Migration command to generate a blank migration, and then manually write code that calls the [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) method.</span></span>

## <a name="deploy-to-azure"></a><span data-ttu-id="ba648-177">Implantar no Azure</span><span class="sxs-lookup"><span data-stu-id="ba648-177">Deploy to Azure</span></span>

<span data-ttu-id="ba648-178">Esta seção requer que você concluiu a opcional **Implantando o aplicativo do Azure** seção o [migrações e implantação](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial desta série.</span><span class="sxs-lookup"><span data-stu-id="ba648-178">This section requires you to have completed the optional **Deploying the app to Azure** section in the [Migrations and Deployment](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial of this series.</span></span> <span data-ttu-id="ba648-179">Se você tiver erros de migrações que resolvido excluindo o banco de dados em seu projeto local, ignore esta seção.</span><span class="sxs-lookup"><span data-stu-id="ba648-179">If you had migrations errors that you resolved by deleting the database in your local project, skip this section.</span></span>

1. <span data-ttu-id="ba648-180">No Visual Studio, clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="ba648-180">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>
2. <span data-ttu-id="ba648-181">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="ba648-181">Click **Publish**.</span></span>

    <span data-ttu-id="ba648-182">O Visual Studio implanta o aplicativo no Azure e o aplicativo é aberto no navegador padrão, em execução no Azure.</span><span class="sxs-lookup"><span data-stu-id="ba648-182">Visual Studio deploys the application to Azure, and the application opens in your default browser, running in Azure.</span></span>
3. <span data-ttu-id="ba648-183">Testar o aplicativo para verificar se ele está funcionando.</span><span class="sxs-lookup"><span data-stu-id="ba648-183">Test the application to verify it's working.</span></span>

    <span data-ttu-id="ba648-184">Na primeira vez que você executa uma página que acessa o banco de dados, o Entity Framework é executado todas as migrações `Up` métodos necessários para colocar o banco de dados atualizado com o modelo de dados atual.</span><span class="sxs-lookup"><span data-stu-id="ba648-184">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span> <span data-ttu-id="ba648-185">Agora você pode usar todas as páginas da web que você adicionou desde a última vez implantado, incluindo as páginas de departamento que você adicionou neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="ba648-185">You can now use all of the web pages that you added since the last time you deployed, including the Department pages that you added in this tutorial.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="ba648-186">Obter o código</span><span class="sxs-lookup"><span data-stu-id="ba648-186">Get the code</span></span>

[<span data-ttu-id="ba648-187">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="ba648-187">Download the Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a><span data-ttu-id="ba648-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ba648-188">Additional resources</span></span>

<span data-ttu-id="ba648-189">Links para outros recursos do Entity Framework podem ser encontradas na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="ba648-189">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ba648-190">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="ba648-190">Next steps</span></span>

<span data-ttu-id="ba648-191">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="ba648-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ba648-192">Aprendeu sobre o código assíncrono</span><span class="sxs-lookup"><span data-stu-id="ba648-192">Learned about asynchronous code</span></span>
> * <span data-ttu-id="ba648-193">Criou um controlador de departamento</span><span class="sxs-lookup"><span data-stu-id="ba648-193">Created a Department controller</span></span>
> * <span data-ttu-id="ba648-194">Procedimentos armazenados usados</span><span class="sxs-lookup"><span data-stu-id="ba648-194">Used stored procedures</span></span>
> * <span data-ttu-id="ba648-195">Implantado no Azure</span><span class="sxs-lookup"><span data-stu-id="ba648-195">Deployed to Azure</span></span>

<span data-ttu-id="ba648-196">Avance para o próximo artigo para aprender a lidar com conflitos quando vários usuários atualizam a mesma entidade ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="ba648-196">Advance to the next article to learn how to handle conflicts when multiple users update the same entity at the same time.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="ba648-197">Tratamento de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="ba648-197">Handling concurrency</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
