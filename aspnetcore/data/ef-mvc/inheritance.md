---
title: 'Tutorial: implementar a herança – ASP.NET MVC com o EF Core'
description: Este tutorial mostrará como implementar a herança no modelo de dados, usando o Entity Framework Core em um aplicativo ASP.NET Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: dab3d2b057162f6d986db10e74e3681acc0ada3b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657237"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="45e3f-103">Tutorial: implementar a herança – ASP.NET MVC com o EF Core</span><span class="sxs-lookup"><span data-stu-id="45e3f-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="45e3f-104">No tutorial anterior, você tratou exceções de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="45e3f-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="45e3f-105">Este tutorial mostrará como implementar a herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="45e3f-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="45e3f-106">Na programação orientada a objeto, você pode usar a herança para facilitar a reutilização de código.</span><span class="sxs-lookup"><span data-stu-id="45e3f-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="45e3f-107">Neste tutorial, você alterará as classes `Instructor` e `Student`, de modo que elas derivem de uma classe base `Person` que contém propriedades, como `LastName`, comuns a instrutores e alunos.</span><span class="sxs-lookup"><span data-stu-id="45e3f-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="45e3f-108">Você não adicionará nem alterará as páginas da Web, mas alterará uma parte do código, e essas alterações serão refletidas automaticamente no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45e3f-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="45e3f-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="45e3f-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45e3f-110">Mapeará a herança para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="45e3f-110">Map inheritance to database</span></span>
> * <span data-ttu-id="45e3f-111">Criar a classe Person</span><span class="sxs-lookup"><span data-stu-id="45e3f-111">Create the Person class</span></span>
> * <span data-ttu-id="45e3f-112">Atualizará Instructor e Student</span><span class="sxs-lookup"><span data-stu-id="45e3f-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="45e3f-113">Adicionará Person ao modelo</span><span class="sxs-lookup"><span data-stu-id="45e3f-113">Add Person to the model</span></span>
> * <span data-ttu-id="45e3f-114">Criará e atualizará migrações</span><span class="sxs-lookup"><span data-stu-id="45e3f-114">Create and update migrations</span></span>
> * <span data-ttu-id="45e3f-115">Testará a implementação</span><span class="sxs-lookup"><span data-stu-id="45e3f-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="45e3f-116">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="45e3f-116">Prerequisites</span></span>

* [<span data-ttu-id="45e3f-117">Manipular a simultaneidade</span><span class="sxs-lookup"><span data-stu-id="45e3f-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="45e3f-118">Mapeará a herança para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="45e3f-118">Map inheritance to database</span></span>

<span data-ttu-id="45e3f-119">As classes `Instructor` e `Student` no modelo de dados Escola têm várias propriedades idênticas:</span><span class="sxs-lookup"><span data-stu-id="45e3f-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Classes Student e Instructor](inheritance/_static/no-inheritance.png)

<span data-ttu-id="45e3f-121">Suponha que você deseje eliminar o código redundante para as propriedades compartilhadas pelas entidades `Instructor` e `Student`.</span><span class="sxs-lookup"><span data-stu-id="45e3f-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="45e3f-122">Ou você deseja escrever um serviço que pode formatar nomes sem se preocupar se o nome foi obtido de um instrutor ou um aluno.</span><span class="sxs-lookup"><span data-stu-id="45e3f-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="45e3f-123">Você pode criar uma classe base `Person` que contém apenas essas propriedades compartilhadas e, em seguida, fazer com que as classes `Instructor` e `Student` herdem dessa classe base, conforme mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="45e3f-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Classes Student e Instructor derivando da classe Person](inheritance/_static/inheritance.png)

<span data-ttu-id="45e3f-125">Há várias maneiras pelas quais essa estrutura de herança pode ser representada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45e3f-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="45e3f-126">Você pode ter uma tabela Person que inclui informações sobre alunos e instrutores em uma única tabela.</span><span class="sxs-lookup"><span data-stu-id="45e3f-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="45e3f-127">Algumas das colunas podem se aplicar somente a instrutores (HireDate), algumas somente a alunos (EnrollmentDate) e outras a ambos (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="45e3f-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="45e3f-128">Normalmente, você terá uma coluna discriminatória para indicar qual tipo cada linha representa.</span><span class="sxs-lookup"><span data-stu-id="45e3f-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="45e3f-129">Por exemplo, a coluna discriminatória pode ter "Instrutor" para instrutores e "Aluno" para alunos.</span><span class="sxs-lookup"><span data-stu-id="45e3f-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Exemplo de tabela por hierarquia](inheritance/_static/tph.png)

