---
title: 'Tutorial: Introdução ao EF Core em um aplicativo Web ASP.NET MVC'
description: Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 8cad650cacd0b467a45a13c7dde0410aa41fdb32
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212569"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="5fcf7-103">Tutorial: Introdução ao EF Core em um aplicativo Web ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="5fcf7-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="5fcf7-104">O aplicativo Web de exemplo Contoso University demonstra como criar aplicativos Web em ASP.NET Core 2.2 MVC usando o EF (Entity Framework) Core 2.2 e o Visual Studio 2017 ou 2019.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-104">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="5fcf7-105">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-105">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="5fcf7-106">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-106">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="5fcf7-107">Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-107">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="5fcf7-108">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5fcf7-109">Criar um aplicativo Web MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fcf7-109">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="5fcf7-110">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="5fcf7-110">Set up the site style</span></span>
> * <span data-ttu-id="5fcf7-111">Aprender sobre pacotes NuGet do EF Core</span><span class="sxs-lookup"><span data-stu-id="5fcf7-111">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="5fcf7-112">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-112">Create the data model</span></span>
> * <span data-ttu-id="5fcf7-113">Criar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-113">Create the database context</span></span>
> * <span data-ttu-id="5fcf7-114">Registrar o contexto para injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="5fcf7-114">Register the context for dependency injection</span></span>
> * <span data-ttu-id="5fcf7-115">Inicializar o banco de dados com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="5fcf7-115">Initialize the database with test data</span></span>
> * <span data-ttu-id="5fcf7-116">Criar um controlador e exibições</span><span class="sxs-lookup"><span data-stu-id="5fcf7-116">Create a controller and views</span></span>
> * <span data-ttu-id="5fcf7-117">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-117">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5fcf7-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="5fcf7-118">Prerequisites</span></span>

