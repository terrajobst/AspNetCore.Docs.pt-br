---
title: Páginas Razor com o Entity Framework Core no ASP.NET Core – Tutorial 1 de 8
author: rick-anderson
description: Mostra como criar um aplicativo das Páginas do Razor usando o Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656817"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="f6e13-103">Páginas Razor com o Entity Framework Core no ASP.NET Core – Tutorial 1 de 8</span><span class="sxs-lookup"><span data-stu-id="f6e13-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="f6e13-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f6e13-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6e13-105">Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="f6e13-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="f6e13-106">O tutorial cria um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="f6e13-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f6e13-107">O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="f6e13-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f6e13-108">O tutorial usa a abordagem Code First.</span><span class="sxs-lookup"><span data-stu-id="f6e13-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="f6e13-109">Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="f6e13-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="f6e13-110">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="f6e13-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f6e13-111">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f6e13-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6e13-112">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f6e13-112">Prerequisites</span></span>

* <span data-ttu-id="f6e13-113">Se você for novo no Razor Pages, confira a série de tutoriais de [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar este.</span><span class="sxs-lookup"><span data-stu-id="f6e13-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="f6e13-116">Mecanismos de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-116">Database engines</span></span>

<span data-ttu-id="f6e13-117">As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.</span><span class="sxs-lookup"><span data-stu-id="f6e13-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="f6e13-118">As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="f6e13-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="f6e13-119">Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="f6e13-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6e13-120">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f6e13-120">Troubleshooting</span></span>

<span data-ttu-id="f6e13-121">Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f6e13-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f6e13-122">Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="f6e13-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="f6e13-123">O aplicativo de exemplo</span><span class="sxs-lookup"><span data-stu-id="f6e13-123">The sample app</span></span>

<span data-ttu-id="f6e13-124">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="f6e13-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="f6e13-125">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="f6e13-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f6e13-126">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f6e13-126">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

<span data-ttu-id="f6e13-129">O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="f6e13-130">O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="f6e13-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="f6e13-131">Siga o link na parte superior da página para obter o código-fonte do projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="f6e13-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="f6e13-132">A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial.</span><span class="sxs-lookup"><span data-stu-id="f6e13-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="f6e13-133">Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e13-135">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="f6e13-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f6e13-136">Exclua três arquivos e uma pasta que tenha *SQLite* no nome.</span><span class="sxs-lookup"><span data-stu-id="f6e13-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="f6e13-137">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-137">Build the project.</span></span>
* <span data-ttu-id="f6e13-138">No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="f6e13-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="f6e13-139">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e13-141">Para executar o aplicativo depois de baixar o projeto concluído:</span><span class="sxs-lookup"><span data-stu-id="f6e13-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f6e13-142">Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="f6e13-143">Exclua *Startup.cs* e altere o nome de *StartupSQLite.cs* para *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="f6e13-144">Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="f6e13-145">Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="f6e13-146">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-146">Build the project.</span></span>
* <span data-ttu-id="f6e13-147">Em um prompt de comando na pasta do projeto, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f6e13-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="f6e13-148">Em sua ferramenta do SQLite, execute a seguinte instrução SQL:</span><span class="sxs-lookup"><span data-stu-id="f6e13-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="f6e13-149">Execute o projeto para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="f6e13-150">Criar o projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="f6e13-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-152">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="f6e13-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6e13-153">Selecione **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f6e13-154">Nomeie o projeto *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="f6e13-155">É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.</span><span class="sxs-lookup"><span data-stu-id="f6e13-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="f6e13-156">Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e13-158">Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.</span><span class="sxs-lookup"><span data-stu-id="f6e13-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="f6e13-159">Execute os comandos a seguir para criar um projeto do Razor Pages `cd` para a nova pasta do projeto:</span><span class="sxs-lookup"><span data-stu-id="f6e13-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f6e13-160">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="f6e13-160">Set up the site style</span></span>

<span data-ttu-id="f6e13-161">Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f6e13-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="f6e13-162">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="f6e13-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f6e13-163">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="f6e13-163">There are three occurrences.</span></span>

* <span data-ttu-id="f6e13-164">Exclua as entradas de menu **Início** e **Privacidade**, então adicione as entradas para **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="f6e13-165">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="f6e13-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="f6e13-166">Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f6e13-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="f6e13-167">Execute o aplicativo para verificar se o página inicial aparece.</span><span class="sxs-lookup"><span data-stu-id="f6e13-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="f6e13-168">O modelo de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-168">The data model</span></span>

<span data-ttu-id="f6e13-169">As seções a seguir criam um modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="f6e13-169">The following sections create a data model:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="f6e13-171">Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="f6e13-172">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="f6e13-172">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

* <span data-ttu-id="f6e13-174">Crie uma pasta *Modelos* na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="f6e13-175">Crie *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="f6e13-176">A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="f6e13-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="f6e13-177">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f6e13-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f6e13-178">Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="f6e13-179">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f6e13-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f6e13-180">As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="f6e13-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="f6e13-181">Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="f6e13-182">Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro.</span><span class="sxs-lookup"><span data-stu-id="f6e13-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="f6e13-183">No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="f6e13-184">Por exemplo, suponha que uma linha de aluno tenha ID=1.</span><span class="sxs-lookup"><span data-stu-id="f6e13-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="f6e13-185">As linhas de registro relacionadas terão StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="f6e13-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="f6e13-186">StudentID é uma *chave estrangeira* na tabela de Registro.</span><span class="sxs-lookup"><span data-stu-id="f6e13-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="f6e13-187">A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas.</span><span class="sxs-lookup"><span data-stu-id="f6e13-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="f6e13-188">Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="f6e13-189">Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="f6e13-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="f6e13-190">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="f6e13-190">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="f6e13-192">Crie *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="f6e13-193">A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="f6e13-194">Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente.</span><span class="sxs-lookup"><span data-stu-id="f6e13-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="f6e13-195">Este tutorial usa ambos apenas para ilustrar que os dois funcionam.</span><span class="sxs-lookup"><span data-stu-id="f6e13-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="f6e13-196">Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="f6e13-197">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f6e13-198">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="f6e13-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="f6e13-199">Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="f6e13-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f6e13-200">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f6e13-201">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="f6e13-202">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f6e13-203">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f6e13-204">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f6e13-205">Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f6e13-206">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f6e13-207">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="f6e13-208">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="f6e13-208">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="f6e13-210">Crie *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="f6e13-211">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="f6e13-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f6e13-212">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f6e13-213">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="f6e13-214">Compile o projeto para validar que não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="f6e13-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="f6e13-215">Aplicar scaffold a páginas de Aluno</span><span class="sxs-lookup"><span data-stu-id="f6e13-215">Scaffold Student pages</span></span>

<span data-ttu-id="f6e13-216">Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:</span><span class="sxs-lookup"><span data-stu-id="f6e13-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="f6e13-217">Uma classe *contexto* do EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6e13-217">An EF Core *context* class.</span></span> <span data-ttu-id="f6e13-218">O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="f6e13-219">Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="f6e13-220">Páginas do Razor que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-222">Crie uma pasta *Alunos* na pasta *Páginas*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="f6e13-223">Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta *páginas/alunos* e selecione **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f6e13-224">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="f6e13-225">Na caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6e13-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="f6e13-226">Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="f6e13-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="f6e13-227">Na linha **Classe de contexto de dados**, selecione o sinal de **+** (adição).</span><span class="sxs-lookup"><span data-stu-id="f6e13-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="f6e13-228">Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="f6e13-229">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-229">Select **Add**.</span></span>

<span data-ttu-id="f6e13-230">Os seguintes pacotes são instalados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="f6e13-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e13-232">Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:</span><span class="sxs-lookup"><span data-stu-id="f6e13-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="f6e13-233">O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding.</span><span class="sxs-lookup"><span data-stu-id="f6e13-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="f6e13-234">Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f6e13-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="f6e13-235">Crie uma pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="f6e13-236">Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="f6e13-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="f6e13-237">Execute o comando a seguir para aplicar scaffold às páginas do aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="f6e13-238">**No Windows**</span><span class="sxs-lookup"><span data-stu-id="f6e13-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="f6e13-239">**No macOS ou no Linux**</span><span class="sxs-lookup"><span data-stu-id="f6e13-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="f6e13-240">Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.</span><span class="sxs-lookup"><span data-stu-id="f6e13-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="f6e13-241">O processo de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="f6e13-241">The scaffolding process:</span></span>

* <span data-ttu-id="f6e13-242">Cria páginas do Razor na pasta *Pages/Students:*</span><span class="sxs-lookup"><span data-stu-id="f6e13-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="f6e13-243">*Create.cshtml* e *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="f6e13-244">*Delete.cshtml* e *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="f6e13-245">*Details.cshtml* e *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="f6e13-246">*Edit.cshtml* e *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="f6e13-247">*Index.cshtml* e *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="f6e13-248">Cria *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="f6e13-249">Adiciona o contexto à injeção de dependência em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="f6e13-250">Adiciona uma cadeia de conexão de banco de dados a *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="f6e13-251">Cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e13-253">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="f6e13-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="f6e13-254">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="f6e13-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f6e13-255">Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e13-257">Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="f6e13-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="f6e13-258">Atualizar a classe do contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-258">Update the database context class</span></span>

<span data-ttu-id="f6e13-259">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="f6e13-260">O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f6e13-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f6e13-261">O contexto especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f6e13-262">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f6e13-263">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="f6e13-264">O código destacado cria uma propriedade [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f6e13-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f6e13-265">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="f6e13-265">In EF Core terminology:</span></span>

* <span data-ttu-id="f6e13-266">Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="f6e13-267">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="f6e13-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f6e13-268">Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural.</span><span class="sxs-lookup"><span data-stu-id="f6e13-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="f6e13-269">Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural.</span><span class="sxs-lookup"><span data-stu-id="f6e13-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="f6e13-270">Para fazer com que o código do Razor Pages corresponda ao novo nome do DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="f6e13-271">Há oito ocorrências.</span><span class="sxs-lookup"><span data-stu-id="f6e13-271">There are 8 occurrences.</span></span>

<span data-ttu-id="f6e13-272">Compile o projeto para verificar se não há erros de compilador.</span><span class="sxs-lookup"><span data-stu-id="f6e13-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="f6e13-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f6e13-273">Startup.cs</span></span>

<span data-ttu-id="f6e13-274">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6e13-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f6e13-275">Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f6e13-276">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="f6e13-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f6e13-277">O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f6e13-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f6e13-278">A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="f6e13-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-280">Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="f6e13-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e13-282">Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="f6e13-283">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="f6e13-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f6e13-284">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f6e13-285">Criar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-285">Create the database</span></span>

<span data-ttu-id="f6e13-286">Atualize *Program.cs* para criar o banco de dados se ele não existir:</span><span class="sxs-lookup"><span data-stu-id="f6e13-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="f6e13-287">O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="f6e13-288">Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema.</span><span class="sxs-lookup"><span data-stu-id="f6e13-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="f6e13-289">`EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="f6e13-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="f6e13-290">Exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-290">Delete the database.</span></span> <span data-ttu-id="f6e13-291">Qualquer dado existente é perdido.</span><span class="sxs-lookup"><span data-stu-id="f6e13-291">Any existing data is lost.</span></span>
* <span data-ttu-id="f6e13-292">Altere o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-292">Change the data model.</span></span> <span data-ttu-id="f6e13-293">Por exemplo, adicione um campo `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="f6e13-294">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-294">Run the app.</span></span>
* <span data-ttu-id="f6e13-295">`EnsureCreated` cria um banco de dados com o novo esquema.</span><span class="sxs-lookup"><span data-stu-id="f6e13-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="f6e13-296">Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="f6e13-297">A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="f6e13-298">Quando esse for o caso, use migrações.</span><span class="sxs-lookup"><span data-stu-id="f6e13-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="f6e13-299">Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso.</span><span class="sxs-lookup"><span data-stu-id="f6e13-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="f6e13-300">Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.</span><span class="sxs-lookup"><span data-stu-id="f6e13-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f6e13-301">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6e13-301">Test the app</span></span>

* <span data-ttu-id="f6e13-302">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-302">Run the app.</span></span>
* <span data-ttu-id="f6e13-303">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f6e13-304">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="f6e13-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="f6e13-305">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-305">Seed the database</span></span>

<span data-ttu-id="f6e13-306">O método `EnsureCreated` cria um banco de dados vazio.</span><span class="sxs-lookup"><span data-stu-id="f6e13-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="f6e13-307">Esta seção adiciona um código que preenche o banco de dados com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="f6e13-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="f6e13-308">Crie *Data/DbInitializer.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="f6e13-309">O código verifica se há alunos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="f6e13-310">Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="f6e13-311">Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="f6e13-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="f6e13-312">Em *Program.cs*, substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:</span><span class="sxs-lookup"><span data-stu-id="f6e13-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e13-314">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="f6e13-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e13-316">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="f6e13-317">Reinicie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-317">Restart the app.</span></span>

* <span data-ttu-id="f6e13-318">Selecione a página Alunos para ver os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="f6e13-319">Exibir o banco de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-321">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f6e13-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="f6e13-322">No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="f6e13-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="f6e13-323">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="f6e13-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="f6e13-324">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="f6e13-325">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="f6e13-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="f6e13-326">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e13-328">Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="f6e13-329">O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="f6e13-330">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="f6e13-330">Asynchronous code</span></span>

<span data-ttu-id="f6e13-331">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6e13-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f6e13-332">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="f6e13-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f6e13-333">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f6e13-334">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="f6e13-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f6e13-335">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="f6e13-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f6e13-336">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="f6e13-337">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f6e13-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f6e13-338">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="f6e13-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f6e13-339">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="f6e13-340">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="f6e13-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f6e13-341">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="f6e13-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f6e13-342">Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="f6e13-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="f6e13-343">O tipo retornado `Task<T>` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="f6e13-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="f6e13-344">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="f6e13-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f6e13-345">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f6e13-346">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="f6e13-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f6e13-347">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f6e13-348">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="f6e13-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f6e13-349">Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="f6e13-350">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f6e13-351">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f6e13-352">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f6e13-353">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="f6e13-354">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f6e13-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f6e13-355">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f6e13-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f6e13-356">Próximo tutorial</span><span class="sxs-lookup"><span data-stu-id="f6e13-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6e13-357">O aplicativo Web de exemplo Contoso University demonstra como criar um aplicativo Razor Pages do ASP.NET Core usando o EF (Entity Framework) Core.</span><span class="sxs-lookup"><span data-stu-id="f6e13-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="f6e13-358">O aplicativo de exemplo é um site de uma Contoso University fictícia.</span><span class="sxs-lookup"><span data-stu-id="f6e13-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f6e13-359">Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor.</span><span class="sxs-lookup"><span data-stu-id="f6e13-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f6e13-360">Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f6e13-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="f6e13-361">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="f6e13-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f6e13-362">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f6e13-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f6e13-363">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="f6e13-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="f6e13-366">Familiaridade com as [Páginas do Razor](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="f6e13-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="f6e13-367">Os novos programadores devem concluir a [Introdução às Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start) antes de começar esta série.</span><span class="sxs-lookup"><span data-stu-id="f6e13-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f6e13-368">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="f6e13-368">Troubleshooting</span></span>

<span data-ttu-id="f6e13-369">Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f6e13-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f6e13-370">Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="f6e13-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="f6e13-371">O aplicativo Web Contoso University</span><span class="sxs-lookup"><span data-stu-id="f6e13-371">The Contoso University web app</span></span>

<span data-ttu-id="f6e13-372">O aplicativo criado nesses tutoriais é um site básico de universidade.</span><span class="sxs-lookup"><span data-stu-id="f6e13-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="f6e13-373">Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores.</span><span class="sxs-lookup"><span data-stu-id="f6e13-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f6e13-374">Veja a seguir algumas das telas criadas no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f6e13-374">Here are a few of the screens created in the tutorial.</span></span>

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

<span data-ttu-id="f6e13-377">O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="f6e13-378">O foco do tutorial é o EF Core com as Páginas do Razor, não a interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="f6e13-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="f6e13-379">Criar o aplicativo Web Razor Pages da ContosoUniversity</span><span class="sxs-lookup"><span data-stu-id="f6e13-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-381">No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="f6e13-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f6e13-382">Crie um novo Aplicativo Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6e13-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="f6e13-383">Nomeie o projeto **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="f6e13-384">É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.</span><span class="sxs-lookup"><span data-stu-id="f6e13-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="f6e13-385">Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="f6e13-386">Para ver imagens das etapas anteriores, confira [Criar um aplicativo Web do Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="f6e13-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="f6e13-387">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f6e13-389">Configurar o estilo do site</span><span class="sxs-lookup"><span data-stu-id="f6e13-389">Set up the site style</span></span>

<span data-ttu-id="f6e13-390">Algumas alterações configuram o menu do site, o layout e a home page.</span><span class="sxs-lookup"><span data-stu-id="f6e13-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="f6e13-391">Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="f6e13-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="f6e13-392">Altere cada ocorrência de "ContosoUniversity" para "Contoso University".</span><span class="sxs-lookup"><span data-stu-id="f6e13-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f6e13-393">Há três ocorrências.</span><span class="sxs-lookup"><span data-stu-id="f6e13-393">There are three occurrences.</span></span>

* <span data-ttu-id="f6e13-394">Adicione entradas de menu para **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="f6e13-395">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="f6e13-395">The changes are highlighted.</span></span> <span data-ttu-id="f6e13-396">(Toda a marcação *não* é exibida.)</span><span class="sxs-lookup"><span data-stu-id="f6e13-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="f6e13-397">Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:</span><span class="sxs-lookup"><span data-stu-id="f6e13-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="f6e13-398">Criar o modelo de dados</span><span class="sxs-lookup"><span data-stu-id="f6e13-398">Create the data model</span></span>

<span data-ttu-id="f6e13-399">Crie classes de entidade para o aplicativo Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f6e13-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="f6e13-400">Comece com as três seguintes entidades:</span><span class="sxs-lookup"><span data-stu-id="f6e13-400">Start with the following three entities:</span></span>

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

<span data-ttu-id="f6e13-402">Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="f6e13-403">Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="f6e13-404">Um aluno pode se registrar em qualquer quantidade de cursos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="f6e13-405">Um curso pode ter qualquer quantidade de alunos registrados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="f6e13-406">Nas seções a seguir, é criada uma classe para cada uma dessas entidades.</span><span class="sxs-lookup"><span data-stu-id="f6e13-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="f6e13-407">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="f6e13-407">The Student entity</span></span>

![Diagrama da entidade Student](intro/_static/student-entity.png)

<span data-ttu-id="f6e13-409">Crie uma pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-409">Create a *Models* folder.</span></span> <span data-ttu-id="f6e13-410">Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="f6e13-411">A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe.</span><span class="sxs-lookup"><span data-stu-id="f6e13-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="f6e13-412">Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f6e13-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f6e13-413">Em `classnameID`, `classname` é o nome da classe.</span><span class="sxs-lookup"><span data-stu-id="f6e13-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="f6e13-414">A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="f6e13-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="f6e13-415">A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f6e13-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f6e13-416">As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade.</span><span class="sxs-lookup"><span data-stu-id="f6e13-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="f6e13-417">Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="f6e13-418">Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="f6e13-419">Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="f6e13-420">Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="f6e13-421">A tabela `Enrollment` tem duas linhas com `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="f6e13-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="f6e13-422">`StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="f6e13-423">Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="f6e13-424">`ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="f6e13-425">Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="f6e13-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="f6e13-426">As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="f6e13-427">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="f6e13-427">The Enrollment entity</span></span>

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

<span data-ttu-id="f6e13-429">Na pasta *Models*, crie *Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="f6e13-430">A propriedade `EnrollmentID` é a chave primária.</span><span class="sxs-lookup"><span data-stu-id="f6e13-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="f6e13-431">Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="f6e13-432">Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="f6e13-433">Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="f6e13-434">A propriedade `Grade` é um `enum`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f6e13-435">O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="f6e13-436">Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.</span><span class="sxs-lookup"><span data-stu-id="f6e13-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f6e13-437">A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f6e13-438">Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="f6e13-439">A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="f6e13-440">A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f6e13-441">Uma entidade `Enrollment` está associada a uma entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f6e13-442">O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f6e13-443">Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f6e13-444">Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f6e13-445">Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="f6e13-446">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="f6e13-446">The Course entity</span></span>

![Diagrama de entidade Curso](intro/_static/course-entity.png)

<span data-ttu-id="f6e13-448">Na pasta *Models*, crie *Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="f6e13-449">A propriedade `Enrollments` é uma propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="f6e13-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f6e13-450">Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f6e13-451">O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="f6e13-452">Gere um modelo de aluno por scaffold</span><span class="sxs-lookup"><span data-stu-id="f6e13-452">Scaffold the student model</span></span>

<span data-ttu-id="f6e13-453">Nesta seção, é feito o scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="f6e13-454">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="f6e13-455">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="f6e13-455">Build the project.</span></span>
* <span data-ttu-id="f6e13-456">Crie a pasta *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f6e13-458">Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta *páginas/alunos* > **Adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f6e13-459">Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="f6e13-460">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="f6e13-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f6e13-461">Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)** .</span><span class="sxs-lookup"><span data-stu-id="f6e13-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="f6e13-462">Na linha **Classe de contexto de dados**, selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="f6e13-463">Na lista suspensa **Classe de contexto de dados**, selecione **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="f6e13-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="f6e13-464">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-464">Select **Add**.</span></span>

![Caixa de diálogo CRUD](intro/_static/s1.png)

<span data-ttu-id="f6e13-466">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="f6e13-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f6e13-468">Execute os comandos a seguir para fazer scaffold do modelo de aluno.</span><span class="sxs-lookup"><span data-stu-id="f6e13-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="f6e13-469">O processo de scaffold criou e alterou os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="f6e13-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f6e13-470">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="f6e13-470">Files created</span></span>

* <span data-ttu-id="f6e13-471">*Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="f6e13-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="f6e13-472">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f6e13-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="f6e13-473">Atualizações de arquivo</span><span class="sxs-lookup"><span data-stu-id="f6e13-473">File updates</span></span>

* <span data-ttu-id="f6e13-474">*Startup.cs*: alterações a esse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="f6e13-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="f6e13-475">*appsettings.json*: a cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="f6e13-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f6e13-476">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="f6e13-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f6e13-477">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f6e13-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f6e13-478">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f6e13-479">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="f6e13-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f6e13-480">O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="f6e13-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f6e13-481">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="f6e13-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f6e13-482">Examine o método `ConfigureServices` em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="f6e13-483">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="f6e13-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="f6e13-484">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="f6e13-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f6e13-485">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="f6e13-486">Atualizar o principal</span><span class="sxs-lookup"><span data-stu-id="f6e13-486">Update main</span></span>

<span data-ttu-id="f6e13-487">Em *Program.cs*, modifique o método `Main` para fazer o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f6e13-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f6e13-488">Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="f6e13-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f6e13-489">Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="f6e13-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="f6e13-490">Descarte o contexto quando o método `EnsureCreated` for concluído.</span><span class="sxs-lookup"><span data-stu-id="f6e13-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="f6e13-491">O código a seguir mostra o arquivo *Program.cs* atualizado.</span><span class="sxs-lookup"><span data-stu-id="f6e13-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="f6e13-492">`EnsureCreated` garante que o banco de dados do contexto exista.</span><span class="sxs-lookup"><span data-stu-id="f6e13-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="f6e13-493">Se ele existir, nenhuma ação será realizada.</span><span class="sxs-lookup"><span data-stu-id="f6e13-493">If it exists, no action is taken.</span></span> <span data-ttu-id="f6e13-494">Se ele não existir, o banco de dados e todos os seus esquemas serão criados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="f6e13-495">`EnsureCreated` não usa migrações para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="f6e13-496">Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.</span><span class="sxs-lookup"><span data-stu-id="f6e13-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="f6e13-497">`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:</span><span class="sxs-lookup"><span data-stu-id="f6e13-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="f6e13-498">Exclua o BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-498">Delete the DB.</span></span>
* <span data-ttu-id="f6e13-499">Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).</span><span class="sxs-lookup"><span data-stu-id="f6e13-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="f6e13-500">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-500">Run the app.</span></span>
* <span data-ttu-id="f6e13-501">`EnsureCreated` cria um BD com a coluna `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="f6e13-502">`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente.</span><span class="sxs-lookup"><span data-stu-id="f6e13-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="f6e13-503">Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="f6e13-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f6e13-504">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="f6e13-504">Test the app</span></span>

<span data-ttu-id="f6e13-505">Execute o aplicativo e aceite a política de cookies.</span><span class="sxs-lookup"><span data-stu-id="f6e13-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="f6e13-506">Este aplicativo não armazena informações pessoais.</span><span class="sxs-lookup"><span data-stu-id="f6e13-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="f6e13-507">Você pode ler sobre a política de cookies em [Suporte para a RGPD (Regulamento Geral sobre a Proteção de Dados) da UE](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f6e13-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="f6e13-508">Selecione o link **Alunos** e **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f6e13-509">Teste os links Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="f6e13-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="f6e13-510">Examine o contexto de BD SchoolContext</span><span class="sxs-lookup"><span data-stu-id="f6e13-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="f6e13-511">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="f6e13-512">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f6e13-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f6e13-513">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f6e13-514">Neste projeto, a classe é chamada `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f6e13-515">Atualize *SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="f6e13-516">O código destacado cria uma propriedade [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="f6e13-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f6e13-517">Na terminologia do EF Core:</span><span class="sxs-lookup"><span data-stu-id="f6e13-517">In EF Core terminology:</span></span>

* <span data-ttu-id="f6e13-518">Um conjunto de entidades normalmente corresponde a uma tabela de BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="f6e13-519">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="f6e13-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f6e13-520">`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="f6e13-521">O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="f6e13-522">Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="f6e13-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f6e13-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f6e13-524">A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="f6e13-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="f6e13-525">LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção.</span><span class="sxs-lookup"><span data-stu-id="f6e13-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f6e13-526">O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa.</span><span class="sxs-lookup"><span data-stu-id="f6e13-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f6e13-527">Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="f6e13-528">Adicionar um código para inicializar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="f6e13-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="f6e13-529">O EF Core cria um BD vazio.</span><span class="sxs-lookup"><span data-stu-id="f6e13-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="f6e13-530">Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="f6e13-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="f6e13-531">Na pasta *Dados*, crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f6e13-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="f6e13-532">Observação: o código anterior usa `Models` para o namespace (`namespace ContosoUniversity.Models`) em vez de `Data`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="f6e13-533">`Models` é consistente com o código gerado pelo scaffolder.</span><span class="sxs-lookup"><span data-stu-id="f6e13-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="f6e13-534">Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="f6e13-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="f6e13-535">O código verifica se há alunos no BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="f6e13-536">Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="f6e13-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="f6e13-537">Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="f6e13-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="f6e13-538">O método `EnsureCreated` cria o BD automaticamente para o contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="f6e13-539">Se o BD existir, `EnsureCreated` retornará sem modificar o BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="f6e13-540">Em *Program.cs*, modifique o método `Main` para chamar `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="f6e13-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="f6e13-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f6e13-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f6e13-542">Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="f6e13-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f6e13-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f6e13-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f6e13-544">Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f6e13-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="f6e13-545">Exibir o BD</span><span class="sxs-lookup"><span data-stu-id="f6e13-545">View the DB</span></span>

<span data-ttu-id="f6e13-546">O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.</span><span class="sxs-lookup"><span data-stu-id="f6e13-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="f6e13-547">Assim, o nome do banco de dados será "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="f6e13-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="f6e13-548">O GUID será diferente para cada usuário.</span><span class="sxs-lookup"><span data-stu-id="f6e13-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="f6e13-549">Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f6e13-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="f6e13-550">No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="f6e13-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="f6e13-551">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="f6e13-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="f6e13-552">Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.</span><span class="sxs-lookup"><span data-stu-id="f6e13-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="f6e13-553">Código assíncrono</span><span class="sxs-lookup"><span data-stu-id="f6e13-553">Asynchronous code</span></span>

<span data-ttu-id="f6e13-554">A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.</span><span class="sxs-lookup"><span data-stu-id="f6e13-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f6e13-555">Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso.</span><span class="sxs-lookup"><span data-stu-id="f6e13-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f6e13-556">Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados.</span><span class="sxs-lookup"><span data-stu-id="f6e13-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f6e13-557">Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S.</span><span class="sxs-lookup"><span data-stu-id="f6e13-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f6e13-558">Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações.</span><span class="sxs-lookup"><span data-stu-id="f6e13-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f6e13-559">Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.</span><span class="sxs-lookup"><span data-stu-id="f6e13-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="f6e13-560">O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="f6e13-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f6e13-561">Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.</span><span class="sxs-lookup"><span data-stu-id="f6e13-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f6e13-562">No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="f6e13-563">A palavra-chave `async` instrui o compilador a:</span><span class="sxs-lookup"><span data-stu-id="f6e13-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f6e13-564">Gerar retornos de chamada para partes do corpo do método.</span><span class="sxs-lookup"><span data-stu-id="f6e13-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f6e13-565">Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado.</span><span class="sxs-lookup"><span data-stu-id="f6e13-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="f6e13-566">Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="f6e13-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="f6e13-567">O tipo de retorno implícito `Task` representa um trabalho em andamento.</span><span class="sxs-lookup"><span data-stu-id="f6e13-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="f6e13-568">A palavra-chave `await` faz com que o compilador divida o método em duas partes.</span><span class="sxs-lookup"><span data-stu-id="f6e13-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f6e13-569">A primeira parte termina com a operação que é iniciada de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f6e13-570">A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="f6e13-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f6e13-571">`ToListAsync` é a versão assíncrona do método de extensão `ToList`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f6e13-572">Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:</span><span class="sxs-lookup"><span data-stu-id="f6e13-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f6e13-573">Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="f6e13-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="f6e13-574">Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f6e13-575">Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span><span class="sxs-lookup"><span data-stu-id="f6e13-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f6e13-576">Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.</span><span class="sxs-lookup"><span data-stu-id="f6e13-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f6e13-577">Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="f6e13-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="f6e13-578">Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f6e13-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="f6e13-579">No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.</span><span class="sxs-lookup"><span data-stu-id="f6e13-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="f6e13-580">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f6e13-580">Additional resources</span></span>

* [<span data-ttu-id="f6e13-581">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="f6e13-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="f6e13-582">Próximo</span><span class="sxs-lookup"><span data-stu-id="f6e13-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