<span data-ttu-id="45e3f-131">Esse padrão de geração de uma estrutura de herança de entidade com base em uma tabela de banco de dados individual é chamado de herança TPH (tabela por hierarquia).</span><span class="sxs-lookup"><span data-stu-id="45e3f-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="45e3f-132">Uma alternativa é fazer com que o banco de dados se pareça mais com a estrutura de herança.</span><span class="sxs-lookup"><span data-stu-id="45e3f-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="45e3f-133">Por exemplo, você pode ter apenas os campos de nome na tabela Pessoa e as tabelas separadas Instrutor e Aluno com os campos de data.</span><span class="sxs-lookup"><span data-stu-id="45e3f-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Herança de tabela por tipo](inheritance/_static/tpt.png)

<span data-ttu-id="45e3f-135">Esse padrão de criação de uma tabela de banco de dados para cada classe de entidade é chamado de herança TPT (tabela por tipo).</span><span class="sxs-lookup"><span data-stu-id="45e3f-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="45e3f-136">Outra opção é mapear todos os tipos não abstratos para tabelas individuais.</span><span class="sxs-lookup"><span data-stu-id="45e3f-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="45e3f-137">Todas as propriedades de uma classe, incluindo propriedades herdadas, são mapeadas para colunas da tabela correspondente.</span><span class="sxs-lookup"><span data-stu-id="45e3f-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="45e3f-138">Esse padrão é chamado de herança TPC (Tabela por Classe Concreta).</span><span class="sxs-lookup"><span data-stu-id="45e3f-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="45e3f-139">Se você tiver implementado a herança TPC para as classes Person, Student e Instructor, conforme mostrado anteriormente, as tabelas Aluno e Instrutor não parecerão diferentes após a implementação da herança do que antes.</span><span class="sxs-lookup"><span data-stu-id="45e3f-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="45e3f-140">Em geral, os padrões de herança TPC e TPH oferecem melhor desempenho do que os padrões de herança TPT, porque os padrões TPT podem resultar em consultas de junção complexas.</span><span class="sxs-lookup"><span data-stu-id="45e3f-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="45e3f-141">Este tutorial demonstra como implementar a herança TPH.</span><span class="sxs-lookup"><span data-stu-id="45e3f-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="45e3f-142">TPH é o único padrão de herança compatível com o Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="45e3f-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="45e3f-143">O que você fará é criar uma classe `Person`, alterar as classes `Instructor` e `Student` para que elas derivem de `Person`, adicionar a nova classe ao `DbContext` e criar uma migração.</span><span class="sxs-lookup"><span data-stu-id="45e3f-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="45e3f-144">Considere a possibilidade de salvar uma cópia do projeto antes de fazer as alterações a seguir.</span><span class="sxs-lookup"><span data-stu-id="45e3f-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="45e3f-145">Em seguida, se você tiver problemas e precisar recomeçar, será mais fácil começar do projeto salvo, em vez de reverter as etapas executadas para este tutorial ou voltar ao início da série inteira.</span><span class="sxs-lookup"><span data-stu-id="45e3f-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="45e3f-146">Criar a classe Person</span><span class="sxs-lookup"><span data-stu-id="45e3f-146">Create the Person class</span></span>

<span data-ttu-id="45e3f-147">Na pasta Models, crie Person.cs e substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="45e3f-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="45e3f-148">Atualizará Instructor e Student</span><span class="sxs-lookup"><span data-stu-id="45e3f-148">Update Instructor and Student</span></span>

<span data-ttu-id="45e3f-149">Em *Instructor.cs*, derive a classe Instructor da classe Person e remova os campos de nome e chave.</span><span class="sxs-lookup"><span data-stu-id="45e3f-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="45e3f-150">O código será semelhante ao seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="45e3f-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="45e3f-151">Faça as mesmas alterações em *Student.cs*.</span><span class="sxs-lookup"><span data-stu-id="45e3f-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="45e3f-152">Adicionará Person ao modelo</span><span class="sxs-lookup"><span data-stu-id="45e3f-152">Add Person to the model</span></span>