* [<span data-ttu-id="5fcf7-119">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="5fcf7-119">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download)
* <span data-ttu-id="5fcf7-120">[Visual Studio 2017 ou 2019](https://visualstudio.microsoft.com/downloads/) com as seguintes cargas de trabalho:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-120">[Visual Studio 2017 or 2019](https://visualstudio.microsoft.com/downloads/) with the following workloads:</span></span>
  * <span data-ttu-id="5fcf7-121">Carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="5fcf7-121">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="5fcf7-122">Carga de trabalho de **desenvolvimento multiplataforma do .NET Core**</span><span class="sxs-lookup"><span data-stu-id="5fcf7-122">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5fcf7-123">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="5fcf7-123">Troubleshooting</span></span>

<span data-ttu-id="5fcf7-124">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-124">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="5fcf7-125">Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-125">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="5fcf7-126">Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-126">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="5fcf7-127">Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-127">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="5fcf7-128">Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-128">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="5fcf7-129">Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-129">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="5fcf7-130">Aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="5fcf7-130">Contoso University web app</span></span>

<span data-ttu-id="5fcf7-131">O aplicativo que você criará nestes tutoriais é um site simples de uma universidade.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-131">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="5fcf7-132">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-132">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="5fcf7-133">Estas são algumas das telas que você criará.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-133">Here are a few of the screens you'll create.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="5fcf7-136">Criar aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="5fcf7-136">Create web app</span></span>

* <span data-ttu-id="5fcf7-137">Abra o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-137">Open Visual Studio.</span></span>

* <span data-ttu-id="5fcf7-138">No menu **Arquivo**, selecione **Novo > Projeto**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-138">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="5fcf7-139">No painel esquerdo, selecione **Instalado > Visual C# > Web**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-139">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="5fcf7-140">Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-140">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="5fcf7-141">Insira **ContosoUniversity** como o nome e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-141">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![Caixa de diálogo Novo Projeto](intro/_static/new-project2.png)

* <span data-ttu-id="5fcf7-143">Aguarde a caixa de diálogo **Novo Aplicativo Web ASP.NET Core** aparecer.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-143">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="5fcf7-144">Selecione **.NET Core**, **ASP.NET Core 2.2** e o modelo **Aplicativo Web (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-144">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="5fcf7-145">Verifique se a opção **Autenticação** está definida como **Sem Autenticação**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-145">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="5fcf7-146">Selecione **OK**</span><span class="sxs-lookup"><span data-stu-id="5fcf7-146">Select **OK**</span></span>

  ![Caixa de diálogo Novo projeto ASP.NET Core](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="5fcf7-148">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="5fcf7-148">Set up the site style</span></span>

<span data-ttu-id="5fcf7-149">Algumas alterações simples configurarão o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-149">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="5fcf7-150">Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-150">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="5fcf7-151">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="5fcf7-151">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="5fcf7-152">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-152">There are three occurrences.</span></span>

* <span data-ttu-id="5fcf7-153">Adicione entradas de menu **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-153">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="5fcf7-154">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-154">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="5fcf7-155">Em *Views/Home/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-155">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="5fcf7-156">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-156">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="5fcf7-157">Você verá a home page com guias para as páginas que você criará nestes tutoriais.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-157">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Home page da Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="5fcf7-159">Sobre pacotes NuGet do EF Core</span><span class="sxs-lookup"><span data-stu-id="5fcf7-159">About EF Core NuGet packages</span></span>

<span data-ttu-id="5fcf7-160">Para adicionar o suporte do EF Core a um projeto, instale o provedor de banco de dados que você deseja ter como destino.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-160">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="5fcf7-161">Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-161">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="5fcf7-162">Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto, você não precisa referenciar o pacote.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-162">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="5fcf7-163">O pacote SQL Server do EF e suas dependências (`Microsoft.EntityFrameworkCore` e `Microsoft.EntityFrameworkCore.Relational`) fornecem suporte de tempo de execução para o EF.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-163">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="5fcf7-164">Você adicionará um pacote de ferramentas posteriormente, no tutorial [Migrações](migrations.md).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-164">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="5fcf7-165">Para obter informações sobre outros provedores de banco de dados que estão disponíveis para o Entity Framework Core, consulte [Provedores de banco de dados](/ef/core/providers/).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-165">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="5fcf7-166">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-166">Create the data model</span></span>

<span data-ttu-id="5fcf7-167">Em seguida, você criará as classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-167">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="5fcf7-168">Você começará com as três entidades a seguir.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-168">You'll start with the following three entities.</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="5fcf7-170">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`, e uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-170">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="5fcf7-171">Em outras palavras, um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-171">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="5fcf7-172">Nas seções a seguir, você criará uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-172">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="5fcf7-173">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="5fcf7-173">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="5fcf7-175">Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* e substitua o código de modelo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-175">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="5fcf7-176">A propriedade `ID` se tornará a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-176">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="5fcf7-177">Por padrão, o Entity Framework interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-177">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="5fcf7-178">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-178">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="5fcf7-179">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-179">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="5fcf7-180">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazenará todas as entidades `Enrollment` relacionadas a essa entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-180">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="5fcf7-181">Em outras palavras, se determinada linha Aluno no banco de dados tiver duas linhas Registro relacionadas (linhas que contêm o valor de chave primária do aluno na coluna de chave estrangeira StudentID), a propriedade de navegação `Enrollments` dessa entidade `Student` conterá as duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-181">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="5fcf7-182">Se uma propriedade de navegação pode armazenar várias entidades (como em relações muitos para muitos ou um-para-muitos), o tipo precisa ser uma lista na qual entradas podem ser adicionadas, excluídas e atualizadas, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-182">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="5fcf7-183">Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-183">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="5fcf7-184">Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-184">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="5fcf7-185">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="5fcf7-185">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="5fcf7-187">Na pasta *Models*, crie *Enrollment.cs* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-187">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="5fcf7-188">A propriedade `EnrollmentID` será a chave primária; essa entidade usa o padrão `classnameID` em vez de `ID` por si só, como você viu na entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-188">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="5fcf7-189">Normalmente, você escolhe um padrão e usa-o em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-189">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="5fcf7-190">Aqui, a variação ilustra que você pode usar qualquer um dos padrões.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-190">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="5fcf7-191">Em um [tutorial posterior](inheritance.md), você verá como usar uma ID sem nome de classe facilita a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-191">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="5fcf7-192">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-192">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="5fcf7-193">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-193">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="5fcf7-194">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-194">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="5fcf7-195">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-195">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="5fcf7-196">Uma entidade `Enrollment` é associada a uma entidade `Student`, de modo que a propriedade possa armazenar apenas uma única entidade `Student` (ao contrário da propriedade de navegação `Student.Enrollments` que você viu anteriormente, que pode armazenar várias entidades `Enrollment`).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="5fcf7-197">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-197">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="5fcf7-198">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-198">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="5fcf7-199">O Entity Framework interpreta uma propriedade como uma propriedade de chave estrangeira se ela é nomeada `<navigation property name><primary key property name>` (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-199">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="5fcf7-200">As propriedades de chave estrangeira também podem ser nomeadas apenas `<primary key property name>` (por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-200">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="5fcf7-201">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="5fcf7-201">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="5fcf7-203">Na pasta *Models*, crie *Course.cs* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-203">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="5fcf7-204">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="5fcf7-205">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="5fcf7-206">Falaremos mais sobre o atributo `DatabaseGenerated` em um [tutorial posterior](complex-data-model.md) desta série.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-206">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="5fcf7-207">Basicamente, esse atributo permite que você insira a chave primária do curso, em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-207">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="5fcf7-208">Criar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-208">Create the database context</span></span>

<span data-ttu-id="5fcf7-209">A classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-209">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="5fcf7-210">Você cria essa classe derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-210">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="5fcf7-211">No código, especifique quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-211">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="5fcf7-212">Também personalize o comportamento específico do Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-212">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="5fcf7-213">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="5fcf7-214">Na pasta do projeto, crie uma pasta chamada *Dados*.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-214">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="5fcf7-215">Na pasta *Dados*, crie um novo arquivo de classe chamado *SchoolContext.cs* e substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-215">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="5fcf7-216">Esse código cria uma propriedade `DbSet` para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-216">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="5fcf7-217">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-217">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5fcf7-218">Você pode omitir as instruções `DbSet<Enrollment>` e `DbSet<Course>` e elas funcionarão da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-218">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="5fcf7-219">O Entity Framework inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-219">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="5fcf7-220">Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-220">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="5fcf7-221">Em geral, os nomes de propriedade de coleções são plurais (Alunos em vez de Aluno), mas os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-221">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="5fcf7-222">Para esses tutoriais, você substituirá o comportamento padrão especificando nomes singulares de tabela no DbContext.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-222">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="5fcf7-223">Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-223">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="5fcf7-224">Registrar o SchoolContext</span><span class="sxs-lookup"><span data-stu-id="5fcf7-224">Register the SchoolContext</span></span>

<span data-ttu-id="5fcf7-225">O ASP.NET Core implementa a [injeção de dependência](../../fundamentals/dependency-injection.md) por padrão.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-225">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="5fcf7-226">Serviços (como o contexto de banco de dados do EF) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-226">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5fcf7-227">Os componentes que exigem esses serviços (como controladores MVC) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-227">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5fcf7-228">Você verá o código de construtor do controlador que obtém uma instância de contexto mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-228">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="5fcf7-229">Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-229">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="5fcf7-230">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-230">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="5fcf7-231">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-231">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="5fcf7-232">Adicione instruções `using` aos namespaces `ContosoUniversity.Data` e `Microsoft.EntityFrameworkCore` e, em seguida, compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-232">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="5fcf7-233">Abra o arquivo *appsettings.json* e adicione uma cadeia de conexão, conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-233">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="5fcf7-234">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="5fcf7-234">SQL Server Express LocalDB</span></span>

<span data-ttu-id="5fcf7-235">A cadeia de conexão especifica um banco de dados LocalDB do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-235">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="5fcf7-236">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-236">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="5fcf7-237">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-237">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="5fcf7-238">Por padrão, o LocalDB cria arquivos de banco de dados *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-238">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="5fcf7-239">Inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="5fcf7-239">Initialize DB with test data</span></span>

<span data-ttu-id="5fcf7-240">O Entity Framework criará um banco de dados vazio para você.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-240">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="5fcf7-241">Nesta seção, você escreve um método que é chamado depois que o banco de dados é criado para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-241">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="5fcf7-242">Aqui, você usará o método `EnsureCreated` para criar o banco de dados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-242">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="5fcf7-243">Em um [tutorial posterior](migrations.md), você verá como manipular as alterações do modelo usando as Migrações do Code First para alterar o esquema de banco de dados, em vez de remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-243">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="5fcf7-244">Na pasta *Data*, crie um novo arquivo de classe chamado *DbInitializer.cs* e substitua o código de modelo pelo código a seguir, que faz com que um banco de dados seja criado, quando necessário, e carrega dados de teste no novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-244">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="5fcf7-245">O código verifica se há alunos no banco de dados e, se não há, ele pressupõe que o banco de dados é novo e precisa ser propagado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-245">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="5fcf7-246">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-246">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="5fcf7-247">Em *Program.cs*, modifique o método `Main` para fazer o seguinte na inicialização do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-247">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="5fcf7-248">Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-248">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="5fcf7-249">Chame o método de semente passando a ele o contexto.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-249">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="5fcf7-250">Descarte o contexto quando o método de semente for concluído.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-250">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="5fcf7-251">Adicione instruções `using`:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-251">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="5fcf7-252">Nos tutoriais mais antigos, você poderá ver um código semelhante no método `Configure` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-252">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="5fcf7-253">Recomendamos que você use o método `Configure` apenas para configurar o pipeline de solicitação.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-253">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="5fcf7-254">O código de inicialização do aplicativo pertence ao método `Main`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-254">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="5fcf7-255">Agora, na primeira vez que você executar o aplicativo, o banco de dados será criado e propagado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-255">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="5fcf7-256">Sempre que você alterar o modelo de dados, exclua o banco de dados, atualize o método de semente e comece novamente com um novo banco de dados da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-256">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="5fcf7-257">Nos próximos tutoriais, você verá como modificar o banco de dados quando o modelo de dados for alterado, sem excluí-lo e recriá-lo.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-257">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="5fcf7-258">Criar um controlador e exibições</span><span class="sxs-lookup"><span data-stu-id="5fcf7-258">Create controller and views</span></span>

<span data-ttu-id="5fcf7-259">Em seguida, você usará o mecanismo de scaffolding no Visual Studio para adicionar um controlador MVC e exibições que usam o EF para consultar e salvar dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-259">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="5fcf7-260">A criação automática de métodos de ação CRUD e exibições é conhecida como scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-260">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="5fcf7-261">O scaffolding difere da geração de código, em que o código gerado por scaffolding é um ponto de partida que você pode modificar de acordo com seus requisitos, enquanto que normalmente o código gerado não é modificado.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-261">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="5fcf7-262">Quando precisar personalizar o código gerado, use classes parciais ou regenere o código quando as coisas mudarem.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-262">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="5fcf7-263">Clique com o botão direito do mouse na pasta **Controladores** no **Gerenciador de Soluções** e selecione **Adicionar > Novo Item Gerado por Scaffolding**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-263">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="5fcf7-264">Na caixa de diálogo **Adicionar Scaffolding**:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-264">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="5fcf7-265">Selecione **Controlador MVC com exibições, usando o Entity Framework**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-265">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="5fcf7-266">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-266">Click **Add**.</span></span> <span data-ttu-id="5fcf7-267">A caixa de diálogo **Adicionar Controlador MVC com exibições, usando o Entity Framework** é exibida.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-267">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Gerar aluno por scaffolding](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="5fcf7-269">Na **classe Model**, selecione **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-269">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="5fcf7-270">Na **Classe de contexto de dados** selecione **SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-270">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="5fcf7-271">Aceite o **StudentsController** padrão como o nome.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-271">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="5fcf7-272">Clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-272">Click **Add**.</span></span>

  <span data-ttu-id="5fcf7-273">Quando você clica em **Adicionar**, o mecanismo de scaffolding do Visual Studio cria um arquivo *StudentsController.cs* e um conjunto de exibições (arquivos *.cshtml*) que funcionam com o controlador.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-273">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="5fcf7-274">(O mecanismo de scaffolding também poderá criar o contexto de banco de dados para você se não criá-lo manualmente primeiro como fez anteriormente para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-274">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="5fcf7-275">Especifique uma nova classe de contexto na caixa **Adicionar Controlador** clicando no sinal de adição à direita de **Classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-275">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="5fcf7-276">Em seguida, o Visual Studio criará a classe `DbContext`, bem como o controlador e as exibições.)</span><span class="sxs-lookup"><span data-stu-id="5fcf7-276">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="5fcf7-277">Você observará que o controlador usa um `SchoolContext` como parâmetro de construtor.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-277">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="5fcf7-278">A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-278">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="5fcf7-279">Você configurou isso no arquivo *Startup.cs* anteriormente.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-279">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="5fcf7-280">O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-280">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="5fcf7-281">O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-281">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="5fcf7-282">Você aprenderá sobre os elementos de programação assíncronos nesse código mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-282">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="5fcf7-283">A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-283">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="5fcf7-284">Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-284">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="5fcf7-285">Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-285">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="5fcf7-286">Dependendo da largura da janela do navegador, você verá o link da guia `Students` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-286">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="5fcf7-289">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-289">View the database</span></span>

<span data-ttu-id="5fcf7-290">Quando você iniciou o aplicativo, o método `DbInitializer.Initialize` chamou `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-290">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="5fcf7-291">O EF observou que não havia nenhum banco de dados e, portanto, ele criou um; em seguida, o restante do código do método `Initialize` populou o banco de dados com os dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-291">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="5fcf7-292">Use o **SSOX** (Pesquisador de Objetos do SQL Server) para exibir o banco de dados no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-292">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="5fcf7-293">Feche o navegador.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-293">Close the browser.</span></span>

<span data-ttu-id="5fcf7-294">Se a janela do SSOX ainda não estiver aberta, selecione-a no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-294">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="5fcf7-295">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados** e, em seguida, clique na entrada do nome do banco de dados que está na cadeia de conexão no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-295">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="5fcf7-296">Expanda o nó **Tabelas** para ver as tabelas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-296">Expand the **Tables** node to see the tables in your database.</span></span>

![Tabelas no SSOX](intro/_static/ssox-tables.png)

<span data-ttu-id="5fcf7-298">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas que foram inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-298">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

<span data-ttu-id="5fcf7-300">Os arquivos de banco de dados *.mdf* e *.ldf* estão na pasta *C:\Usuários\\\<yourusername>*.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-300">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="5fcf7-301">Como você está chamando `EnsureCreated` no método inicializador executado na inicialização do aplicativo, agora você pode fazer uma alteração na classe `Student`, excluir o banco de dados, executar novamente o aplicativo e o banco de dados será recriado automaticamente para que ele corresponda à alteração.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-301">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="5fcf7-302">Por exemplo, se você adicionar uma propriedade `EmailAddress` à classe `Student`, verá uma nova coluna `EmailAddress` na tabela recriada.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-302">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="5fcf7-303">Convenções</span><span class="sxs-lookup"><span data-stu-id="5fcf7-303">Conventions</span></span>

<span data-ttu-id="5fcf7-304">A quantidade de código feita para que o Entity Framework possa criar um banco de dados completo para você é mínima, devido ao uso de convenções ou de suposições feitas pelo Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-304">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="5fcf7-305">Os nomes de propriedades `DbSet` são usadas como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-305">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="5fcf7-306">Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-306">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="5fcf7-307">Os nomes de propriedade de entidade são usados para nomes de coluna.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-307">Entity property names are used for column names.</span></span>

* <span data-ttu-id="5fcf7-308">As propriedades de entidade que são nomeadas ID ou classnameID são reconhecidas como propriedades de chave primária.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-308">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="5fcf7-309">Uma propriedade é interpretada como sendo de chave estrangeira se tiver um *\<nome da propriedade de navegação>\<nome da propriedade de chave primária>* (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-309">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="5fcf7-310">As propriedades de chave estrangeira também podem ser nomeadas apenas *\<nome da propriedade da chave primária>* (por exemplo, `EnrollmentID`, pois a chave primária da entidade `Enrollment` é `EnrollmentID`).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-310">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="5fcf7-311">O comportamento convencional pode ser substituído.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-311">Conventional behavior can be overridden.</span></span> <span data-ttu-id="5fcf7-312">Por exemplo, você pode especificar os nomes de tabela de forma explícita, conforme visto anteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-312">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="5fcf7-313">Além disso, você pode definir nomes de coluna e qualquer propriedade como a chave primária ou chave estrangeira, como você verá em um [tutorial posterior](complex-data-model.md) desta série.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-313">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="5fcf7-314">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="5fcf7-314">Asynchronous code</span></span>

<span data-ttu-id="5fcf7-315">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-315">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="5fcf7-316">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-316">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="5fcf7-317">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-317">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="5fcf7-318">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-318">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="5fcf7-319">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-319">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="5fcf7-320">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-320">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="5fcf7-321">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-321">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="5fcf7-322">No código a seguir, a palavra-chave `async`, o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-322">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="5fcf7-323">A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-323">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="5fcf7-324">O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-324">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="5fcf7-325">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-325">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="5fcf7-326">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-326">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="5fcf7-327">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-327">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="5fcf7-328">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-328">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="5fcf7-329">Algumas coisas a serem consideradas quando você estiver escrevendo um código assíncrono que usa o Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-329">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="5fcf7-330">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-330">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="5fcf7-331">Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-331">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="5fcf7-332">Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-332">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="5fcf7-333">Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-333">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="5fcf7-334">Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-334">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="5fcf7-335">Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-335">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="5fcf7-336">Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).</span><span class="sxs-lookup"><span data-stu-id="5fcf7-336">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="5fcf7-337">Obter o código</span><span class="sxs-lookup"><span data-stu-id="5fcf7-337">Get the code</span></span>

[<span data-ttu-id="5fcf7-338">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-338">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="5fcf7-339">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="5fcf7-339">Next steps</span></span>

<span data-ttu-id="5fcf7-340">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="5fcf7-340">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5fcf7-341">Criou um aplicativo Web MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fcf7-341">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="5fcf7-342">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="5fcf7-342">Set up the site style</span></span>
> * <span data-ttu-id="5fcf7-343">Aprendeu sobre pacotes NuGet do EF Core</span><span class="sxs-lookup"><span data-stu-id="5fcf7-343">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="5fcf7-344">Adicionou o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-344">Created the data model</span></span>
> * <span data-ttu-id="5fcf7-345">Criou o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-345">Created the database context</span></span>
> * <span data-ttu-id="5fcf7-346">Registrou o SchoolContext</span><span class="sxs-lookup"><span data-stu-id="5fcf7-346">Registered the SchoolContext</span></span>
> * <span data-ttu-id="5fcf7-347">Inicializou o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="5fcf7-347">Initialized DB with test data</span></span>
> * <span data-ttu-id="5fcf7-348">Criou um controlador e exibições</span><span class="sxs-lookup"><span data-stu-id="5fcf7-348">Created controller and views</span></span>
> * <span data-ttu-id="5fcf7-349">Exibiu o banco de dados</span><span class="sxs-lookup"><span data-stu-id="5fcf7-349">Viewed the database</span></span>

<span data-ttu-id="5fcf7-350">No tutorial a seguir, você aprenderá a executar operações CRUD (criar, ler, atualizar e excluir) básicas.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-350">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="5fcf7-351">Vá para o próximo tutorial para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.</span><span class="sxs-lookup"><span data-stu-id="5fcf7-351">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5fcf7-352">Implementar a funcionalidade CRUD básica</span><span class="sxs-lookup"><span data-stu-id="5fcf7-352">Implement basic CRUD functionality</span></span>](crud.md)
