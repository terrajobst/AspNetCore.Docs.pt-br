---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Modelo: Implementar a herança com o EF em um aplicativos ASP.NET MVC 5'
description: Este tutorial mostrará como implementar a herança no modelo de dados.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: 08834147-77ec-454a-bb7a-d931d2a40dab
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: df8715e4416ce3ccdf1d9e380addcded553d85f8
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444279"
---
# <a name="template-implement-inheritance-with-ef-in-an-aspnet-mvc-5-app"></a><span data-ttu-id="e6eb0-103">Modelo: Implementar a herança com o EF em um aplicativo ASP.NET MVC 5</span><span class="sxs-lookup"><span data-stu-id="e6eb0-103">Template: Implement Inheritance with EF in an ASP.NET MVC 5 app</span></span>

<span data-ttu-id="e6eb0-104">No tutorial anterior, você tratou exceções de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-104">In the previous tutorial you handled concurrency exceptions.</span></span> <span data-ttu-id="e6eb0-105">Este tutorial mostrará como implementar a herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="e6eb0-106">Na programação orientada a objeto, você pode usar [herança](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) para facilitar [reutilização de código](http://en.wikipedia.org/wiki/Code_reuse).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-106">In object-oriented programming, you can use [inheritance](http://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming)) to facilitate [code reuse](http://en.wikipedia.org/wiki/Code_reuse).</span></span> <span data-ttu-id="e6eb0-107">Neste tutorial, você alterará as classes `Instructor` e `Student`, de modo que elas derivem de uma classe base `Person` que contém propriedades, como `LastName`, comuns a instrutores e alunos.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="e6eb0-108">Você não adicionará nem alterará as páginas da Web, mas alterará uma parte do código, e essas alterações serão refletidas automaticamente no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="e6eb0-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6eb0-110">Saiba como mapear herança para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6eb0-110">Learn to map inheritance to database</span></span>
> * <span data-ttu-id="e6eb0-111">Criar a classe Person</span><span class="sxs-lookup"><span data-stu-id="e6eb0-111">Create the Person class</span></span>
> * <span data-ttu-id="e6eb0-112">Aluno e instrutor de atualização</span><span class="sxs-lookup"><span data-stu-id="e6eb0-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="e6eb0-113">Adicionar pessoa ao modelo</span><span class="sxs-lookup"><span data-stu-id="e6eb0-113">Add Person to the Model</span></span>
> * <span data-ttu-id="e6eb0-114">Criar e atualizar as migrações</span><span class="sxs-lookup"><span data-stu-id="e6eb0-114">Create and Update Migrations</span></span>
> * <span data-ttu-id="e6eb0-115">Testar a implementação</span><span class="sxs-lookup"><span data-stu-id="e6eb0-115">Test the implementation</span></span>
> * <span data-ttu-id="e6eb0-116">Implantar no Azure</span><span class="sxs-lookup"><span data-stu-id="e6eb0-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e6eb0-117">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e6eb0-117">Prerequisites</span></span>

* [<span data-ttu-id="e6eb0-118">Implementação de herança</span><span class="sxs-lookup"><span data-stu-id="e6eb0-118">Implementing Inheritance</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="e6eb0-119">Mapear herança para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6eb0-119">Map inheritance to database</span></span>

<span data-ttu-id="e6eb0-120">O `Instructor` e `Student` as classes no `School` modelo de dados têm várias propriedades que são idênticas:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-120">The `Instructor` and `Student` classes in the `School` data model have several properties that are identical:</span></span>

![Student_and_Instructor_classes](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

<span data-ttu-id="e6eb0-122">Suponha que você deseje eliminar o código redundante para as propriedades compartilhadas pelas entidades `Instructor` e `Student`.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-122">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="e6eb0-123">Ou você deseja escrever um serviço que pode formatar nomes sem se preocupar se o nome foi obtido de um instrutor ou um aluno.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-123">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="e6eb0-124">Você pode criar uma `Person` classe que contém apenas essas propriedades compartilhadas base, em seguida, fazer a `Instructor` e `Student` entidades herdam dessa classe base, conforme mostrado na ilustração a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-124">You could create a `Person` base class which contains only those shared properties, then make the `Instructor` and `Student` entities inherit from that base class, as shown in the following illustration:</span></span>

![Student_and_Instructor_classes_deriving_from_Person_class](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="e6eb0-126">Há várias maneiras pelas quais essa estrutura de herança pode ser representada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-126">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="e6eb0-127">Você poderia ter um `Person` tabela que inclui informações sobre os alunos e instrutores em uma única tabela.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-127">You could have a `Person` table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="e6eb0-128">Algumas das colunas pode aplicar somente a instrutores (`HireDate`), algumas somente a alunos (`EnrollmentDate`), alguns para ambos (`LastName`, `FirstName`).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-128">Some of the columns could apply only to instructors (`HireDate`), some only to students (`EnrollmentDate`), some to both (`LastName`, `FirstName`).</span></span> <span data-ttu-id="e6eb0-129">Normalmente, você teria uma *discriminador* coluna para indicar qual tipo cada linha representa.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-129">Typically, you'd have a *discriminator* column to indicate which type each row represents.</span></span> <span data-ttu-id="e6eb0-130">Por exemplo, a coluna discriminatória pode ter "Instrutor" para instrutores e "Aluno" para alunos.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-130">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Table-per-hierarchy_example](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

<span data-ttu-id="e6eb0-132">Esse padrão de geração de uma estrutura de herança de entidade de uma tabela de banco de dados individual é chamado *tabela por hierarquia* herança (TPH).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-132">This pattern of generating an entity inheritance structure from a single database table is called *table-per-hierarchy* (TPH) inheritance.</span></span>

<span data-ttu-id="e6eb0-133">Uma alternativa é fazer com que o banco de dados se pareça mais com a estrutura de herança.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-133">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="e6eb0-134">Por exemplo, você poderia ter apenas os campos de nome `Person` de tabela e ter separado `Instructor` e `Student` tabelas com os campos de data.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-134">For example, you could have only the name fields in the `Person` table and have separate `Instructor` and `Student` tables with the date fields.</span></span>

![Table-per-type_inheritance](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

<span data-ttu-id="e6eb0-136">Esse padrão de criação de uma tabela de banco de dados para cada classe de entidade é chamada *tabela por tipo* herança (TPT).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-136">This pattern of making a database table for each entity class is called *table per type* (TPT) inheritance.</span></span>

<span data-ttu-id="e6eb0-137">Outra opção é mapear todos os tipos não abstratos para tabelas individuais.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="e6eb0-138">Todas as propriedades de uma classe, incluindo propriedades herdadas, são mapeadas para colunas da tabela correspondente.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="e6eb0-139">Esse padrão é chamado de herança TPC (Tabela por Classe Concreta).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="e6eb0-140">Se você tiver implementado a herança TPC para o `Person`, `Student`, e `Instructor` classes como mostrado anteriormente, o `Student` e `Instructor` tabelas não parecerão diferentes após a implementação de herança do que antes.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-140">If you implemented TPC inheritance for the `Person`, `Student`, and `Instructor` classes as shown earlier, the `Student` and `Instructor` tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="e6eb0-141">TPC e padrões de herança TPH geralmente oferecem melhor desempenho no Entity Framework que os padrões de herança TPT, porque os padrões TPT podem resultar em consultas de junção complexas.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-141">TPC and TPH inheritance patterns generally deliver better performance in the Entity Framework than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="e6eb0-142">Este tutorial demonstra como implementar a herança TPH.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="e6eb0-143">TPH é o padrão de herança no Entity Framework, portanto, tudo o que você precisa fazer é criar uma `Person` classe, altere o `Instructor` e `Student` classes sejam derivadas de `Person`, adicione a nova classe para o `DbContext`e criar um migração.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-143">TPH is the default inheritance pattern in the Entity Framework, so all you have to do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span> <span data-ttu-id="e6eb0-144">(Para obter informações sobre como implementar padrões de herança, consulte [mapeando a herança de tabela por tipo (TPT)](https://msdn.microsoft.com/data/jj591617#2.5) e [mapeando a herança de classe de tabela por concreto (TPC)](https://msdn.microsoft.com/data/jj591617#2.6) no MSDN Documentação do Entity Framework.)</span><span class="sxs-lookup"><span data-stu-id="e6eb0-144">(For information about how to implement the other inheritance patterns, see [Mapping the Table-Per-Type (TPT) Inheritance](https://msdn.microsoft.com/data/jj591617#2.5) and [Mapping the Table-Per-Concrete Class (TPC) Inheritance](https://msdn.microsoft.com/data/jj591617#2.6) in the MSDN Entity Framework documentation.)</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="e6eb0-145">Criar a classe Person</span><span class="sxs-lookup"><span data-stu-id="e6eb0-145">Create the Person class</span></span>

<span data-ttu-id="e6eb0-146">No *modelos* pasta, crie *Person.cs* e substitua o código de modelo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-146">In the *Models* folder, create *Person.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="e6eb0-147">Aluno e instrutor de atualização</span><span class="sxs-lookup"><span data-stu-id="e6eb0-147">Update Instructor and Student</span></span>

<span data-ttu-id="e6eb0-148">Atualize agora o *Instructor.cs* e *Sudent.cs* herdar valores da *Person.sc*.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-148">Now update the *Instructor.cs* and *Sudent.cs* to inherit values from the *Person.sc*.</span></span>

<span data-ttu-id="e6eb0-149">Na *Instructor.cs*, derivar o `Instructor` classe o `Person` de classe e remova os campos nomes e chaves.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-149">In *Instructor.cs*, derive the `Instructor` class from the `Person` class and remove the key and name fields.</span></span> <span data-ttu-id="e6eb0-150">O código será semelhante ao seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-150">The code will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="e6eb0-151">Fazer alterações semelhantes ao *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-151">Make similar changes to *Student.cs*.</span></span> <span data-ttu-id="e6eb0-152">O `Student` classe se parecerá com o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-152">The `Student` class will look like the following example:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="e6eb0-153">Adicionar pessoa ao modelo</span><span class="sxs-lookup"><span data-stu-id="e6eb0-153">Add Person to the Model</span></span>

<span data-ttu-id="e6eb0-154">Na *SchoolContext.cs*, adicione uma `DbSet` propriedade para o `Person` tipo de entidade:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-154">In *SchoolContext.cs*, add a `DbSet` property for the `Person` entity type:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

<span data-ttu-id="e6eb0-155">Isso é tudo o que o Entity Framework precisa para configurar a herança de tabela por hierarquia.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="e6eb0-156">Como você verá, quando o banco de dados é atualizado, ele terá um `Person` de tabela em vez do `Student` e `Instructor` tabelas.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-156">As you'll see, when the database is updated, it will have a `Person` table in place of the `Student` and `Instructor` tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="e6eb0-157">Criar e atualizar as migrações</span><span class="sxs-lookup"><span data-stu-id="e6eb0-157">Create and Update Migrations</span></span>

<span data-ttu-id="e6eb0-158">No pacote Manager Console (PMC), digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-158">In the Package Manager Console (PMC), enter the following command:</span></span>

`Add-Migration Inheritance`

<span data-ttu-id="e6eb0-159">Execute o `Update-Database` comando no PMC.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-159">Run the `Update-Database` command in the PMC.</span></span> <span data-ttu-id="e6eb0-160">O comando falhará neste momento porque temos os dados existentes que as migrações não sabe como tratar.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-160">The command will fail at this point because we have existing data that migrations doesn't know how to handle.</span></span> <span data-ttu-id="e6eb0-161">Você receberá uma mensagem de erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-161">You get an error message like the following one:</span></span>

> <span data-ttu-id="e6eb0-162">*Não foi possível descartar o objeto ' dbo. Instrutor ' porque ele é referenciado por uma restrição FOREIGN KEY.*</span><span class="sxs-lookup"><span data-stu-id="e6eb0-162">*Could not drop object 'dbo.Instructor' because it is referenced by a FOREIGN KEY constraint.*</span></span>


<span data-ttu-id="e6eb0-163">Abra *migrações\&lt; carimbo de hora&gt;\_Inheritance.cs* e substitua o `Up` método com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-163">Open *Migrations\&lt;timestamp&gt;\_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

<span data-ttu-id="e6eb0-164">Este código é responsável pelas seguintes tarefas de atualização de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-164">This code takes care of the following database update tasks:</span></span>

- <span data-ttu-id="e6eb0-165">Remove as restrições de chave estrangeira e índices que apontam para a tabela Aluno.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>
- <span data-ttu-id="e6eb0-166">Renomeia a tabela Instrutor como Pessoa e faz as alterações necessárias para que ela armazene dados de Aluno:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

    - <span data-ttu-id="e6eb0-167">Adiciona EnrollmentDate que permite valo nulo para os alunos.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-167">Adds nullable EnrollmentDate for students.</span></span>
    - <span data-ttu-id="e6eb0-168">Adiciona a coluna Discriminatória para indicar se uma linha refere-se a um aluno ou um instrutor.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>
    - <span data-ttu-id="e6eb0-169">Faz com que HireDate permita valor nulo, pois linhas de alunos não terão datas de contratação.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>
    - <span data-ttu-id="e6eb0-170">Adiciona um campo temporário que será usado para atualizar chaves estrangeiras que apontam para alunos.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="e6eb0-171">Quando você copia os alunos para a tabela Person que receberão novos valores de chave primários.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-171">When you copy students into the Person table they'll get new primary key values.</span></span>
- <span data-ttu-id="e6eb0-172">Copia os dados da tabela Aluno para a tabela Pessoa.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="e6eb0-173">Isso faz com que os alunos recebam novos valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-173">This causes students to get assigned new primary key values.</span></span>
- <span data-ttu-id="e6eb0-174">Corrige valores de chave estrangeira que apontam para alunos.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-174">Fixes foreign key values that point to students.</span></span>
- <span data-ttu-id="e6eb0-175">Recria restrições de chave estrangeira e índices, agora apontando-os para a tabela Person.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="e6eb0-176">(Se você tiver usado o GUID, em vez de inteiro como o tipo de chave primária, os valores de chave primária dos alunos não precisarão ser alterados e várias dessas etapas poderão ser omitidas.)</span><span class="sxs-lookup"><span data-stu-id="e6eb0-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="e6eb0-177">Execute o `update-database` comando novamente.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-177">Run the `update-database` command again.</span></span>

<span data-ttu-id="e6eb0-178">(Em um sistema de produção você fará as alterações correspondentes para o método de busca, caso você teve de usá-lo para voltar para a versão anterior do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-178">(In a production system you would make corresponding changes to the Down method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="e6eb0-179">Para este tutorial você não usará o método de busca.)</span><span class="sxs-lookup"><span data-stu-id="e6eb0-179">For this tutorial you won't be using the Down method.)</span></span>

> [!NOTE]
> <span data-ttu-id="e6eb0-180">É possível receber outros erros durante a migração de dados e fazer alterações de esquema.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-180">It's possible to get other errors when migrating data and making schema changes.</span></span> <span data-ttu-id="e6eb0-181">Se você obtiver erros de migração não é possível resolver, você pode continuar com o tutorial, alterando a cadeia de conexão na *Web. config* de arquivos ou excluindo o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-181">If you get migration errors you can't resolve, you can continue with the tutorial by changing the connection string in the *Web.config* file or by deleting the database.</span></span> <span data-ttu-id="e6eb0-182">A abordagem mais simples é renomear o banco de dados do *Web. config* arquivo.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-182">The simplest approach is to rename the database in the *Web.config* file.</span></span> <span data-ttu-id="e6eb0-183">Por exemplo, altere o nome do banco de dados para ContosoUniversity2, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-183">For example, change the database name to ContosoUniversity2 as shown in the following example:</span></span>
>
> [!code-xml[Main](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.xml?highlight=2)]
>
> <span data-ttu-id="e6eb0-184">Com um novo banco de dados, não há nenhum dado para migrar e o `update-database` comando é muito mais provável de ser concluído sem erros.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-184">With a new database, there is no data to migrate, and the `update-database` command is much more likely to complete without errors.</span></span> <span data-ttu-id="e6eb0-185">Para obter instruções sobre como excluir o banco de dados, consulte [como descartar um banco de dados do Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-185">For instructions on how to delete the database, see [How to Drop a Database from Visual Studio 2012](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/).</span></span> <span data-ttu-id="e6eb0-186">Se você usar essa abordagem para continuar com o tutorial, ignore a etapa de implantação no final deste tutorial ou implantar um novo site e o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-186">If you take this approach in order to continue with the tutorial, skip the deployment step at the end of this tutorial or deploy to a new site and database.</span></span> <span data-ttu-id="e6eb0-187">Se você implantar uma atualização ao mesmo site que já foi implantado já, o EF obterá o mesmo erro existe quando ele é automaticamente executado as migrações.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-187">If you deploy an update to the same site you've been deploying to already, EF will get the same error there when it runs migrations automatically.</span></span> <span data-ttu-id="e6eb0-188">Se você quiser solucionar o erro migrações, o melhor recurso é um dos fóruns do Entity Framework ou StackOverflow.com.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-188">If you want to troubleshoot a migrations error, the best resource is one of the Entity Framework forums or StackOverflow.com.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="e6eb0-189">Testar a implementação</span><span class="sxs-lookup"><span data-stu-id="e6eb0-189">Test the implementation</span></span>

<span data-ttu-id="e6eb0-190">Executar o site e tente várias páginas.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-190">Run the site and try various pages.</span></span> <span data-ttu-id="e6eb0-191">Tudo funciona da mesma maneira que antes.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-191">Everything works the same as it did before.</span></span>

<span data-ttu-id="e6eb0-192">No **Gerenciador de servidores** expanda **dados Connections\SchoolContext** e, em seguida, **tabelas**, e você verá que o **aluno** e **Instrutor** tabelas foram substituídas por um **pessoa** tabela.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-192">In **Server Explorer,** expand **Data Connections\SchoolContext** and then **Tables**, and you see that the **Student** and **Instructor** tables have been replaced by a **Person** table.</span></span> <span data-ttu-id="e6eb0-193">Expanda o **pessoa** tabela e você ver que ele tem todas as colunas que costumavam estar na **aluno** e **instrutor** tabelas.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-193">Expand the **Person** table and you see that it has all of the columns that used to be in the **Student** and **Instructor** tables.</span></span>

<span data-ttu-id="e6eb0-194">Clique com o botão direito do mouse na tabela Person e, em seguida, clique em **Mostrar Dados da Tabela** para ver a coluna discriminatória.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-194">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

<span data-ttu-id="e6eb0-195">O diagrama a seguir ilustra a estrutura do novo banco de dados de escola:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-195">The following diagram illustrates the structure of the new School database:</span></span>

![School_database_diagram](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

## <a name="deploy-to-azure"></a><span data-ttu-id="e6eb0-197">Implantar no Azure</span><span class="sxs-lookup"><span data-stu-id="e6eb0-197">Deploy to Azure</span></span>

<span data-ttu-id="e6eb0-198">Esta seção requer que você concluiu a opcional **Implantando o aplicativo do Azure** seção [parte 3, classificação, filtragem e paginação](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) esta série de tutoriais.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-198">This section requires you to have completed the optional **Deploying the app to Azure** section in [Part 3, Sorting, Filtering, and Paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md) of this tutorial series.</span></span> <span data-ttu-id="e6eb0-199">Se você tiver erros de migrações que resolvido excluindo o banco de dados em seu projeto local, ignore esta etapa; ou crie um novo site e o banco de dados e implantar para o novo ambiente.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-199">If you had migrations errors that you resolved by deleting the database in your local project, skip this step; or create a new site and database, and deploy to the new environment.</span></span>

1. <span data-ttu-id="e6eb0-200">No Visual Studio, clique com botão direito no projeto no **Gerenciador de soluções** e selecione **publicar** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-200">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>

2. <span data-ttu-id="e6eb0-201">Clique em **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-201">Click **Publish**.</span></span>

    <span data-ttu-id="e6eb0-202">O aplicativo Web é aberto no navegador padrão.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-202">The Web app opens in your default browser.</span></span>

3. <span data-ttu-id="e6eb0-203">Testar o aplicativo para verificar se ele está funcionando.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-203">Test the application to verify it's working.</span></span>

    <span data-ttu-id="e6eb0-204">Na primeira vez que você executa uma página que acessa o banco de dados, o Entity Framework é executado todas as migrações `Up` métodos necessários para colocar o banco de dados atualizado com o modelo de dados atual.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-204">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e6eb0-205">Obter o código</span><span class="sxs-lookup"><span data-stu-id="e6eb0-205">Get the code</span></span>

[<span data-ttu-id="e6eb0-206">Baixe o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="e6eb0-206">Download Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a><span data-ttu-id="e6eb0-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e6eb0-207">Additional resources</span></span>

<span data-ttu-id="e6eb0-208">Links para outros recursos do Entity Framework podem ser encontradas na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="e6eb0-208">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

<span data-ttu-id="e6eb0-209">Para obter mais informações sobre esta e outras estruturas de herança, consulte [padrão de herança TPT](https://msdn.microsoft.com/data/jj618293) e [padrão de herança TPH](https://msdn.microsoft.com/data/jj618292) no MSDN.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-209">For more information about this and other inheritance structures, see [TPT Inheritance Pattern](https://msdn.microsoft.com/data/jj618293) and [TPH Inheritance Pattern](https://msdn.microsoft.com/data/jj618292) on MSDN.</span></span> <span data-ttu-id="e6eb0-210">No próximo tutorial, você verá como lidar com uma variedade de cenários relativamente avançados do Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-210">In the next tutorial you'll see how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e6eb0-211">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e6eb0-211">Next steps</span></span>

<span data-ttu-id="e6eb0-212">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="e6eb0-212">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e6eb0-213">Aprendeu a herança do mapa para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="e6eb0-213">Learned to map inheritance to database</span></span>
> * <span data-ttu-id="e6eb0-214">Criei a classe Person</span><span class="sxs-lookup"><span data-stu-id="e6eb0-214">Created the Person class</span></span>
> * <span data-ttu-id="e6eb0-215">Aluno e instrutor atualizado</span><span class="sxs-lookup"><span data-stu-id="e6eb0-215">Updated Instructor and Student</span></span>
> * <span data-ttu-id="e6eb0-216">Pessoa adicionada ao modelo</span><span class="sxs-lookup"><span data-stu-id="e6eb0-216">Added Person to the Model</span></span>
> * <span data-ttu-id="e6eb0-217">Criado e atualize as migrações</span><span class="sxs-lookup"><span data-stu-id="e6eb0-217">Created and Update Migrations</span></span>
> * <span data-ttu-id="e6eb0-218">Testado a implementação</span><span class="sxs-lookup"><span data-stu-id="e6eb0-218">Tested the implementation</span></span>
> * <span data-ttu-id="e6eb0-219">Implantado no Azure</span><span class="sxs-lookup"><span data-stu-id="e6eb0-219">Deployed to Azure</span></span>

<span data-ttu-id="e6eb0-220">Avance para o próximo artigo para saber mais sobre tópicos que são úteis a serem consideradas quando você vai além das noções básicas de desenvolvimento de aplicativos web ASP.NET que usam o Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="e6eb0-220">Advance to the next article to learn about topics that are useful to be aware of when you go beyond the basics of developing ASP.NET web applications that use Entity Framework Code First.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="e6eb0-221">Cenários de Entity Framework avançados</span><span class="sxs-lookup"><span data-stu-id="e6eb0-221">Advanced Entity Framework Scenarios</span></span>](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)