<span data-ttu-id="45e3f-153">Adicione o tipo de entidade Person a *SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="45e3f-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="45e3f-154">As novas linhas são realçadas.</span><span class="sxs-lookup"><span data-stu-id="45e3f-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="45e3f-155">Isso é tudo o que o Entity Framework precisa para configurar a herança de tabela por hierarquia.</span><span class="sxs-lookup"><span data-stu-id="45e3f-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="45e3f-156">Como você verá, quando o banco de dados for atualizado, ele terá uma tabela Pessoa no lugar das tabelas Aluno e Instrutor.</span><span class="sxs-lookup"><span data-stu-id="45e3f-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="45e3f-157">Criará e atualizará migrações</span><span class="sxs-lookup"><span data-stu-id="45e3f-157">Create and update migrations</span></span>

<span data-ttu-id="45e3f-158">Salve as alterações e compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="45e3f-158">Save your changes and build the project.</span></span> <span data-ttu-id="45e3f-159">Em seguida, abra a janela Comando na pasta do projeto e insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="45e3f-159">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="45e3f-160">Não execute o comando `database update` ainda.</span><span class="sxs-lookup"><span data-stu-id="45e3f-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="45e3f-161">Esse comando resultará em perda de dados porque ele removerá a tabela Instrutor e renomeará a tabela Aluno como Pessoa.</span><span class="sxs-lookup"><span data-stu-id="45e3f-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="45e3f-162">Você precisa fornecer o código personalizado para preservar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="45e3f-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="45e3f-163">Abra *Migrations/\<timestamp>_Inheritance.cs* e substitua o método `Up` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="45e3f-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="45e3f-164">Este código é responsável pelas seguintes tarefas de atualização de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="45e3f-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="45e3f-165">Remove as restrições de chave estrangeira e índices que apontam para a tabela Aluno.</span><span class="sxs-lookup"><span data-stu-id="45e3f-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="45e3f-166">Renomeia a tabela Instrutor como Pessoa e faz as alterações necessárias para que ela armazene dados de Aluno:</span><span class="sxs-lookup"><span data-stu-id="45e3f-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="45e3f-167">Adiciona EnrollmentDate que permite valo nulo para os alunos.</span><span class="sxs-lookup"><span data-stu-id="45e3f-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="45e3f-168">Adiciona a coluna Discriminatória para indicar se uma linha refere-se a um aluno ou um instrutor.</span><span class="sxs-lookup"><span data-stu-id="45e3f-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="45e3f-169">Faz com que HireDate permita valor nulo, pois linhas de alunos não terão datas de contratação.</span><span class="sxs-lookup"><span data-stu-id="45e3f-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="45e3f-170">Adiciona um campo temporário que será usado para atualizar chaves estrangeiras que apontam para alunos.</span><span class="sxs-lookup"><span data-stu-id="45e3f-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="45e3f-171">Quando você copiar os alunos para a tabela Person, eles receberão novos valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="45e3f-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="45e3f-172">Copia os dados da tabela Aluno para a tabela Pessoa.</span><span class="sxs-lookup"><span data-stu-id="45e3f-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="45e3f-173">Isso faz com que os alunos recebam novos valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="45e3f-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="45e3f-174">Corrige valores de chave estrangeira que apontam para alunos.</span><span class="sxs-lookup"><span data-stu-id="45e3f-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="45e3f-175">Recria restrições de chave estrangeira e índices, agora apontando-os para a tabela Person.</span><span class="sxs-lookup"><span data-stu-id="45e3f-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="45e3f-176">(Se você tiver usado o GUID, em vez de inteiro como o tipo de chave primária, os valores de chave primária dos alunos não precisarão ser alterados e várias dessas etapas poderão ser omitidas.)</span><span class="sxs-lookup"><span data-stu-id="45e3f-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="45e3f-177">Execute o comando `database update`:</span><span class="sxs-lookup"><span data-stu-id="45e3f-177">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="45e3f-178">(Em um sistema de produção, você fará as alterações correspondentes no método `Down`, caso já tenha usado isso para voltar à versão anterior do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45e3f-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="45e3f-179">Para este tutorial, você não usará o método `Down`.)</span><span class="sxs-lookup"><span data-stu-id="45e3f-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="45e3f-180">É possível receber outros erros ao fazer alterações de esquema em um banco de dados que contém dados existentes.</span><span class="sxs-lookup"><span data-stu-id="45e3f-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="45e3f-181">Se você receber erros de migração que não consegue resolver, altere o nome do banco de dados na cadeia de conexão ou exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45e3f-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="45e3f-182">Com um novo banco de dados, não há nenhum dado a ser migrado e o comando de atualização de banco de dados terá uma probabilidade maior de ser concluído sem erros.</span><span class="sxs-lookup"><span data-stu-id="45e3f-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="45e3f-183">Para excluir o banco de dados, use o SSOX ou execute o comando `database drop` da CLI.</span><span class="sxs-lookup"><span data-stu-id="45e3f-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="45e3f-184">Testará a implementação</span><span class="sxs-lookup"><span data-stu-id="45e3f-184">Test the implementation</span></span>

<span data-ttu-id="45e3f-185">Execute o aplicativo e teste várias páginas.</span><span class="sxs-lookup"><span data-stu-id="45e3f-185">Run the app and try various pages.</span></span> <span data-ttu-id="45e3f-186">Tudo funciona da mesma maneira que antes.</span><span class="sxs-lookup"><span data-stu-id="45e3f-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="45e3f-187">No **Pesquisador de Objetos do SQL Server**, expanda **Data Connections/SchoolContext** e, em seguida, **Tabelas** e você verá que as tabelas Aluno e Instrutor foram substituídas por uma tabela Pessoa.</span><span class="sxs-lookup"><span data-stu-id="45e3f-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="45e3f-188">Abra o designer de tabela Pessoa e você verá que ela contém todas as colunas que costumavam estar nas tabelas Aluno e Instrutor.</span><span class="sxs-lookup"><span data-stu-id="45e3f-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Tabela Person no SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="45e3f-190">Clique com o botão direito do mouse na tabela Person e, em seguida, clique em **Mostrar Dados da Tabela** para ver a coluna discriminatória.</span><span class="sxs-lookup"><span data-stu-id="45e3f-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Tabela Person no SSOX – dados de tabela](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="45e3f-192">Obter o código</span><span class="sxs-lookup"><span data-stu-id="45e3f-192">Get the code</span></span>

[<span data-ttu-id="45e3f-193">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="45e3f-193">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="45e3f-194">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="45e3f-194">Additional resources</span></span>

<span data-ttu-id="45e3f-195">Para obter mais informações sobre a herança no Entity Framework Core, consulte [Herança](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="45e3f-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="45e3f-196">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="45e3f-196">Next steps</span></span>

<span data-ttu-id="45e3f-197">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="45e3f-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45e3f-198">Mapeou a herança para o banco de dados</span><span class="sxs-lookup"><span data-stu-id="45e3f-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="45e3f-199">Criou a classe Person</span><span class="sxs-lookup"><span data-stu-id="45e3f-199">Created the Person class</span></span>
> * <span data-ttu-id="45e3f-200">Atualizou Instructor e Student</span><span class="sxs-lookup"><span data-stu-id="45e3f-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="45e3f-201">Adicionou Person ao modelo</span><span class="sxs-lookup"><span data-stu-id="45e3f-201">Added Person to the model</span></span>
> * <span data-ttu-id="45e3f-202">Criou e atualizou migrações</span><span class="sxs-lookup"><span data-stu-id="45e3f-202">Created and update migrations</span></span>
> * <span data-ttu-id="45e3f-203">Testou a implementação</span><span class="sxs-lookup"><span data-stu-id="45e3f-203">Tested the implementation</span></span>

<span data-ttu-id="45e3f-204">Vá para o próximo tutorial para saber como lidar com vários cenários relativamente avançados do Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="45e3f-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="45e3f-205">Próximo: Tópicos avançados</span><span class="sxs-lookup"><span data-stu-id="45e3f-205">Next: Advanced topics</span></span>](advanced.md)
